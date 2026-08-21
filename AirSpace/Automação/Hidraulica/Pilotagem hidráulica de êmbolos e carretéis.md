
A pilotagem hidráulica de êmbolos e carretéis é baseada na conversão de **pressão hidráulica em força mecânica**.

> [!important] Relação fundamental  
> A força hidráulica produzida sobre um êmbolo é:
> 
> $$  
> F = p \cdot A  
> $$
> 
> onde:
> 
> - $F$ = força hidráulica
>     
> - $p$ = pressão
>     
> - $A$ = área efetiva do êmbolo
>     

---

## Área efetiva do êmbolo

Quanto maior a área efetiva sobre a qual a pressão atua, maior será a força produzida.

> [!formula] Conversão prática  
> Quando usamos pressão em **bar** e área em **cm²**:
> 
> $$  
> \boxed{F[N] = 10 \cdot p[bar] \cdot A[cm^2]}  
> $$

### Exemplo

Considere:

$$  
A = 1\ cm^2  
$$

e:

$$  
p = 100\ bar  
$$

Então:

$$  
F = 10 \cdot 100 \cdot 1  
$$

$$  
\boxed{F = 1000\ N}  
$$

> [!tip]  
> Uma regra muito útil:
> 
> $$  
> \boxed{1\ bar \ em\ 1\ cm^2 = 10\ N}  
> $$

Logo:

$$  
100\ bar \rightarrow 1000\ N  
$$

$$  
200\ bar \rightarrow 2000\ N  
$$

---

# Força da mola

Em muitos reguladores hidráulicos, a pressão precisa vencer uma mola.

A mola segue aproximadamente a Lei de Hooke:

$$  
F_m = Kx  
$$

onde:

- $F_m$ = força da mola
    
- $K$ = constante elástica da mola
    
- $x$ = compressão da mola
    

### Exemplo

Para:

$$  
K = 4000\ N/m  
$$

e:

$$  
x = 60\ mm = 0{,}06\ m  
$$

temos:

$$  
F_m = 4000 \cdot 0{,}06  
$$

$$  
\boxed{F_m = 240\ N}  
$$

---

# Pré-carga da mola

A mola normalmente já é montada parcialmente comprimida.

Se:

- $L_0$ = comprimento livre
    
- $L_w$ = comprimento instalado
    

então:

$$  
x = L_0 - L_w  
$$

e:

$$  
F_{pré} = K(L_0-L_w)  
$$

### Exemplo

Se:

$$  
L_0 = 120\ mm  
$$

e:

$$  
L_w = 60\ mm  
$$

então:

$$  
x = 120-60  
$$

$$  
x = 60\ mm  
$$

Convertendo:

$$  
x = 0{,}06\ m  
$$

Logo:

$$  
F_{pré} = 4000 \cdot 0{,}06  
$$

$$  
\boxed{F_{pré} = 240\ N}  
$$

> [!note]  
> Essa força já existe antes mesmo de a pressão hidráulica começar a atuar sobre o êmbolo.

---

# Pressão necessária para vencer a mola

Quando a pressão atua contra uma mola:

$$  
F_{hid} = F_m  
$$

Como:

$$  
F_{hid}=pA  
$$

temos:

$$  
pA=F_m  
$$

Logo:

$$  
\boxed{p=\frac{F_m}{A}}  
$$

---

## Exemplo com área de 1 cm²

Considere:

$$  
A=1\ cm^2  
$$

e:

$$  
F_m=240\ N  
$$

Como:

$$  
1\ cm^2 = 1\times10^{-4}\ m^2  
$$

temos:

$$  
p=\frac{240}{1\times10^{-4}}  
$$

$$  
p=2{,}4\times10^6\ Pa  
$$

Sabendo que:

$$  
1\ bar=10^5\ Pa  
$$

então:

$$  
p=24\ bar  
$$

> [!success] Resultado  
> $$  
> \boxed{p_{atuação}\approx24\ bar}  
> $$

---

# Relação direta entre área e pressão

A pressão de atuação é:

$$  
p=\frac{F_m}{A}  
$$

Portanto:

$$  
A\uparrow \Rightarrow p_{atuação}\downarrow  
$$

e:

$$  
A\downarrow \Rightarrow p_{atuação}\uparrow  
$$

> [!example]  
> Para a mesma mola de 240 N:
> 
> Se:
> 
> $$  
> A=1\ cm^2  
> $$
> 
> então:
> 
> $$  
> p=24\ bar  
> $$
> 
> Mas se:
> 
> $$  
> A=0{,}5\ cm^2  
> $$
> 
> então:
> 
> $$  
> p=48\ bar  
> $$

---

# Relação entre ajuste da mola e pressão

O parafuso de regulagem normalmente modifica a **pré-compressão da mola**.

Ao apertar o parafuso:

$$  
x\uparrow  
$$

então:

$$  
F_m=Kx\uparrow  
$$

e consequentemente:

$$  
p_{ajuste}\uparrow  
$$

Ao soltar:

$$  
x\downarrow  
$$

$$  
F_m\downarrow  
$$

$$  
p_{ajuste}\downarrow  
$$

> [!important]  
> O parafuso não muda a constante $K$ da mola.
> 
> Ele muda apenas a sua **pré-carga**.

---

# Exemplo de regulagem para 200 bar

Suponha:

$$  
A=1\ cm^2  
$$

e desejamos:

$$  
p=200\ bar  
$$

Usando:

$$  
F = 10\cdot p\cdot A  
$$

temos:

$$  
F = 10\cdot200\cdot1  
$$

$$  
\boxed{F=2000\ N}  
$$

Isso significa que o sistema precisa de aproximadamente:

$$  
\boxed{2000\ N}  
$$

de força contrária para equilibrar 200 bar nessa área.

---

## Calculando a constante da mola

Se a compressão disponível for:

$$  
x=60\ mm=0{,}06\ m  
$$

e queremos:

$$  
F=2000\ N  
$$

então:

$$  
K=\frac{F}{x}  
$$

$$  
K=\frac{2000}{0{,}06}  
$$

$$  
K\approx33333\ N/m  
$$

> [!success] Resultado  
> Para produzir cerca de 2000 N com 60 mm de compressão:
> 
> $$  
> \boxed{K\approx33{,}3\ kN/m}  
> $$

---

# Diâmetro e área do êmbolo

Quando conhecemos apenas o diâmetro:

$$  
A=\frac{\pi D^2}{4}  
$$

### Exemplo

Para:

$$  
D=10\ mm  
$$

temos:

$$  
A=\frac{\pi(10)^2}{4}  
$$

$$  
A\approx78{,}54\ mm^2  
$$

Convertendo:

$$  
A\approx0{,}785\ cm^2  
$$

Se aplicarmos:

$$  
p=100\ bar  
$$

então:

$$  
F=10\cdot100\cdot0{,}785  
$$

$$  
\boxed{F\approx785\ N}  
$$

---

# Área anular

Quando existe uma haste em um dos lados do pistão, as áreas não são iguais.

Área total:

$$  
A_1=\frac{\pi D^2}{4}  
$$

Área anular:

$$  
A_2=\frac{\pi(D^2-d^2)}{4}  
$$

onde:

- $D$ = diâmetro do pistão
    
- $d$ = diâmetro da haste
    

Por isso, a mesma pressão pode produzir forças diferentes nos dois sentidos.

---

# Pilotagem de um lado contra mola

Em uma configuração simples:

- pressão atua de um lado;
    
- mola atua do outro.
    

Enquanto:

$$  
pA<F_m  
$$

o êmbolo permanece parado.

Quando:

$$  
pA>F_m  
$$

o êmbolo começa a se deslocar.

> [!summary]  
> $$  
> \boxed{  
> \text{pressão}  
> \rightarrow  
> \text{força hidráulica}  
> \rightarrow  
> \text{vence mola}  
> \rightarrow  
> \text{movimento}  
> }  
> $$

---

# Pilotagem hidráulica dos dois lados

Quando o êmbolo possui duas câmaras:

$$  
F_1=p_1A_1  
$$

e:

$$  
F_2=p_2A_2  
$$

A força resultante pode ser escrita aproximadamente como:

$$  
F_R=p_1A_1-p_2A_2-F_m  
$$

### Condições

Se:

$$  
F_R>0  
$$

o êmbolo movimenta-se em um sentido.

Se:

$$  
F_R<0  
$$

movimenta-se no sentido contrário.

Se:

$$  
F_R\approx0  
$$

o êmbolo entra em equilíbrio.

---

# Equilíbrio estático

Em um sistema sem aceleração significativa:

$$  
\sum F=0  
$$

Por exemplo:

$$  
p_1A_1=p_2A_2+F_m  
$$

Esse é o princípio que permite ao êmbolo permanecer em **posições intermediárias**.

> [!important]  
> Um regulador hidráulico não precisa trabalhar apenas totalmente aberto ou totalmente fechado.
> 
> Ele pode modular continuamente sua posição através do equilíbrio de forças.

---

# Influência da rigidez da mola

A rigidez é dada por:

$$  
K=\frac{\Delta F}{\Delta x}  
$$

Uma mola com $K$ elevado aumenta bastante a força mesmo com pequenos deslocamentos.

Uma mola com $K$ baixo possui comportamento mais suave.

Assim:

$$  
K\uparrow  
\Rightarrow  
\frac{\Delta F}{\Delta x}\uparrow  
$$

---

# Relação entre pressão e deslocamento

Quando:

$$  
pA=Kx  
$$

temos:

$$  
x=\frac{pA}{K}  
$$

Isso mostra que, em determinados sistemas, o deslocamento do êmbolo pode ser aproximadamente proporcional à pressão aplicada.

---

# Aplicação no compensador DR

No controle **DR**, a pressão da saída da bomba atua sobre um piloto hidráulico.

A força hidráulica é:

$$  
F_B=p_BA  
$$

Enquanto:

$$  
p_BA<F_m  
$$

a mola mantém o carretel na posição inicial.

A bomba permanece próxima de:

$$  
V_g=V_{gmax}  
$$

Quando:

$$  
p_BA\approx F_m  
$$

o carretel começa a se deslocar.

Esse movimento direciona óleo para o servo do prato.

O servo reduz a inclinação:

$$  
\alpha\downarrow  
$$

então:

$$  
V_g\downarrow  
$$

e:

$$  
Q\downarrow  
$$

> [!summary]  
> $$  
> \boxed{  
> p_B\uparrow  
> \rightarrow  
> F_B\uparrow  
> \rightarrow  
> carretel\ desloca  
> \rightarrow  
> servo\ atua  
> \rightarrow  
> V_g\downarrow  
> \rightarrow  
> Q\downarrow  
> }  
> $$

---

# Aplicação no controle FR

No controle **FR**, o carretel compara duas pressões:

$$  
\Delta p=p_B-p_X  
$$

A mola estabelece o diferencial desejado:

$$  
\Delta p_{ajuste}\approx\frac{F_m}{A}  
$$

Se:

$$  
\Delta p>\Delta p_{ajuste}  
$$

o controlador reduz a cilindrada.

Se:

$$  
\Delta p<\Delta p_{ajuste}  
$$

o controlador aumenta a cilindrada.

> [!summary]  
> O FR não regula diretamente uma pressão absoluta.
> 
> Ele regula uma **diferença de pressão**.

---

# Servo do prato

O servo é responsável por transformar o sinal hidráulico em movimento do prato oscilante.

A cadeia completa é:

$$  
\boxed{  
\text{pressão piloto}  
\rightarrow  
F=pA  
\rightarrow  
\text{movimento do servo}  
\rightarrow  
\text{ângulo do prato}  
\rightarrow  
V_g  
\rightarrow  
Q  
}  
$$

Em servos com duas câmaras, o controlador pode:

- pressurizar uma câmara;
    
- aliviar a outra;
    
- inverter as ligações para movimentar no sentido oposto.
    

---

# Fórmulas principais

> [!formula] Força hidráulica  
> $$  
> \boxed{F=pA}  
> $$

> [!formula] Forma prática  
> $$  
> \boxed{F[N]=10\cdot p[bar]\cdot A[cm^2]}  
> $$

> [!formula] Força da mola  
> $$  
> \boxed{F_m=Kx}  
> $$

> [!formula] Pré-carga  
> $$  
> \boxed{F_{pré}=K(L_0-L_w)}  
> $$

> [!formula] Pressão de atuação  
> $$  
> \boxed{p=\frac{F_m}{A}}  
> $$

> [!formula] Área circular  
> $$  
> \boxed{A=\frac{\pi D^2}{4}}  
> $$

> [!formula] Área anular  
> $$  
> \boxed{A=\frac{\pi(D^2-d^2)}{4}}  
> $$

> [!formula] Força resultante em êmbolo de duas câmaras  
> $$  
> \boxed{F_R=p_1A_1-p_2A_2-F_m}  
> $$

---

# Resumo final

A pilotagem hidráulica pode ser entendida como um equilíbrio entre:

> [!important]  
> **Pressão × Área × Mola**

A pressão gera força:

$$  
F=pA  
$$

A mola oferece resistência:

$$  
F_m=Kx  
$$

O êmbolo começa a se mover quando a força hidráulica supera as forças contrárias.

Assim:

- maior pressão → maior força;
    
- maior área → maior força;
    
- maior pré-carga → maior pressão de atuação;
    
- maior rigidez da mola → maior aumento de força com o curso;
    
- menor área → maior pressão necessária;
    
- duas câmaras permitem atuação hidráulica nos dois sentidos.
    

Nas bombas de cilindrada variável, esse equilíbrio é utilizado para controlar o servo que posiciona o prato oscilante e, consequentemente, controlar:

$$  
\boxed{V_g}  
$$

e:

$$  
\boxed{Q}  
$$