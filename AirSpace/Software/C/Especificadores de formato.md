| Especificador | Tipo de dado                    | Exemplo de uso       | Saída esperada |
| ------------- | ------------------------------- | -------------------- | -------------- |
| `%d`          | `int` (decimal com sinal)       | `printf("%d", -42);` | `-42`          |
| `%i`          | `int` (igual ao `%d`)           | `printf("%i", 42);`  | `42`           |
| `%u`          | `unsigned int`                  | `printf("%u", 42);`  | `42`           |
| `%o`          | `unsigned int` (octal)          | `printf("%o", 42);`  | `52`           |
| `%x`          | `unsigned int` (hex minúsculas) | `printf("%x", 255);` | `ff`           |
| `%X`          | `unsigned int` (hex maiúsculas) | `printf("%X", 255);` | `FF`           |

| Especificador | Tipo de dado           | Descrição                                     |
| ------------- | ---------------------- | --------------------------------------------- |
| `%f`          | `float` ou `double`    | Número com ponto fixo (ex: `3.14`)            |
| `%F`          | `float` ou `double`    | Igual ao `%f`, mas com `INF`/`NAN` maiúsculos |
| `%e` / `%E`   | `float` ou `double`    | Notação científica (`1.23e+02`)               |
| `%g` / `%G`   | `float` ou `double`    | Usa `%f` ou `%e` dependendo do valor          |
| `%c`          | `char`                 | Um único caractere (`A`)                      |
| `%s`          | `char*`                | Uma string (`"Hello"`)                        |
| Especificador | Tipo de dado           | Descrição                                     |
| `%p`          | Ponteiro genérico      | Ex: `0x7ffde89320b0`                          |
| Especificador | Significado            |                                               |
| `%%`          | Imprime um `%` literal |                                               |
# Especificador de largura

Um numero colocado entre o símbolo % e o código de formato como um `especificador de largura mínima de campo`
isso preenche a saída com espaços. Por exemplo `%05d` preenchera um numero de menos de cinco dígitos com 0s,de forma que seu comprimento total seja de cinco:

```
void main(void){
double item;

item = 10.12304

printf("%f\n", item) <- 10.123040
printf("10%f\n", item) <- 10.123040
printf("012%f\n", item) <-00010.123040
}
```

# Especificador de Precisão