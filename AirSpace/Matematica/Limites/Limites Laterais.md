Aqui é onde o conteúdo começa a ficar mais interessante.

Quando aparece:
$$
 \boxed{\lim_{x\to a^-}f(x)} 
$$
o sinal \(-\) quer dizer:

> \(x\) está se aproximando de \(a\) **pela esquerda**.

Ou seja, usamos valores menores que \(a\).

Por exemplo, se:
$$
 x\to3^- 
$$
podemos imaginar:
$$
 2,\quad2,9,\quad2,99,\quad2,999,\dots 
$$

$$
 \boxed{\lim_{x\to a^+}f(x)}
$$
significa aproximação **pela direita**.

Se:
$$
 x\to3^+ 
$$
podemos imaginar:
$$
 4,\quad3,1,\quad3,01,\quad3,001,\dots 
$$

# Limites laterais — aproximação pela esquerda

## Ideia principal

Quando temos:

$$
\lim_{x \to a^-} f(x)
$$

isso significa:

> \(x\) está se aproximando de \(a\) pela esquerda.

Ou seja, usamos valores menores que \(a\).

Exemplo:

$$
x \to 3^-
$$

Podemos pensar em:

$$
2,9,\quad 2,99,\quad 2,999,\ldots
$$

Já:

$$
x \to 0^-
$$

significa que usamos valores negativos cada vez mais próximos de zero:

$$
-0,1,\quad -0,01,\quad -0,001,\ldots
$$

---

# Exemplo

Calcular:

$$
\lim_{x\to0^-}\frac{1-x}{e^x-1}
$$

## 1. Analisar o numerador

Temos:

$$
1-x
$$

Quando:

$$
x\to0^-
$$

então:

$$
1-x\to1
$$

Portanto, o numerador se aproxima de:

$$
\boxed{1}
$$

e permanece positivo.

---

## 2. Analisar o denominador

Temos:

$$
e^x-1
$$

Como estamos usando valores negativos de \(x\):

$$
x<0
$$

temos:

$$
e^x<1
$$

Logo:

$$
e^x-1<0
$$

Quando \(x\) se aproxima de zero pela esquerda:

$$
e^x-1\to0^-
$$

Ou seja, o denominador se aproxima de zero por valores negativos.

---

## 3. Juntar as informações

Temos:

$$
\frac{1-x}{e^x-1}
$$

com:

$$
1-x\to1
$$

e:

$$
e^x-1\to0^-
$$

Então o comportamento é semelhante a:

$$
\frac{1}{0^-}
$$

Isso significa dividir um número positivo por um número negativo extremamente pequeno.

Por exemplo:

$$
\frac{1}{-0,1}=-10
$$

$$
\frac{1}{-0,01}=-100
$$

$$
\frac{1}{-0,001}=-1000
$$

Quanto mais o denominador se aproxima de zero pela esquerda, mais negativo fica o resultado.

Portanto:

$$
\boxed{
\lim_{x\to0^-}\frac{1-x}{e^x-1}=-\infty
}
$$

---

# Regra prática

Se:

$$
\frac{\text{positivo}}{0^+}
$$

então:

$$
+\infty
$$

Se:

$$
\frac{\text{positivo}}{0^-}
$$

então:

$$
-\infty
$$

Se:

$$
\frac{\text{negativo}}{0^+}
$$

então:

$$
-\infty
$$

Se:

$$
\frac{\text{negativo}}{0^-}
$$

então:

$$
+\infty
$$

---

# Resumo

$$
x\to a^-
$$

significa aproximação pela esquerda:

$$
x<a
$$

$$
x\to a^+
$$

significa aproximação pela direita:

$$
x>a
$$

Para:

$$
x\to0^-
$$

usamos valores como:

$$
-0,1,\;-0,01,\;-0,001
$$

Para:

$$
x\to0^+
$$

usamos valores como:

$$
0,1,\;0,01,\;0,001
$$

O sinal do denominador próximo de zero é fundamental para descobrir se o limite tende a:

$$
+\infty
$$

ou:

$$
-\infty
$$