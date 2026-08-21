# Cálculo de Vazão em Bombas Hidráulicas

A vazão de uma bomba hidráulica de deslocamento positivo depende principalmente de:

- **cilindrada da bomba**
    
- **rotação do eixo**
    
- **eficiência volumétrica**
    

Para bombas de pistões axiais de cilindrada variável, também entra a posição do prato oscilante, porque ela determina a cilindrada instantânea.

---

## Vazão teórica

A fórmula básica é:

$$  
Q_{teórico} = \frac{V_g \cdot n}{1000}  
$$

onde:

- $Q$ = vazão em L/min
    
- $V_g$ = cilindrada em cm³/rev
    
- $n$ = rotação em rpm
    

> [!important] Fórmula principal  
> $$  
> \boxed{  
> Q[L/min]=\frac{V_g[cm^3/rev]\cdot n[rpm]}{1000}  
> }  
> $$

---

## Exemplo 1 — bomba de 100 cm³/rev

Considere:

$$  
V_g=100\ cm^3/rev  
$$

e:

$$  
n=1500\ rpm  
$$

Então:

$$  
Q=\frac{100\cdot1500}{1000}  
$$

$$  
\boxed{Q=150\ L/min}  
$$

Isso significa que uma bomba de 100 cm³/rev girando a 1500 rpm fornece teoricamente:

$$  
\boxed{150\ L/min}  
$$

---

## Exemplo 2 — bomba de 28 cm³/rev

Para:

$$  
V_g=28\ cm^3/rev  
$$

e:

$$  
n=2000\ rpm  
$$

temos:

$$  
Q=\frac{28\cdot2000}{1000}  
$$

$$  
\boxed{Q=56\ L/min}  
$$

---

# Vazão real

Na prática existem vazamentos internos.

Por isso:

$$  
Q_{real}=Q_{teórico}\cdot\eta_v  
$$

onde:

- $\eta_v$ = eficiência volumétrica
    

Então:

$$  
\boxed{  
Q_{real}=  
\frac{V_g\cdot n}{1000}\cdot\eta_v  
}  
$$

---

## Exemplo com eficiência volumétrica

Considere:

$$  
V_g=100\ cm^3/rev  
$$

$$  
n=1500\ rpm  
$$

$$  
\eta_v=0{,}95  
$$

Primeiro:

$$  
Q_{teórico}=150\ L/min  
$$

Depois:

$$  
Q_{real}=150\cdot0{,}95  
$$

$$  
\boxed{Q_{real}=142{,}5\ L/min}  
$$

---

# Bombas de cilindrada variável

Em uma bomba variável, $V_g$ não é necessariamente constante.

O prato oscilante pode variar entre:

$$  
V_{gmin}  
$$

e:

$$  
V_{gmax}  
$$

Portanto:

$$  
V_{gmin}\leq V_g\leq V_{gmax}  
$$

Quanto maior a inclinação do prato:

$$  
\alpha\uparrow  
$$

maior a cilindrada:

$$  
V_g\uparrow  
$$

e consequentemente:

$$  
Q\uparrow  
$$

---

## Relação percentual de cilindrada

Se uma bomba possui:

$$  
V_{gmax}=100\ cm^3/rev  
$$

e está trabalhando com 80% da cilindrada:

$$  
V_g=0{,}8\cdot100  
$$

$$  
V_g=80\ cm^3/rev  
$$

A 1500 rpm:

$$  
Q=\frac{80\cdot1500}{1000}  
$$

$$  
\boxed{Q=120\ L/min}  
$$

---

# Vazão máxima

A vazão máxima ocorre quando:

$$  
V_g=V_{gmax}  
$$

Então:

$$  
\boxed{  
Q_{max}=  
\frac{V_{gmax}\cdot n}{1000}  
}  
$$

Por exemplo:

$$  
V_{gmax}=100\ cm^3/rev  
$$

$$  
n=3000\ rpm  
$$

Logo:

$$  
Q_{max}=\frac{100\cdot3000}{1000}  
$$

$$  
\boxed{Q_{max}=300\ L/min}  
$$

---

# Vazão mínima

Se a bomba puder chegar praticamente a:

$$  
V_g=0  
$$

então:

$$  
Q\approx0  
$$

Porém, na prática podem existir:

- vazamentos internos
    
- vazão de pilotagem
    
- vazão de lubrificação
    
- cilindrada residual
    
- batente mecânico de $V_{gmin}$
    

Por isso a vazão real pode não chegar exatamente a zero.

---

# Relação entre vazão e rotação

Para cilindrada constante:

$$  
Q\propto n  
$$

Isso significa que dobrar a rotação dobra aproximadamente a vazão.

Exemplo:

Para uma bomba de:

$$  
V_g=100\ cm^3/rev  
$$

a 1000 rpm:

$$  
Q=100\ L/min  
$$

a 1500 rpm:

$$  
Q=150\ L/min  
$$

a 2000 rpm:

$$  
Q=200\ L/min  
$$

> [!tip]  
> Em uma bomba de **100 cm³/rev**, a relação fica particularmente simples:
> 
> $$  
> \boxed{Q=0{,}1\cdot n}  
> $$

---

# Relação entre vazão e velocidade do cilindro

A vazão consumida por um cilindro é:

$$  
Q=A\cdot v  
$$

onde:

- $A$ = área do pistão
    
- $v$ = velocidade
    

Em unidades práticas:

# $$  
\boxed{  
Q[L/min]

A[cm^2]\cdot v[cm/s]\cdot0{,}06  
}  
$$

---

## Exemplo

Para um cilindro com:

$$  
A=100\ cm^2  
$$

movendo-se a:

$$  
v=20\ cm/s  
$$

temos:

$$  
Q=100\cdot20\cdot0{,}06  
$$

$$  
\boxed{Q=120\ L/min}  
$$

---

# Área do cilindro

Para o lado sem haste:

$$  
A=\frac{\pi D^2}{4}  
$$

onde $D$ é o diâmetro do pistão.

Para o lado da haste:

$$  
A=\frac{\pi(D^2-d^2)}{4}  
$$

onde:

- $D$ = diâmetro do pistão
    
- $d$ = diâmetro da haste
    

Por isso a vazão necessária para a mesma velocidade pode ser diferente no avanço e no recuo.

---

# Vazão e controle DR

Em uma bomba com controle DR, a bomba varia a cilindrada para tentar manter a pressão ajustada.

Se o sistema exige mais vazão:

$$  
Q_{demanda}\uparrow  
$$

o DR permite aumentar:

$$  
V_g\uparrow  
$$

para fornecer mais vazão.

Mas existe um limite:

$$  
V_g=V_{gmax}  
$$

Nesse ponto:

$$  
Q=Q_{max}  
$$

Se a carga exigir:

$$  
Q_{demanda}>Q_{max}  
$$

a bomba não consegue fornecer vazão suficiente.

Então:

$$  
\boxed{p\downarrow}  
$$

mesmo com o DR tentando recuperar a pressão.

---

# Reserva de vazão

Para que o controle DR ainda tenha capacidade de compensação, é interessante não trabalhar continuamente com a bomba em $V_{gmax}$.

Por exemplo:

$$  
Q_{demanda}\approx70%-85%\ de\ Q_{max}  
$$

deixa uma margem para o prato aumentar a cilindrada quando a pressão cair.

---

## Exemplo de reserva

Bomba:

$$  
V_{gmax}=100\ cm^3/rev  
$$

Rotação:

$$  
n=1500\ rpm  
$$

Então:

$$  
Q_{max}=150\ L/min  
$$

Se o sistema utilizar:

$$  
Q=120\ L/min  
$$

a porcentagem utilizada é:

$$  
\frac{120}{150}\cdot100  
$$

$$  
\boxed{80%}  
$$

A cilindrada necessária será:

$$  
V_g=\frac{Q\cdot1000}{n}  
$$

$$  
V_g=\frac{120\cdot1000}{1500}  
$$

$$  
\boxed{V_g=80\ cm^3/rev}  
$$

Portanto ainda existe:

$$  
20\ cm^3/rev  
$$

de reserva até $V_{gmax}$.

---

# Fórmulas principais

> [!formula] Vazão teórica da bomba  
> $$  
> \boxed{  
> Q=\frac{V_g\cdot n}{1000}  
> }  
> $$

> [!formula] Vazão real  
> $$  
> \boxed{  
> Q_{real}=  
> \frac{V_g\cdot n}{1000}\cdot\eta_v  
> }  
> $$

> [!formula] Cilindrada necessária  
> $$  
> \boxed{  
> V_g=\frac{Q\cdot1000}{n}  
> }  
> $$

> [!formula] Rotação necessária  
> $$  
> \boxed{  
> n=\frac{Q\cdot1000}{V_g}  
> }  
> $$

> [!formula] Vazão consumida por cilindro  
> $$  
> \boxed{  
> Q=A\cdot v  
> }  
> $$

> # [!formula] Forma prática para cilindro  
> $$  
> \boxed{  
> Q[L/min]
> 
> A[cm^2]\cdot v[cm/s]\cdot0{,}06  
> }  
> $$

---

# Resumo

A vazão de uma bomba depende principalmente de:

$$  
\boxed{  
\text{cilindrada}\times\text{rotação}  
}  
$$

Assim:

- maior cilindrada → maior vazão
    
- maior rotação → maior vazão
    
- maior inclinação do prato → maior cilindrada
    
- menor inclinação → menor vazão
    
- perdas internas reduzem a vazão real
    
- se a demanda superar $Q_{max}$, a pressão do sistema tende a cair
    

A relação fundamental é:

# $$  
\boxed{  
Q[L/min]

\frac{V_g[cm^3/rev]\cdot n[rpm]}{1000}  
}  
$$