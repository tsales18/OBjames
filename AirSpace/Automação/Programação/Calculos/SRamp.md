(*
Siemens TIA Portal - SCL (Structured Control Language / Structured Text)
Corpo do FB SRamp.

Interface esperada:
    Data : UDT_SRamp;   // VAR_IN_OUT

A rotina auxiliar global "RRoot" deve existir no projeto.
*)

(*
Indexação da sub-rotina
*)

(*
Leitura dos parâmetros da rampa de acordo com a rampa ativa
*)

(*
function speed = rspeed3(x0, x1, x2, x3, y0, y1, y3, smooth_start, smooth_stop, current_position)
-------------------------------------------------------------------------------
function speed = rspeed3(x0, x1, x2, x3, y0, y1, y3, smooth_start, smooth_stop, current_position)
Cálculo da velocidade em função da posição
Esta é a função a ser utilizada on-line durante o controle
do motor
Entradas:
    x0 = posição inicial
    x1 = distância/espaço reservado para a aceleração
    x2 = distância/espaço reservado para a desaceleração antes do target
    x3 = posição final (velocidade mínima)
    y0 = velocidade inicial
    y1 = velocidade máxima
    y3 = velocidade final
    smooth_start = Curvatura do perfil de aceleração (1..100): 1-->reta, 100-->duas parábolas
    smooth_stop = Curvatura do perfil de desaceleração (1..100): 1-->reta, 100-->duas parábolas
    current_position = posição na qual a velocidade deve ser calculada
     o valor deste parâmetro usa as mesmas unidades de medida de x0, x1, x2 e x3
Saídas:
    valor da velocidade
-------------------------------------------------------------------------------

0 --parábola--> t1 --reta--> t2 --parábola--> t3 --velocidade constante--> t4
    --parábola--> t5 --reta--> t6 --parábola--> t7


    tempo   velocidade  posição 
     0    y0        x0
     t1         x01
     t2         x02
     t3   y1        x1  velocidade máxima
     t4   y1        x2  velocidade máxima
     t5         x21
     t6         x22
     t7   y2        x
*)

(*
CAMADA OPCIONAL DE CORREÇÃO FINAL DE POSIÇÃO

Esta camada atua somente próximo ao target e NÃO substitui o perfil S principal.
Quando habilitada, ela reduz a referência de velocidade proporcionalmente ao erro
de posição na janela final e zera a velocidade dentro da tolerância.

Novos membros necessários na estrutura Data:
  PosCorrEnable   : BOOL  -> habilita a correção final
  PosCorrWindow   : REAL  -> distância antes do target onde a correção pode atuar
  PosCorrKp       : REAL  -> ganho posição->velocidade (unid. velocidade / unid. posição)
  PosCorrMinSpeed : REAL  -> menor velocidade de aproximação fora da tolerância
  PosTolerance    : REAL  -> tolerância de posição para considerar o eixo no target
  PosError        : REAL  -> erro de posição normalizado
  PosCorrSpeed    : REAL  -> velocidade calculada pela correção
  AtTarget        : BOOL  -> posição dentro da tolerância
  PosOvershoot    : BOOL  -> target ultrapassado além da tolerância

IMPORTANTE:
- PosCorrEnable = 0 mantém o comportamento original do SRamp.
- O cálculo de BWD já espelha current_position; por isso PosError é calculado
  no sistema normalizado e a mesma lógica funciona para FWD e BWD.
- Esta camada NÃO inverte automaticamente o sentido após overshoot.
  Em caso de ultrapassagem, Speed vai para zero e PosOvershoot é sinalizado.
*)


// t0 é a origem temporal da primeira curva.
#Data.t0 := 0.0;

// Sem comando de avanço/recuo, sai da rotina

IF NOT #Data.BWD AND NOT #Data.FWD THEN
    #Data.ONS_0 := FALSE;
    #Data.ONS_1 := FALSE;
    #Data.ONS_2 := FALSE;
    #Data.ONS_3 := FALSE;
    #Data.ONS_4 := FALSE;
    #Data.done := FALSE;
    #Data.AccOn := FALSE;
    #Data.DecOn := FALSE;
    #Data.Speed := 0.0;
ELSE
    
    
    (*
    Se o sistema estiver em desaceleração e, por algum motivo, o target mudar (
    ficando maior que o atual), é necessário recalcular a posição inicial
    para evitar que a velocidade mude bruscamente do valor atual para o valor 
    máximo. 
    *)
    
    IF #Data.TARGET <> #Data.COPY_TARGET AND ABS(#Data.TARGET - #Data.COPY_TARGET) > 2.0 OR #Data.RECALCULATE THEN
        #Data.DummyR := ABS(#Data.TARGET - #Data.COPY_TARGET);
        IF (#Data.X3_Pos - #Data.POSITION) < #Data.DummyR AND #Data.DecOn OR #Data.RECALCULATE THEN
            #Data.CopyX0 := #Data.POSITION;
        END_IF;
        #Data.COPY_TARGET := #Data.TARGET;
        #Data.ONS_0 := FALSE;
        #Data.ONS_3 := FALSE;
        #Data.ONS_4 := FALSE;
        #Data.RECALCULATE := FALSE;
    END_IF;
    
    IF (#Data.FWD AND NOT #Data.BWD AND NOT #Data.ONS_1) OR (#Data.BWD AND NOT #Data.FWD AND NOT #Data.ONS_2) THEN
        #Data.CopyX0 := #Data.POSITION;
        IF #Data.FWD THEN
            #Data.ONS_1 := TRUE;
            #Data.ONS_2 := FALSE;
        END_IF;
        IF #Data.BWD THEN
            #Data.ONS_2 := TRUE;
            #Data.ONS_1 := FALSE;
        END_IF;
    END_IF;
    
    IF (#Data.FWD AND NOT #Data.BWD AND NOT #Data.ONS_3) OR (#Data.BWD AND NOT #Data.FWD AND NOT #Data.ONS_4) THEN
        #Data.X0_Pos := #Data.CopyX0;
        IF #Data.FWD THEN
            #Data.ONS_3 := TRUE;
            #Data.ONS_4 := FALSE;
        END_IF;
        IF #Data.BWD THEN
            #Data.ONS_4 := TRUE;
            #Data.ONS_3 := FALSE;
        END_IF;
        #Data.done := FALSE;
        #Data.Y0equalY1 := FALSE;
        #Data.y0modified := FALSE;
        IF ABS(#Data.SPEED_FEEDBACK) > #Data.Y0 AND ((#Data.SPEED_FEEDBACK > 0.0 AND #Data.FWD) OR (#Data.SPEED_FEEDBACK < 0.0 AND #Data.BWD)) AND NOT #Data.ONS_0 THEN
            IF ABS(#Data.SPEED_FEEDBACK) > #Data.Y1 THEN
                #Data.Y0 := #Data.Y1;
            ELSE
                #Data.Y0 := ABS(#Data.SPEED_FEEDBACK);
            END_IF;
            #Data.y0modified := TRUE;
            IF #Data.Y0 = #Data.Y1 THEN
                #Data.Y0equalY1 := TRUE;
            END_IF;
        END_IF;
        #Data.ONS_0 := TRUE;
        #Data.X3_Pos := #Data.TARGET;
    END_IF;
    
    // PRIMEIRA VARREDURA
    IF NOT #Data.done THEN
        #Data.X1_Dist := #Data.X1;
        #Data.X2_Dist := #Data.X2;
        
        // LIMITES MÁXIMOS DOS VALORES DA RAMPA
        IF #Data.X1_Dist > #Data.MAX_X1 THEN
            #Data.X1_Dist := #Data.MAX_X1;
        END_IF;
        IF #Data.X2_Dist > #Data.MAX_X2 THEN
            #Data.X2_Dist := #Data.MAX_X2;
        END_IF;
        IF #Data.Y0 > #Data.MAX_Y0 THEN
            #Data.Y0 := #Data.MAX_Y0;
        END_IF;
        IF #Data.Y1 > #Data.MAX_Y1 THEN
            #Data.Y1 := #Data.MAX_Y1;
        END_IF;
        IF #Data.Y3 > #Data.MAX_Y3 THEN
            #Data.Y3 := #Data.MAX_Y3;
        END_IF;
        // LIMITES MÍNIMOS DOS VALORES DA RAMPA
        IF #Data.X1_Dist < #Data.MIN_X1 THEN
            #Data.X1_Dist := #Data.MIN_X1;
        END_IF;
        IF #Data.X2_Dist < #Data.MIN_X2 THEN
            #Data.X2_Dist := #Data.MIN_X2;
        END_IF;
        IF #Data.Y0 < #Data.MIN_Y0 THEN
            #Data.Y0 := #Data.MIN_Y0;
        END_IF;
        IF #Data.Y1 < #Data.MIN_Y1 THEN
            #Data.Y1 := #Data.MIN_Y1;
        END_IF;
        IF #Data.Y3 < #Data.MIN_Y3 THEN
            #Data.Y3 := #Data.MIN_Y3;
        END_IF;
    END_IF;
    IF NOT #Data.done THEN
        
        //****************************INÍCIO DA NORMALIZAÇÃO DOS PARÂMETROS DE ENTRADA*************************************
        
        IF #Data.BWD THEN
            #Data.X3_Pos := 2 * #Data.X0_Pos - #Data.X3_Pos;
        END_IF;
        
        #Data.diff_target := ABS(#Data.X0_Pos - #Data.X3_Pos);
        
        // LIMITAÇÃO DA ESCALA
        IF #Data.SCALE < 0 THEN
            #Data.SCALE := 0;
        END_IF;
        IF #Data.SCALE > 97 THEN
            #Data.SCALE := 97;
        END_IF;
        
        #Data.ScalePerc := (100.0 - #Data.SCALE) * 0.01;
        
        // IMPORTANTE:
        // X1_Dist, X2_Dist e Y1 são parâmetros de configuração.
        // Não são sobrescritos pela escala. X1_R, X2_R e Y1_R são usados
        // como variáveis de trabalho durante o cálculo da trajetória.
        // Isso evita redução cumulativa quando a trajetória é recalculada.
        #Data.X0_R := #Data.X0_Pos;
        #Data.X1_R := #Data.X1_Dist * #Data.ScalePerc;   // distância de aceleração de trabalho
        #Data.X2_R := #Data.X2_Dist * #Data.ScalePerc;   // distância de desaceleração de trabalho
        #Data.X3_R := #Data.X3_Pos;
        #Data.Y0_R := #Data.Y0;
        #Data.Y1_R := #Data.Y1 * #Data.ScalePerc;        // velocidade máxima de trabalho
        #Data.Y3_R := #Data.Y3;
        
        // Se a soma das distâncias de aceleração e desaceleração não couber
        // no deslocamento total, reduz as duas proporcionalmente.
        IF #Data.X1_R + #Data.X2_R > #Data.diff_target THEN
            #Data.a_perc := #Data.X1_R / (#Data.X1_R + #Data.X2_R);
            #Data.b_perc := 1.0 - #Data.a_perc;
            #Data.Kv := #Data.diff_target / (#Data.X1_R + #Data.X2_R);
            #Data.X1_R := #Data.diff_target * #Data.a_perc;
            #Data.X2_R := #Data.diff_target * #Data.b_perc;
            
            IF NOT #Data.Y0equalY1 THEN
                // Reduz a velocidade máxima proporcionalmente ao espaço disponível.
                #Data.Y1_R := #Data.Kv * #Data.Y1_R;
                IF #Data.y0modified AND #Data.Y0 > #Data.Y1_R THEN
                    // Se a velocidade inicial ficou maior que a Vmax reduzida,
                    // força Vmax reduzida = velocidade inicial.
                    #Data.Y1_R := #Data.Y0;
                END_IF;
            ELSE
                // Se velocidade inicial = velocidade máxima, mantém a distância
                // de desaceleração e usa o restante para a aceleração.
                #Data.X1_R := #Data.diff_target - #Data.X2_R;
                IF #Data.X1_R < 0.0 THEN
                    #Data.X1_R := 10.0;
                    #Data.X2_R := #Data.diff_target - 10.0;
                END_IF;
            END_IF;
            
            IF (#Data.Y1_R <= #Data.Y3) OR (#Data.Y1_R < #Data.Y0) THEN
                IF #Data.Y3 < #Data.Y0 THEN
                    #Data.Y1_R := 1.0 + #Data.Y3;
                    #Data.Y0_R := #Data.Y3_R;
                ELSE
                    #Data.Y1_R := 1.0 + #Data.Y0;
                    #Data.Y3_R := #Data.Y0_R;
                END_IF;
            END_IF;
        END_IF;
        
        // Converte as distâncias de trabalho em posições absolutas do perfil.
        #Data.X1_R := #Data.X0_Pos + #Data.X1_R;
        #Data.X2_R := #Data.X3_Pos - #Data.X2_R;
        // LIMITAÇÃO DOS PARÂMETROS DE SUAVIZAÇÃO
        IF #Data.SMOOTH_START < 1 THEN
            #Data.SMOOTH_START := 1;
        END_IF;
        IF #Data.SMOOTH_START > 100 THEN
            #Data.SMOOTH_START := 100;
        END_IF;
        IF #Data.SMOOTH_STOP < 1 THEN
            #Data.SMOOTH_STOP := 1;
        END_IF;
        IF #Data.SMOOTH_STOP > 100 THEN
            #Data.SMOOTH_STOP := 100;
        END_IF;
        //****************************FIM DA NORMALIZAÇÃO DOS PARÂMETROS DE ENTRADA***************************************
        (*
tempo necessário para atingir a velocidade máxima
a integral da velocidade entre t0=0 e t3 deve ser igual ao espaço x1-x0
A função v(t) ainda não é conhecida, pois é composta por duas parábolas e uma reta que ainda precisam ser
determinadas. Esta função composta v(t) tem a mesma integral de uma reta que passa
pelos pontos (t0,y0) e (t3,y1). A integral é a mesma por simetria.
Daqui resulta que o tempo t3 necessário para atingir a velocidade máxima é
t3 = (2*(x1-x0))/(y0+y1);
*)
        #Data.t3 := 2 * (#Data.X1_R - #Data.X0_Pos) / (#Data.Y0_R + #Data.Y1_R);
(*
fim da primeira parábola de aceleração
t1 = (t3/2)*(smooth_start/100);
*)
        #Data.t1 := #Data.t3 / 2 * (#Data.SMOOTH_START / 100);
(*
início da segunda parábola de aceleração
t2 = t3 - t1
*)
        #Data.t2 := #Data.t3 - #Data.t1;
(*
tempo necessário para atingir o ponto de início da desaceleração
t4 = t3  + (x2-x1)/y1;
*)
        #Data.t4 := #Data.t3 + ((#Data.X2_R - #Data.X1_R) / #Data.Y1_R);
(*
tempo necessário para atingir a posição final x3 com velocidade y3
t7 = t4  + 2*(x3-x2)/(y1+y3);
*)
        #Data.t7 := #Data.t4 + (2 * (#Data.X3_R - #Data.X2_R) / (#Data.Y1_R + #Data.Y3_R));
(*
dt_stop =  ((t7+t4)/2 - t4)*(smooth_start/100);
*)
        #Data.dt_stop := ((#Data.t7 + #Data.t4) / 2 - #Data.t4) * (#Data.SMOOTH_STOP / 100);
(*
fim da primeira parábola de desaceleração
t5 = t4 + dt_stop;
*)
        #Data.t5 := #Data.t4 + #Data.dt_stop;
(*
início da segunda parábola de desaceleração
t6 = t7 - dt_stop;
*)
        #Data.t6 := #Data.t7 - #Data.dt_stop;
(*
--------------------------------------------------------------------------------
Cálculo dos coeficientes da primeira parábola
        a1*t^2 + b1*t + c1
A primeira parábola é utilizada no trecho
x0, x0+dx_start
as fórmulas a seguir foram obtidas a partir do seguinte sistema:
y0=a1*x0 + b1*x0 + c1  ---> passagem da parábola pelo ponto (x0,y0)
2*a1*x0+b1 = 0         ---> vértice da parábola em x0
(y0+y1)/2 = m(x0+x1)/2 ---> passagem da reta pelo ponto intermediário da subida
2*a1*(x0+dx_start) + b1 = m ---> tangência entre reta e parábola no ponto de concordância
a1*(x0+dx_start)^2 + b1*(x0+dx_start) + c1 = m*(x0+dx_start) + q 
    --> mesmo valor para a reta e a parábola no ponto de concordância
dessas 5 equações são obtidos a1, b1, c1, m e q
os valores das outras 3 parábolas e da outra reta são obtidos por simetria
--------------------------------------------------------------------------------
num = (y1-y0)/2;
den =  -t1^2 + t1*t3;
*)
        #Data.numacc := (#Data.Y1_R - #Data.Y0_R) / 2;
        #Data.denacc := - (#Data.t1 ** 2) + (#Data.t1 * #Data.t3);
(*
coeficientes da parábola v(t)
a1 = num/den;
b1 = 0;
c1 = y0;
*)
        #Data.a1 := #Data.numacc / #Data.denacc;
        #Data.B1_Coef := 0;
        #Data.c1 := #Data.Y0_R;
(*
-------------------------------------------------
Cálculo dos coeficientes da segunda parábola
        a3*t^2 + b3*t + c3
A segunda parábola é utilizada no trecho
t1-dt_start, t1
-------------------------------------------------
a3 = -a1;
b3 = -2*a3*t3;
c3 = y1 - a3*t3^2 - b3*t3;
*)
        #Data.a3 := - #Data.a1;
        #Data.B3_Coef := -2 * #Data.a3 * #Data.t3;
        #Data.c3 := #Data.Y1_R - (#Data.a3 * (#Data.t3 ** 2)) - (#Data.B3_Coef * #Data.t3);
(*
-------------------------------------------------
Cálculo dos coeficientes da reta
        a2*t + b2
A reta é utilizada no trecho
dt_start, t1-dt_start
-------------------------------------------------
coeficientes da reta v(t)
a2 = 2*a1*t1;
b2 = a1*t1^2 + b1*t1 + c1 - a2*t1;
*)
        #Data.a2 := 2 * #Data.a1 * #Data.t1;
        #Data.B2_Coef := #Data.a1 * (#Data.t1 ** 2) + (#Data.B1_Coef * #Data.t1) + #Data.c1 - (#Data.a2 * #Data.t1);
(*
-------------------------------------------------
Cálculo dos coeficientes da quarta parábola
        a6*t^2 + b6*t + c6
Esta parábola corresponde ao final da desaceleração
Esta parábola é utilizada no trecho
t3-dt_stop, t3
-------------------------------------------------
num = (y1-y3)/2;
den = t7^2 - t6^2 - (t7-t6)*(t7+t4);
*)
        #Data.numdec := (#Data.Y1_R - #Data.Y3_R) / 2;
        #Data.dendec := #Data.t7 ** 2 - (#Data.t6 ** 2) - ((#Data.t7 - #Data.t6) * (#Data.t7 + #Data.t4));
(*
a6 = num/den;
b6 = -2*a6*t7;
c6 = y3 - a6*t7^2 - b6*t7;
*)
        #Data.a6 := #Data.numdec / #Data.dendec;
        #Data.B6_Coef := -2 * #Data.a6 * #Data.t7;
        #Data.c6 := #Data.Y3_R - (#Data.a6 * (#Data.t7 ** 2)) - (#Data.B6_Coef * #Data.t7);
(*
-------------------------------------------------
Cálculo dos coeficientes da reta de desaceleração
        a5*t + b5
-------------------------------------------------
a5 = 2*a6*t6 + b6;
b5 = (y1+y3)/2 - a5*((t7+t4)/2);
*)
        #Data.a5 := 2 * #Data.a6 * #Data.t6 + #Data.B6_Coef;
        #Data.B5_Coef := (#Data.Y1_R + #Data.Y3_R) / 2 - (#Data.a5 * ((#Data.t7 + #Data.t4) / 2));
(*
-------------------------------------------------
Cálculo dos coeficientes da terceira parábola
        a4*t^2 + b4*t + c4
Esta parábola corresponde ao início da desaceleração
Esta parábola é utilizada no trecho
t2,t2+dt_stop
-------------------------------------------------
a4 = -a6;
b4 = -2*a4*t4;
c4 = y1 - a4*t4^2 - b4*t4;
*)
        #Data.a4 := - #Data.a6;
        #Data.B4_Coef := -2 * #Data.a4 * #Data.t4;
        #Data.c4 := #Data.Y1_R - (#Data.a4 * (#Data.t4 ** 2)) - (#Data.B4_Coef * #Data.t4);
(*
-----------------------------------------------------
coeficientes dos polinômios que descrevem s(t)
-----------------------------------------------------
--------------------------------------------
cúbica no intervalo de tempo: 0 ---> t1
              posição: x0 ---> x01
--------------------------------------------
sa1 = a1/3;
sb1 = b1/2;
sc1 = c1;
sd1 = x0;   a posição no tempo 0 vale x0
*)
        #Data.sa1 := #Data.a1 / 3;
        #Data.sb1 := #Data.B1_Coef / 2;
        #Data.sc1 := #Data.c1;
        #Data.sd1 := #Data.X0_R;
        
(*
--------------------------------------------
parábola no intervalo de tempo: t1 ---> t2
                posição: x01 ---> x02
--------------------------------------------
sa2 = (a2/2);
sb2 = b2;
impõe continuidade em t1 entre a parábola e a cúbica
sc2 = sa1*t1^3 + sb1*t1^2 + sc1*t1 + sd1 - sa2*t1^2 - sb2*t1;
*)
        #Data.sa2 := #Data.a2 / 2;
        #Data.sb2 := #Data.B2_Coef;
        #Data.sc2 := #Data.sa1 * (#Data.t1 ** 3) + (#Data.sb1 * (#Data.t1 ** 2)) + (#Data.sc1 * #Data.t1) + #Data.sd1 - (#Data.sa2 * (#Data.t1 ** 2)) - (#Data.sb2 * #Data.t1);
(*
--------------------------------------------
cúbica no intervalo de tempo: t2 ---> t3
             posição: x02 ---> x1
--------------------------------------------
sa3 = a3/3;
sb3 = b3/2;
sc3 = c3;
obtém sd3 impondo s(t3)=x1
sd3 = x1 - sa3*t3^3 - sb3*t3^2 - sc3*t3; 
*)
        #Data.sa3 := #Data.a3 / 3;
        #Data.sb3 := #Data.B3_Coef / 2;
        #Data.sc3 := #Data.c3;
        #Data.sd3 := #Data.X1_R - (#Data.sa3 * (#Data.t3 ** 3)) - (#Data.sb3 * (#Data.t3 ** 2)) - (#Data.sc3 * #Data.t3);
(*
--------------------------------------------
reta no intervalo de tempo: t3 ---> t4
             posição: x1 ---> x2
--------------------------------------------
não é necessário calculá-la

--------------------------------------------
cúbica no intervalo de tempo: t4 ---> t5
             posição: x2 ---> x21
--------------------------------------------
sa4 = a4/3;
sb4 = b4/2;
sc4 = c4;
obtém sd4 impondo s(t4)=x2
sd4 = x2 - sa4*t4^3 - sb4*t4^2 - sc4*t4; 
*)
        #Data.sa4 := #Data.a4 / 3;
        #Data.sb4 := #Data.B4_Coef / 2;
        #Data.sc4 := #Data.c4;
        #Data.sd4 := #Data.X2_R - (#Data.sa4 * (#Data.t4 ** 3)) - (#Data.sb4 * (#Data.t4 ** 2)) - (#Data.sc4 * #Data.t4);
(*
--------------------------------------------
parábola no intervalo de tempo: t5 ---> t6
                posição: x21 ---> x22
--------------------------------------------
sa5 = (a5/2);
sb5 = b5;
impõe continuidade em t5 entre a parábola e a cúbica
sc5 = sa4*t5^3 + sb4*t5^2 + sc4*t5 + sd4 - sa5*t5^2 - sb5*t5;
*)
        #Data.sa5 := #Data.a5 / 2;
        #Data.sb5 := #Data.B5_Coef;
        #Data.sc5 := #Data.sa4 * (#Data.t5 ** 3) + (#Data.sb4 * (#Data.t5 ** 2)) + (#Data.sc4 * #Data.t5) + #Data.sd4 - (#Data.sa5 * (#Data.t5 ** 2)) - (#Data.sb5 * #Data.t5);
(*
--------------------------------------------
cúbica no intervalo de tempo: t6 ---> t7
             posição: x22 ---> x3
--------------------------------------------
sa6 = a6/3;
sb6 = b6/2;
sc6 = c6;
impõe s(t7) = x3
sd6 = x3 - sa6*t7^3 - sb6*t7^2 - sc6*t7;
*)
        #Data.sa6 := #Data.a6 / 3;
        #Data.sb6 := #Data.B6_Coef / 2;
        #Data.sc6 := #Data.c6;
        #Data.sd6 := #Data.X3_R - (#Data.sa6 * (#Data.t7 ** 3)) - (#Data.sb6 * (#Data.t7 ** 2)) - (#Data.sc6 * #Data.t7);
(*
Calcula a função s(t) (posição no tempo) nos instantes em que
é necessário trocar a curva utilizada:
Posição no tempo t1 (início da reta v(t) de aceleração):
x01 = sa2*t1^2 + sb2*t1 + sc2;
*)
        #Data.X01_Pos := #Data.sa2 * (#Data.t1 ** 2) + (#Data.sb2 * #Data.t1) + #Data.sc2;
(*
Posição no tempo t2 (fim da reta v(t) de aceleração):
x02 = sa2*t2^2 + sb2*t2 + sc2
*)
        #Data.X02_Pos := #Data.sa2 * (#Data.t2 ** 2) + (#Data.sb2 * #Data.t2) + #Data.sc2;
(*
Posição no tempo t5 (início da reta de desaceleração):
x21 = sa5*t5^2 + sb5*t5 + sc5;
*)
        #Data.X21_Pos := #Data.sa5 * (#Data.t5 ** 2) + (#Data.sb5 * #Data.t5) + #Data.sc5;
(*
Posição no tempo t6 (fim da reta de desaceleração):
x22 = sa5*t6^2 + sb5*t6 + sc5;
*)
        #Data.X22_Pos := #Data.sa5 * (#Data.t6 ** 2) + (#Data.sb5 * #Data.t6) + #Data.sc5;
        
(*
Para calcular a velocidade em função de current_position, é necessário calcular
o instante de tempo em que current_position é atingida; portanto, deve-se
resolver a equação s(t) = current_position. A função s(t) já é conhecida.
Ela é um polinômio de terceiro grau. Como s(t) é monotônica no intervalo
da curva utilizada, existe uma única solução válida nesse intervalo.
A rotina RRoot encontra essa solução pelo método de Newton-Raphson.
*)
        #Data.done := TRUE;
    END_IF;
(*
-------------------
Usa a parábola 1
-------------------
*)
    
    IF #Data.BWD THEN
        #Data.CURRENT_POSITION := 2 * #Data.X0_R - #Data.POSITION;
    ELSE
        #Data.CURRENT_POSITION := #Data.POSITION;
    END_IF;
    
    IF #Data.CURRENT_POSITION >= #Data.X0_R AND #Data.CURRENT_POSITION < #Data.X01_Pos THEN
        // Usa a parábola 1
        #Data.dummy1 := #Data.sd1 - #Data.CURRENT_POSITION;
        #Data.t := "RRoot"(
                           cx3 := #Data.sa1,
                           cx2 := #Data.sb1,
                           cx1 := #Data.sc1,
                           cx0 := #Data.dummy1,
                           MinVal := #Data.t0,
                           MaxVal := #Data.t1
        );
        
        #Data.Speed := #Data.a1 * #Data.t ** 2 + #Data.B1_Coef * #Data.t + #Data.c1;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X01_Pos AND #Data.CURRENT_POSITION < #Data.X02_Pos THEN
        // Usa a reta de aceleração
        // Entre as duas soluções, utiliza a maior (a é sempre > 0)
        #Data.t := (- #Data.sb2 + SQRT(#Data.sb2 ** 2 - 4 * #Data.sa2 * (#Data.sc2 - #Data.CURRENT_POSITION))) / (2 * #Data.sa2);
        #Data.Speed := #Data.a2 * #Data.t + #Data.B2_Coef;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X02_Pos AND #Data.CURRENT_POSITION < #Data.X1_R THEN
        // Usa a parábola 2
        #Data.dummy1 := #Data.sd3 - #Data.CURRENT_POSITION;
        #Data.t := "RRoot"(
                           cx3 := #Data.sa3,
                           cx2 := #Data.sb3,
                           cx1 := #Data.sc3,
                           cx0 := #Data.dummy1,
                           MinVal := #Data.t2,
                           MaxVal := #Data.t3
        );
        #Data.Speed := #Data.a3 * #Data.t ** 2 + #Data.B3_Coef * #Data.t + #Data.c3;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X1_R AND #Data.CURRENT_POSITION < #Data.X2_R THEN
        // Trecho de velocidade constante
        #Data.Speed := #Data.Y1_R;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X2_R AND #Data.CURRENT_POSITION < #Data.X21_Pos THEN
        // Usa a parábola 3
        #Data.dummy1 := #Data.sd4 - #Data.CURRENT_POSITION;
        #Data.t := "RRoot"(
                           cx3 := #Data.sa4,
                           cx2 := #Data.sb4,
                           cx1 := #Data.sc4,
                           cx0 := #Data.dummy1,
                           MinVal := #Data.t4,
                           MaxVal := #Data.t5
        );
        #Data.Speed := #Data.a4 * #Data.t ** 2 + #Data.B4_Coef * #Data.t + #Data.c4;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X21_Pos AND #Data.CURRENT_POSITION < #Data.X22_Pos THEN
        // Usa a reta de aceleração
        // Entre as duas soluções, utiliza a maior (a é sempre > 0)
        #Data.t := (- #Data.sb5 + SQRT(#Data.sb5 ** 2 - 4 * #Data.sa5 * (#Data.sc5 - #Data.CURRENT_POSITION))) / (2 * #Data.sa5);
        #Data.Speed := #Data.a5 * #Data.t + #Data.B5_Coef;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X22_Pos AND #Data.CURRENT_POSITION < #Data.X3_R THEN
        // Usa a parábola 4
        #Data.dummy1 := #Data.sd6 - #Data.CURRENT_POSITION;
        #Data.t := "RRoot"(
                           cx3 := #Data.sa6,
                           cx2 := #Data.sb6,
                           cx1 := #Data.sc6,
                           cx0 := #Data.dummy1,
                           MinVal := #Data.t6,
                           MaxVal := #Data.t7
        );
        #Data.Speed := #Data.a6 * #Data.t ** 2 + #Data.B6_Coef * #Data.t + #Data.c6;
        
    ELSIF #Data.CURRENT_POSITION >= #Data.X3_R THEN
        #Data.Speed := #Data.Y3_R;
    END_IF;
    
    IF #Data.CURRENT_POSITION <= #Data.X0_R THEN
        #Data.Speed := #Data.Y0_R;
    END_IF;
    
    IF #Data.X3_Pos < #Data.X0_Pos THEN
        #Data.Speed := #Data.Y3_R;
    END_IF;
    
    IF #Data.Speed > #Data.Y1_R THEN
        #Data.Speed := #Data.Y1_R;
    END_IF;
    
    IF #Data.Speed < 0 THEN
        #Data.Speed := 0;
    END_IF;
    
    //**************************** CORREÇÃO FINAL DE POSIÇÃO *******************************************************
    // Normaliza parâmetros para impedir valores inválidos.
    IF #Data.PosTolerance < 0.0 THEN
        #Data.PosTolerance := 0.0;
    END_IF;
    IF #Data.PosCorrWindow < #Data.PosTolerance THEN
        #Data.PosCorrWindow := #Data.PosTolerance;
    END_IF;
    IF #Data.PosCorrKp < 0.0 THEN
        #Data.PosCorrKp := 0.0;
    END_IF;
    IF #Data.PosCorrMinSpeed < 0.0 THEN
        #Data.PosCorrMinSpeed := 0.0;
    END_IF;
    
    // Como BWD já foi espelhado em current_position, o erro positivo sempre significa
    // que o eixo ainda está antes do target no sentido normalizado do movimento.
    #Data.PosError := #Data.X3_R - #Data.CURRENT_POSITION;
    #Data.AtTarget := ABS(#Data.PosError) <= #Data.PosTolerance;
    #Data.PosOverShoot := #Data.PosError < (- #Data.PosTolerance);
    #Data.PosCorrSpeed := #Data.Speed;
    
    IF #Data.PosCorrEnable THEN
        IF #Data.AtTarget THEN
            // Dentro da tolerância: referência zero.
            #Data.Speed := 0.0;
            
        ELSIF #Data.PosOverShoot THEN
            // Ultrapassou o target além da tolerância: para e sinaliza.
            // A inversão automática de sentido deve ser tratada externamente.
            #Data.Speed := 0.0;
            
        ELSIF #Data.CURRENT_POSITION >= #Data.X2_R AND #Data.PosError <= #Data.PosCorrWindow THEN
            // Correção proporcional de posição em baixa velocidade:
            // Vcorr = Kp * erro_de_posição
            #Data.PosCorrSpeed := #Data.PosCorrKp * #Data.PosError;
            
            // Mantém uma velocidade mínima de aproximação enquanto estiver fora da tolerância.
            IF #Data.PosCorrSpeed < #Data.PosCorrMinSpeed THEN
                #Data.PosCorrSpeed := #Data.PosCorrMinSpeed;
            END_IF;
            
            // A camada de correção só pode REDUZIR a velocidade calculada pelo perfil principal.
            // Assim, ela não cria um degrau de aceleração no trecho final.
            IF #Data.PosCorrSpeed < #Data.Speed THEN
                #Data.Speed := #Data.PosCorrSpeed;
            END_IF;
        END_IF;
    END_IF;
    //*************************************************************************************************************
    
    IF #Data.CURRENT_POSITION <= #Data.X1_R THEN
        #Data.AccOn := TRUE;
    ELSE
        #Data.AccOn := FALSE;
    END_IF;
    
    IF #Data.CURRENT_POSITION >= #Data.X2_R THEN
        #Data.DecOn := TRUE;
    ELSE
        #Data.DecOn := FALSE;
    END_IF;
    
    IF #Data.CURRENT_POSITION >= #Data.X3_R THEN
        #Data.MinSpeed := TRUE;
    ELSE
        #Data.MinSpeed := FALSE;
    END_IF;
    
    IF #Data.TIMEDONE THEN
        #Data.BWD := FALSE;
        #Data.FWD := FALSE;
        #Data.Speed := 0.0;
    END_IF;
    
END_IF;
