# Programación Dinámica / Dynamic Programming

En este texto vamos a ver rápidamente en qué consiste la técnica de resolución de problemas de Programación Dinámica. Esta técnica es muy importante ya que se puede utilizar para resolver una gran cantidad de problemas en el mundo real, especialmente problemas de optimización. Por ejemplo, uno de los problemas de optimización más conocidos es el de elegir el plan de ejecución de una consulta en una base de datos. Esto se resuelve habitualmente en **todos** los Sistemas de Administración de Bases de Datos utilizando programación dinámica mas ciertas heurísticas.

## ¿Qué es?

Es una técnica que consiste en identificar un determinado subproblema del problema principal para utilizarlo en la solución de ese problema principal. Es decir, en lugar de estar repitiendo el cálculo de una solución varias veces, calculamos esa solución una sola vez, la memorizamos en una estructura de datos y la utilizamos más adelante. Esto se utiliza en muchos problemas, especialmente en:

1. Contar algo, número de formas de hacer algo en general:
   * contar el número de formas de resolver un problema, por ejemplo de cuántas formas puedo subir una escalera (suponiendo que puedo subir escalones de uno en uno o de dos en dos)
   * cantidad de caminos desde un vértice de un grafo hacia otro

2. Optimizar algún valor (minimizar o maximizar un valor como un camino más corto con pesos)

3. Preguntas si/no (¿existe una suma de un subconjunto igua a X?)

Las opciones 2 y 3 es posible resolverlas utilizando un algoritmo Greedy o con backtracking, sin embargo esas estrategias no garantizan el obtener la solución correcta. Pueden obtenerla, pero no el 100% de las veces, con lo que para los casos erróneos hay que utilizar Programación Dinámica. Por ejemplo, para encontrar el camino más corto en un grafo dirigido con pesos en las aristas, es fácil encontrar un contraejemplo de esto.



De todas formas, la regla principal es siempre pensar en el subproblema, o preguntarse **qué necesito saber en este momento para poder resolver el problema actual**. Hay que pensar cuál es estado actual y cómo hemos llegado a este estado para poder resolver el problema.



## Formas de aplicar Programación Dinámica

Hay dos formas principales de aplicar programación dinámica a un problema: de forma recursiva con *memoization* y de forma iterativa. Ahora veremos estas dos formas y sus ventajas y desventajas.



### Programación Dinámica usando recursión

La idea general es usar backtracking y algún tipo de memoization para resolver los subproblemas del problema principal. Utilizaremos una función recursiva a la cual le pasaremos una estructura que vaya memorizando los subproblemas resueltos hasta el momento.



### Programación Dinámica usando un algoritmo iterativo

La idea general es utilizar un array o una estructura de datos similar para guardar un estado concreto del problema y poder utilizarlo más adelante, sin utilizar una función recursiva. Utilizaremos uno o más bucles para ello.

En la siguiente tabla hay un resumen de las características principales de estas dos opciones.



| Recursivo                                                    | Iterativo                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Más fácil de aplicar<br />Menos estados<br />No importa el orden | Más rápido<br />Menos complejidad<br />Posibilidad de utilizar estructuras complejas |

### Ejemplo: Fibonacci

El ejemplo clásico de cómo aplicar programación dinámica es resolver la secuencia de Fibonacci para un número determinado. Fibonacci se calcula de la siguiente forma: ```Fib(n) = Fib(n-1) + Fib(n-2)```

La solución naïve al problema es calcular de forma recursiva ```Fib(n-1)``` y ```Fib(n-2)``` sin embargo esto es claramente ineficiente ya que la complejidad llega a ```2^n```. Utilizaremos programación dinámica  para optimizar el algoritmo. Y la pregunta que debemos hacernos es, ¿qué necesitamos saber en el estado **i** para poder resolver el problema? Como la propia definición de Fibonacci nos indica, necesitamos conocer ```Fib(i) = Fib(i-1) + Fib(i-2)```. Veamos las dos implementaciones:

#### Fibonacci recursivo

```c++
int fib(int n, int[] prev){
  if(prev[n] != -1){
    return prev[n];
  }
  int result = 0;
  if(n <= 2){
    prev[n] = 1;
    return 1;
  } else{
    result = fib(n-1, prev);
    prev[n] = 1;
  }
  return result;
}
```

En el ejemplo anterior simplemente pasamos el array que contiene los resultados calculados previamente. El uso de ese array se le llama *memoization*.

#### Fibonacci iterativo

En el siguiente ejemplo vemos cómo se implementa la solución a Fibonacci utilizando una función iterativa. Simplemente se accede al mismo array anterior desde la función.

```c++
int fib(int n){
  int fib[n];
  fib[0] = 0;
  fib[1] = 1;
  for(int i = 2; i < n; i++){
    fib[i] = fib[i-1] + fib[i-2];
  }
  return fib[n];
}
```

En este último ejemplo la **complejidad de espacio** pasa de exponencial a constante/lineal. Es fácil  ver que en realidad sólo necesitamos los dos últimos elementos del array *fib* en el ejemplo recursivo, y por tanto el espacio utilizado es constante. En cuanto a la complejidad de ejecución también es constante O(N).

Hay que resaltar que el orden es muy importante en programación dinámica iterativa, ya que el problema i depende de la solución del problema i-1 y esto es lo más difícil de DP. Sin embargo, cuando elegimos ese orden, todo sale mucho más fácil, incluyendo un array para guardar los pasos anteriores (los estados anteriores).

### Ejemplo conteo de escalones

Es este problema tenemos que subir una escalera con N escalones, estos escalones los podemos subir de 1 en 1 o de 2 en 2. ¿Cuántas formas distintas hay de subir estos escalones? Dibujemos el problema:

|      |      |      | 4    |
| :----: | :----: | :----: | :----: |
|      |      | 3    |      |
|      | 2    |      |      |
| 1    |      |      |      |

Es decir, para poder llegar al escalón 3 podemos pasar del 1 al 3 subiendo dos escalones de una vez o podemos subir del 1 al 2 y después del 2 al 3, por lo tanto hay dos formas de llegar al escalón 3. **Lo que debemos preguntarnos es ¿qué es lo más importante que debemos saber en este (o cada) momento?** En este problema lo que nos importa es de cuántas formas hemos llegado al escalón ```i```. Sólo hay dos posibilidades para llegar al escalón ```i```, que son llegar desde el escalón ```i-1``` o desde el escalón ```i-2```. Entonces, llegar al escalón consiste en ```pasos[i] = pasos[i-1] + pasos[i-2]```, lo mismo que en Fibonacci.

### Ejemplo camino mínimo

En este ejemplo tenemos que encontrar el camino más *barato* para llegar desde la posición en (0,0) a la (3,3) en la siguiente tabla, siendo que sólo podemos movernos hacia la derecha o hacia abajo:

|  5  |  2 | 2 | 3 |
| :--: | :--: | :--: | :--: |
| 1 | 9  | 1 | 7 |
| 4 | 2 | 4 | 8 |
| 9 | 15 | 2 | 1 |

En este problema lo que debemos hacer es encontrar el camino óptimo. Podemos darnos cuenta de que si utilizamos un algoritmo greedy desde (0,0) iríamos hacia (0,1), (0,4), (0,9), (1,15),...,(4,4). Claramente el camino no es el óptimo ya que habría que ir por (1,2),...,(4,4).

Para resolver este problema debemos utilizar programación dinámica y debemos preguntarnos, cuando estamos en la posición (i,j) **¿qué es lo más importante que debemos saber en este instante?** Para llegar a (i,j) sólo podemos hacerlo desde la posición (i-1,j) o desde la posición (i, j-1) según la definición del problema. Entonces necesitamos un array de dos dimensiones ```dp[i][j] = coste``` para guardar el coste de llegar a esa posición, ```dp[i][j] = dp[i][j] + min(dp[i-1][j], dp[i][j-1])```.

## Conclusiones

Programación Dinámica es un método para resolver problemas el cual se basa en identificar los subproblemas que componen el problema actual, y utilizar las soluciones a esos subproblemas para resolverlo. La idea es preguntarnos, **¿qué debemos saber para resolver el problema en este punto?**. Una vez tengamos esto claro podremos elegir la implementación más adecuada, aunque en general se recomienda utilizar la implementación iterativa ya que permite mayor flexibilidad en cuanto a estructuras de datos a utilizar y eficiencia.
