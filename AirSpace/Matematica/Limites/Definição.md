A definição mais útil para você é esta:

$$\boxed{\text{Limite é o valor para o qual uma função tende quando a entrada se aproxima de algum ponto.}}$$

A palavra mais importante é:

$\boxed{\text{aproxima}}$

Limite não pergunta necessariamente:

> “Qual é o valor exatamente naquele ponto?”

Ele pergunta:

> “Para qual valor a função está caminhando quando chegamos cada vez mais perto daquele ponto?”

Por exemplo:

$\lim_{x\to3}(2x+1)$

isso se lê:

> Qual valor \(2x+1\) se aproxima quando \(x\) se aproxima de \(3\)?

Se colocarmos valores perto de \(3\):

$x=2{,}9$  
$2(2{,}9)+1=6{,}8$

mais perto:

$x=2{,}99$ 
$2(2{,}99)+1=6{,}98$

mais perto ainda:

$x=2{,}999 \]\[ 2(2{,}999)+1=6{,}998$

Estamos chegando cada vez mais perto de:

\[ 7 \]

Então:

$\boxed{\lim_{x\to3}(2x+1)=7}$

Agora trazendo isso para automação, imagine que \(x\) seja a posição de um cilindro e \(P(x)\) seja a pressão em função da posição.

Suponha que queremos analisar:

$\lim_{x\to1000}P(x)$

Isso quer dizer:

> quando o cilindro se aproxima da posição de $(1000\text{ mm})$, para qual pressão o sistema está tendendo?

Talvez exatamente em $(1000\text{ mm})$ alguma lógica mude, uma válvula comute ou o controle troque de estágio. Mesmo assim, podemos estudar **o comportamento imediatamente antes e depois daquele ponto**.

É aí que entram os limites laterais:

$\lim_{x\to1000^-}P(x)$

significa:

> pressão se aproximando de \(1000\text{ mm}\) pelo lado de posições menores.

E:

$\lim_{x\to1000^+}P(x)$

significa:

> pressão se aproximando de \(1000\text{ mm}\) pelo lado de posições maiores.

Se os dois lados chegam no mesmo valor:

$\lim_{x\to a^-}f(x) = \lim_{x\to a^+}f(x)$

então existe o limite:

$\boxed{\lim_{x\to a}f(x)=L}$

Se chegam a valores diferentes, o limite não existe.

Isso aparece muito bem naquela função que você estudou:

$f(x)=\frac{|x|}{x}$

Para \(x>0\):

$f(x)=1$

então:

$\lim_{x\to0^+}\frac{|x|}{x}=1$

Para \(x<0\):

$f(x)=-1$

então:

$\lim_{x\to0^-}\frac{|x|}{x}=-1$

Como:

$-1\neq1$

temos:

$\boxed{\lim_{x\to0}\frac{|x|}{x}\text{ não existe}}$

Isso é parecido com observar um sinal de controle próximo de uma transição: de um lado ele tende para um valor e do outro lado tende para outro. Não existe uma única tendência no ponto.

A ideia também explica por que limites são fundamentais para derivadas. A derivada usa:

$\frac{f(x+h)-f(x)}{h}$

Essa expressão calcula uma taxa de variação entre dois pontos. Só que queremos que esses dois pontos fiquem praticamente juntos:

$h\to0$

Então usamos um limite:

$f'(x) = \lim_{h\to0} \frac{f(x+h)-f(x)}{h}$

Ou seja:

$\boxed{\text{a derivada nasce de um limite}}$

Em linguagem de automação, você pode pensar assim:

$\boxed{\text{Limite = para onde o sinal está indo}}$

e:

$\boxed{\text{Derivada = com que rapidez o sinal está mudando}}$

Por exemplo, para a posição de um cilindro:

$x(t)$

um limite pode perguntar:

$\lim_{t\to5}x(t)$

> Onde a posição está chegando quando o tempo se aproxima de 5 s?

Já a derivada:
$\frac{dx}{dt}$

pergunta:

> Qual é a velocidade do cilindro naquele instante?

Essa diferença é a que eu quero que você guarde:

$$\boxed{\text{Função: qual é o valor?}}  \boxed{\text{Limite: para qual valor está tendendo?}} \boxed{\text{Derivada: com que rapidez esse valor está mudando?}}$$

E mais uma coisa importante: **\(x\to3\) não significa necessariamente \(x=3\)**. Significa que estamos tomando valores cada vez mais próximos de \(3\). Essa distinção explica boa parte das questões aparentemente estranhas de limites.