
A relação entre **vazão**, **área do cilindro** e **velocidade** é uma das bases do dimensionamento hidráulico.

A fórmula fundamental é:

$$  
Q=A\cdot v  
$$

onde:

- $Q$ = vazão
    
- $A$ = área efetiva
    
- $v$ = velocidade linear do êmbolo
    

---

## Forma prática

Quando usamos:

- área em $cm^2$
    
- velocidade em $cm/s$
    
- vazão em $L/min$
    

a fórmula prática é:

> [!important] Vazão necessária  
> $$  
> \boxed{  
> Q[L/min]=A[cm^2]\cdot v[cm/s]\cdot0{,}06  
> }  
> $$

E isolando a velocidade:

> [!important] Velocidade do cilindro  
> $$  
> \boxed{  
> v[cm/s]=\frac{Q[L/min]}{A[cm^2]\cdot0{,}06}  
> }  
> $$

---

# Área do lado do pistão

No avanço de um cilindro comum de haste simples, a pressão atua sobre toda a área do pistão:

$$  
A_p=\frac{\pi D^2}{4}  
$$

onde:

- $D$ = diâmetro do pistão
    

Se $D$ estiver em cm, a área sairá em $cm^2$.

### Exemplo

Para:

$$  
D=100mm=10cm  
$$

temos:

$$  
A_p=\frac{\pi\cdot10^2}{4}  
$$

$$  
A_p=78{,}54cm^2  
$$

> [!success]  
> $$  
> \boxed{A_p=78{,}54cm^2}  
> $$

---

# Área do lado da haste

No recuo, a haste ocupa parte da área disponível.

Então deve ser utilizada a **área anular**:

$$  
A_r=A_p-A_h  
$$

onde:

$$  
A_h=\frac{\pi d^2}{4}  
$$

Logo:

$$  
A_r=\frac{\pi(D^2-d^2)}{4}  
$$

onde:

- $D$ = diâmetro do pistão
    
- $d$ = diâmetro da haste
    

---

## Exemplo

Considere:

$$  
D=100mm  
$$

e:

$$  
d=50mm  
$$

Área do pistão:

$$  
A_p=78{,}54cm^2  
$$

Área da haste:

$$  
A_h=\frac{\pi\cdot5^2}{4}  
$$

$$  
A_h=19{,}63cm^2  
$$

Então:

$$  
A_r=78{,}54-19{,}63  
$$

$$  
\boxed{A_r=58{,}90cm^2}  
$$

---

# Vazão necessária no avanço

Para o avanço:

$$  
Q_{av}=A_p\cdot v\cdot0{,}06  
$$

Com:

$$  
A_p=78{,}54cm^2  
$$

temos:

$$  
Q_{av}=78{,}54\cdot v\cdot0{,}06  
$$

$$  
\boxed{  
Q_{av}=4{,}712\cdot v  
}  
$$

---

## Exemplo

Se desejamos:

$$  
v=20cm/s  
$$

então:

$$  
Q_{av}=4{,}712\cdot20  
$$

$$  
\boxed{Q_{av}=94{,}24L/min}  
$$

---

# Vazão necessária no recuo

No recuo:

$$  
Q_{rec}=A_r\cdot v\cdot0{,}06  
$$

Para:

$$  
A_r=58{,}90cm^2  
$$

temos:

$$  
Q_{rec}=58{,}90\cdot v\cdot0{,}06  
$$

$$  
\boxed{  
Q_{rec}=3{,}534\cdot v  
}  
$$

Se desejamos os mesmos:

$$  
v=20cm/s  
$$

então:

$$  
Q_{rec}=3{,}534\cdot20  
$$

$$  
\boxed{Q_{rec}=70{,}68L/min}  
$$

---

# Por que avanço e recuo têm velocidades diferentes?

Para uma mesma vazão:

$$  
v=\frac{Q}{A}  
$$

Como:

$$  
A_r<A_p  
$$

então:

$$  
v_{rec}>v_{av}  
$$

Ou seja:

> [!note]  
> Em um cilindro de haste simples, com a mesma vazão de alimentação, o **recuo normalmente é mais rápido que o avanço**.

---

# Exemplo com vazão fixa

Considere:

$$  
Q=120L/min  
$$

### Avanço

$$  
v_{av}=  
\frac{120}{78{,}54\cdot0{,}06}  
$$

$$  
\boxed{v_{av}\approx25{,}5cm/s}  
$$

### Recuo

$$  
v_{rec}=  
\frac{120}{58{,}90\cdot0{,}06}  
$$

$$  
\boxed{v_{rec}\approx34cm/s}  
$$

---

# Tempo de curso

Conhecendo a velocidade, o tempo necessário para percorrer o curso é:

$$  
t=\frac{L}{v}  
$$

onde:

- $t$ = tempo
    
- $L$ = curso
    
- $v$ = velocidade
    

---

## Exemplo

Para um curso de:

$$  
L=500mm=50cm  
$$

e velocidade de avanço:

$$  
v=25{,}5cm/s  
$$

temos:

$$  
t=\frac{50}{25{,}5}  
$$

$$  
\boxed{t\approx1{,}96s}  
$$

---

# Vazão a partir do tempo de curso

Também podemos calcular a vazão diretamente se soubermos quanto tempo desejamos para completar o curso.

Primeiro:

$$  
v=\frac{L}{t}  
$$

Depois:

$$  
Q=A\cdot v\cdot0{,}06  
$$

Substituindo:

$$  
Q=A\cdot\frac{L}{t}\cdot0{,}06  
$$

> # [!important]  
> $$  
> \boxed{  
> Q[L/min]
> 
> \frac{A[cm^2]\cdot L[cm]\cdot0{,}06}{t[s]}  
> }  
> $$

---

## Exemplo

Cilindro:

$$  
A=78{,}54cm^2  
$$

Curso:

$$  
L=50cm  
$$

Tempo desejado:

$$  
t=2s  
$$

Então:

$$  
Q=  
\frac{78{,}54\cdot50\cdot0{,}06}{2}  
$$

$$  
\boxed{Q\approx117{,}8L/min}  
$$

---

# Volume do cilindro

Outra forma de calcular é pelo volume preenchido.

No avanço:

$$  
V_{av}=A_p\cdot L  
$$

No recuo:

$$  
V_{rec}=A_r\cdot L  
$$

Se $A$ estiver em $cm^2$ e $L$ em cm:

$$  
V[cm^3]=A[cm^2]\cdot L[cm]  
$$

Como:

$$  
1000cm^3=1L  
$$

podemos converter para litros.

---

## Exemplo

Para:

$$  
A_p=78{,}54cm^2  
$$

e:

$$  
L=50cm  
$$

temos:

$$  
V_{av}=78{,}54\cdot50  
$$

$$  
V_{av}=3927cm^3  
$$

$$  
\boxed{V_{av}\approx3{,}93L}  
$$

No lado da haste:

$$  
V_{rec}=58{,}90\cdot50  
$$

$$  
V_{rec}=2945cm^3  
$$

$$  
\boxed{V_{rec}\approx2{,}95L}  
$$

---

# Vazão utilizando volume e tempo

Se conhecemos o volume necessário e o tempo de movimento:

$$  
Q=\frac{V}{t}  
$$

Mas é preciso corrigir as unidades.

Para $V$ em litros e $t$ em segundos:

> [!formula]  
> $$  
> \boxed{  
> Q[L/min]=\frac{V[L]}{t[s]}\cdot60  
> }  
> $$

---

## Exemplo

Se o avanço precisa preencher:

$$  
V=3{,}93L  
$$

em:

$$  
t=2s  
$$

então:

$$  
Q=\frac{3{,}93}{2}\cdot60  
$$

$$  
\boxed{Q\approx117{,}9L/min}  
$$

O resultado é o mesmo obtido pelo cálculo de área e velocidade.

---

# Relação com a bomba

Para que o cilindro consiga atingir a velocidade desejada:

$$  
Q_{bomba}\geq Q_{cilindro}  
$$

Se:

$$  
Q_{cilindro}>Q_{bomba}  
$$

a velocidade real será menor que a desejada.

Em uma bomba de cilindrada variável com controle DR, se a demanda aumentar, o controlador tenta aumentar a cilindrada:

$$  
V_g\uparrow  
$$

até chegar a:

$$  
V_{gmax}  
$$

Se ainda assim:

$$  
Q_{demanda}>Q_{max}  
$$

a bomba não consegue fornecer vazão suficiente e a pressão do sistema pode cair.

---

# Exemplo completo

Cilindro:

$$  
D=100mm  
$$

$$  
d=50mm  
$$

$$  
L=500mm  
$$

Áreas:

$$  
A_p=78{,}54cm^2  
$$

$$  
A_r=58{,}90cm^2  
$$

Desejamos avanço em:

$$  
v=25cm/s  
$$

Então:

$$  
Q_{av}=78{,}54\cdot25\cdot0{,}06  
$$

$$  
\boxed{Q_{av}\approx117{,}8L/min}  
$$

No recuo, para manter os mesmos 25 cm/s:

$$  
Q_{rec}=58{,}90\cdot25\cdot0{,}06  
$$

$$  
\boxed{Q_{rec}\approx88{,}4L/min}  
$$

Portanto, para manter **a mesma velocidade nos dois sentidos**, precisamos de vazões diferentes.

---

# Fórmulas principais

> [!formula] Área do pistão  
> $$  
> \boxed{  
> A_p=\frac{\pi D^2}{4}  
> }  
> $$

> [!formula] Área da haste  
> $$  
> \boxed{  
> A_h=\frac{\pi d^2}{4}  
> }  
> $$

> [!formula] Área anular  
> $$  
> \boxed{  
> A_r=\frac{\pi(D^2-d^2)}{4}  
> }  
> $$

> # [!formula] Vazão  
> $$  
> \boxed{  
> Q[L/min]
> 
> A[cm^2]\cdot v[cm/s]\cdot0{,}06  
> }  
> $$

> # [!formula] Velocidade  
> $$  
> \boxed{  
> v[cm/s]
> 
> \frac{Q[L/min]}{A[cm^2]\cdot0{,}06}  
> }  
> $$

> [!formula] Tempo de curso  
> $$  
> \boxed{  
> t=\frac{L}{v}  
> }  
> $$

> [!formula] Volume  
> $$  
> \boxed{  
> V=A\cdot L  
> }  
> $$

> [!formula] Vazão a partir do volume  
> $$  
> \boxed{  
> Q[L/min]=\frac{V[L]}{t[s]}\cdot60  
> }  
> $$

---

# Resumo

A relação fundamental para cilindros hidráulicos é:

$$  
\boxed{Q=A\cdot v}  
$$

Portanto:

- maior vazão → maior velocidade;
    
- maior área → mais vazão necessária para a mesma velocidade;
    
- menor área → maior velocidade para a mesma vazão;
    
- no avanço usa-se a área total do pistão;
    
- no recuo usa-se a área anular;
    
- em cilindros de haste simples, o recuo tende a ser mais rápido que o avanço para a mesma vazão.
    

O cálculo sempre pode ser reduzido a três grandezas:

> [!important]  
> $$  
> \boxed{  
> \text{Área}  
> \leftrightarrow  
> \text{Vazão}  
> \leftrightarrow  
> \text{Velocidade}  
> }  
> $$