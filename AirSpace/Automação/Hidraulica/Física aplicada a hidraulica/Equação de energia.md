
![[Pasted image 20251205091317.png]]

$$
\dot{M}\cdot \left[\frac{\Delta P}{\rho}+\frac{v_2^2-v_1^2}{2}+g\cdot\Delta y + h_L\right] = N
$$

#m = Taxa de massa [kg/s]
#ΔP = Variação de pressão [KPA]
#p = Massa específica do fluido [Kg/m³]
#Q = Vazão mínima necessária à bomba [m³/s]
#v1 = Velocidade inicial do fluido [m/s]
#v2 = Velocidade final do fluido [m/s]
#g = Aceleração da gravidade [m/s²]
#A1 = Seção transversal interna inicial do duto [cm²]
#A2 = Seção transversal interna final do duto [cm²]
#Δy = Diferença de nível [m]
#hl = Perda de carga total (pressão) no intervalo de duto estudado [m²/s²]
#N = Potência necessária à bomba de sucção para elevar o fluido à diferença de nível Δy
# Termo 1 (Energia por kg de óleo): $\frac{\Delta P}p$
O que é: 
- Energia que a bomba gasta para aumentar a pressão do fluido. (Energia por kg de óleo)

ΔP = diferença de pressão que você precisa vencer
p = densidade (kg/m³)

esse termo nos diz o quanto de energia por kg de óleo é necessária para dar esse aumento de pressão 
Energia por kg = ΔP / p

é quando a bomba precisa empurrar o fluido contra uma pressão maior do outro lado em termos físicos:

```
ΔP  é: P_Bomba - P_necessaria

sendo: P_necessaria = Freq / A

sendo: Freq = Fpseso + Fatrito + Fcontrario

sendo: F = m . a (sendo a força em newton)

temos de ver ΔP como uma resistência no circuito, barreira de pressão a ser vencida

no momento que dividimos ΔP/p estamos transformando energia por unidade de volume para energia por unidade de massa.

a kg/s e Energia por kg de óleo, explicam porque cilindros grandes e contra grandes cargas, não é o óleo parado dentro da camisa que esta recebendo energia, a energia só é entregue ao óleo que flui por segundo, ou seja, kg/s
a bomba sempre trabalha com: m = p . Q
```

energia de volume = pressão = força por unidade de área
energia por unidade de massa = quantidade energia que cada kg de óleo precisa

Para cada quilo de fluido que passa pela bomba, eu tenho que dar cerca 6.4kj só para vencer os 64 bar

Para que óleo consiga vencer uma resistência de 64 bar, cada kg de óleo precisa receber 6.4kj de energia útil da bomba.
###### Passo 1 - 

$P_{\text{req}} = \frac{F}{A}$

Se o cilindro tem 40 mm de diâmetro:
 $A=\frac{\pi D^2}{4}=\frac{3,14 \cdot 0,04^2}{4}=0,001256m^2$

Então:

$P_{\text{req}} = \frac{2000}{0,001256} = 1,59 \text{ MPa} \approx 16 \text{ bar}$

Passo 2 — ΔP que a bomba precisa vencer

Se a bomba fornece 80 bar:

$ΔP = 80 - 16 = 64 \text{ bar}$

$J/KG = 6,4  X 10³*³ = 7529\text{ J/kg}$
	
Esse ΔP representa a “margem de pressão” que pode virar:
velocidade (via válvula reguladora)

perdas (tubulação, curvas, filtro sujo)

carga dinâmica (aceleração)





# Termo 2 (Energia para acelerar o fluido): $\frac{v_2^2-v_1^2}{2}$

O que é:
- Energia cinética: acelera o fluido

Se v2 > v1 a bomba acelera o fluido, consome energia

Se v2 < v1 o sistema devolve energia para a bomba.

Aparece quando há estreitamento de tubulação, Venturi, válvulas parcialmente fechadas, boca livre, bomba centrifuga,


Um fluido acelera ao entrar numa tubulação mais estreita:

Tubo 1: Ø 80 mm

Tubo 2: Ø 40 mm

A vazão é Q = 10 L/s = 0,01 m³/s

► Cálculo das velocidades

ÁREA 1:

$A_1 = \frac{\pi (0,08)^2}{4} = 0,0050 \, m^2$

$v_1 = \frac{Q}{A_1} = \frac{0,01}{0,0050} = 2\, m/s$

ÁREA 2:

$A_2 = 0,00126 \, m^2$

$v_2 = \frac{0,01}{0,00126} = 7.9\, m/s$

► Cálculo do bloco 2

$e_c = \frac{v_2^2 - v_1^2}{2} = \frac{7.9^2 - 2^2}{2} = \frac{62.41 - 4}{2} = 29.2\ \text{J/kg}$

29 J/kg é a energia necessária para acelerar a massa de água no estrangulamento.


# Termo 3 (Energia para elevar cada kg de fluido): $g\cdot\Delta y$

```
g = aceleração da gravidade (= 9,81m/s²)
Δy = diferença de altura (m)
```

para cada kg de fluido que sobe ele precisa ganhar energia equivalente ao peso x altura.

O que é: 
- Energia potencial: elevar a coluna de fluido

Se Δy > 0, sobe . a bomba trabalha mais.
Se Δy < 0, desce . o fluido ajuda (gravidade devolve energia)


# Termo 4 (Energia perdida no percurso do fluido por atrito):  $h_L$

```
hl = toda energia desperdiçada por atrito, turbulência, rugosidade, estrangulament, válvulas, curvas, conexões, filtros, trocas de seção e tudo que resiste ao movimento do fluido.
```
O que é:
- perda de carga: atrito, válvulas, curvas, rugosidade, tubulação suja

toda perda de carga é vazamento de energia.

###### Perdas distribuídas:  $h_f = f \cdot \frac{L}{D} \cdot \frac{v^2}{2g}$
```
f = fator de atrito (rugosidade/regime)
L = comprimento do tubo
D = diâmetro interno
v = velocidade média
g = gravidade

isso apresenta "arrasto" do fluido contra as paredes.
```

###### Perdas Localizadas: $h_L = \sum K \cdot \frac{v^2}{2g}$

```
Cada peça tem um coeficiente K:

v = velocidade média
g = gravidade

curva 90° → K ≈ 0.3 a 1.0
T → K ≈ 1.0 a 2.0
válvula gaveta → K ≈ 0.15
válvula globo → K ≈ 5 a 10
redutores → K varia
filtro limpo → moderado
filtro sujo → K gigantesco
Você soma todos os K e multiplica por v²/2g

Noção:
o hl cresce com o quadrado da velocidade hl ∝ v²
dobrar a vazão = quadruplicar as perdas
aumentar 20% na vazão = aumentar 44% na perda
uma bomba mais forte pode não aumentar a vazão quase nada, se hl domina
```

# Termo 5: (Massa de fluido que passa por segundo) $\dot{M}$
```
M = p * Q (kg/s)

N = M * J/kg = potencia = energia por kg x kg por segundo
```

O que é:
- É o fluxo total de massa que passa pela bomba por segundo. Quanto mais fluido você movimenta, mais energia é necessária

Vazão controla velocidade.
Pressão controla força.
Juntos $m= \dot{M} \cdot J/kg$ controlam potência
# other

Outras formas de representar a equação de energia.
![[Pasted image 20251122153558.png]]

![[Pasted image 20251122153655.png]]

![[Pasted image 20251122153907.png]]

