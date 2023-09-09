---
layout: single
title:  "Anotaciones sobre el sistema de coma flotante"
---

En nuestros programas estamos todo el día trabajando con números. Cuando empecé a programar me explicarón que había varias formas de representarlos: Int para números enteros y Float o Double para números decimales. También te cuentan que la diferencia entre Float y Double es la precisión que ofrecen. 

A partir de ahí seguí programando siguiendo esas reglas básicas sin darle muchas más vueltas (ya que en el día a día no me resulta necesario).

En este artículo hablaremos sobre números y buscaremos entender un poco más como nuestro ordenador trata con ellos. Como siempre, los ejemplos en código estarán en Swift, pero los conceptos son extrapolables.

## Representación decimal y binaria

Vamos directamente a ver un ejemplo en código, quiero pienses el resultado que se va a printar:

```
var total: Double = 0

for _ in 0 ..< 10 {
    total += 0.1
}

print(total)
```

El resultado que seguramente esperaríamos todos es `1.0`, pero sorprendentemente el resultado real es `0.9999999999999999`.

Vamos con otro ejemplo:

```
let numberA: Double = 0.1
let numberB: Double = 0.2
let total: Double = numberA + numberB

print(total)
```

El resultado esperado es `0.3` pero recibimos un `0.30000000000000004`.

Porque tiene que ocurrir todo esto 😭? 

La respuesta es que, desde que somos pequeños, nos enseñan a pensar en números usando una representación decimal (base 10). En cambio a los ordenadores les es mucho mas fácil pensar en binario (base 2).

Una representación decimal nos permite representar fracciones tipo `1/10` como `0,1` de forma muy precisa. En cambio en binario no lo tenemos tan sencillo.

Antes de nada vamos a hacer un pequeño repaso de como representamos un número decimal en binario. Por ejemplo en número `21`:

![Número 21 a binario.](/resources/2023-09-09-decimal-to-binary.png)

Como vemos, simplemente vamos haciendo divisiones entre `2` elevado a un exponente.

Para representar numeros con decimales (con coma) en binario se hace lo mismo pero elevando `2` a exponentes negativos. De esta forma representar un `0.5` es tan fàcil como decir `2^-1`. Con otros números, como el `0.1`, no nos queda más remedio que redondear:

![Número 0.1 a binario](/resources/2023-09-09-1-entre-10-a-binario.png)

Entonces, como `0.1` no se puede representar de forma exacta en binario sin redondear, cada vez que hacemos una operación con este número acumulamos un error. Este error acumulado es la respuesta a los resultados que hemos visto en los ejemplos de antes.

Pero la representación con base 2 no es la única que tiene problemas, lo mismo nos pasa si intentamos representar `1/3` en base 10 que sería algo como `1.3333333...` (hasta el infinito). En cambio usando base 3 simplemente diríamos que es `3^-1` es decir `0.1` (en base 3).

Puede que te estes preguntando lo siguiente: Si el numero `4` en binario es `100`, como represento el `4.5`, se pueden poner comas en los números binarios? La respuesta es que si, podríamos poner una coma y representarlo como `100.1` (`100` para el 4 y `0.1` para el `0.5`). 

Para representarlo en un ordenador podríamos utilizar un sistema de "coma fija". Imaginemos que tenemos un tipo para representar números con coma fija de `32bits`. Con un sistema de coma fija, podríamos indicar que los primeros `16bits` son para la parte entera y los `16bits` restantes para la parte fraccionaria. Estaríamos dejando la coma justo en la mitad de forma fija. 

Esto no es muy eficiente. Imaginemos que estoy haciendo un programa que mide distancias entre casas de un vecindario. Si trabajo con metros como unidad seguramente no me importe la diferencia entre `1.0m` y `1.0001m`, por lo tanto estaré desaprovechando los bits de la parte fraccionaria. En cambio para un programa que mide la distancia entre átomos, serán los bits que representan la parte entera los que no voy a usar.

Como ves, con un sistema de coma fija estamos desaprovechando mucho espacio dependiendo de la magnitud de números con la que trabajemos. Cuál es la solución entonces? El sistema de coma flotante.

## Sistema de coma flotante

El sistema de coma flotante es la solución al problema planteado anteriormente, nos permite almacenar números en base 2 de forma muy eficiente.

Este sistema es muy parecido a la notación científica, así que vamos a pegarle un repaso primero a esta:

![Formula notación científica](/resources/2023-09-09-notacion-cientifica.png)

Como se puede observar la notación científica está representada por tres partes:

- **Coeficiente**. Es la parte significativa del número. Cuanto mas dígitos permitamos en el coeficiente más precisión podremos tener al representar nuestro número.
- **Base**. Las más comunes son base 10 y base 2, aunque como hemos dicho también podríamos usar base 3, por ejemplo.
- **Exponente**. El offset de la coma respecto el coeficiente.


Las ventajas de la notación científica son las mismas que tiene el sistema de coma flotante. Nos permite representar (de forma muy acotada) tanto números de magnitudes enormes(`1,32 * 10^24`), como números de distancias muy pequeñas (`1,32 * 10^-24`).

Pues más o menos de la misma forma se representan los números binarios en un ordenador. Pongamos el ejémplo de un Float (32 bits).

> La IEEE especifica la longitud que deben tener los Floats (32 bits) y los Double (64 bits), por eso es común en los diferentes lenguajes de programación. También especifican todo sobre su representación, si tienes curiosidad puedes buscar *IEEE 754*.

![Float bits structure](/resources/2023-09-09-float-structure.png)

Como podemos ver en la imagen anterior los bits de un float estan divididos en 3 partes, el signo (0 para positivo y 1 para negativo), el exponente (8 bits) y el coeficiente (23 bits). La base siempre es 2, ya que trabajamos con binario. Así pues, la formula de la coma flotante sería la siguiente:

![Formula coma flotante](/resources/2023-09-09-floating-point-formula.jpg)

Si queréis entender bien porque esa combinación de bits resulta el número `0.15625` os recomiendo que os leáis [está entrada de wikipedia](https://es.wikipedia.org/wiki/Formato_en_coma_flotante_de_simple_precisión), ya que no es tan directo como puede parecer. Por un lado el exponente viene sesgado en `127` (`(2^8)/2 - 1`). Por otro lado, el bit de mayor peso del coeficiente siempre se omite, pero está especificado como `1` por defecto, a menos que el exponente sea todo ceros, en ese caso se entiende como `0`. A partir de este primer bit omitido, `1` en este caso, añadimos una coma y le siguen el resto de bits del coeficiente. Es decir, en este caso podemos expresar el coeficiente en binario como `1.01` (`1.25` en decimal. El primer `1` es el `1` en decimal y el `.01` es `2^-2`, es decir, `0.25` en decimal.). Siguiendo la fórmula de coma flotante: `coeficiente * 2 ^ (exponente - 127) = 1.25 * 2 ^ (124 - 127) = 0.15625` 🥵.

Como hemos explicado al principio del artículo, al tener un número de bits limitado para el coeficiente nos encontraremos con muchos números que no se pueden representar. De hecho, una diferencia importante entre los Int y los números que se almacenan con coma flotante es que, la distancía entre un entero y el siguiente entero representable siempre es la misma (siempre es uno), en cambio con un Float o Double, la distancía entre un número y el siguiente representable no sigue una distribución uniforme.

Esta distancia mínima entre dos números representables es conocida como EPSILON. En Swift podemos acceder al EPSILON de un número usando la propiedad `.ulp` (Unit in the Last Place):

```
let numberA: Float = 1000000
print(numberA.ulp) // 0.0625
print(numberA.nextUp) // Devuelve el siguiente número representable que en este caso es 1000000.06
```

Si intentas representar un número entre `1000000` y `1000000.06` será redondeado (pruebalo si quieres 😜).

## Y si necesito máxima presición en mi programa?

Normalmente con los Floats y los Double nos podemos apañar sin llegar a que todo este problema de precisión sea un inconveniente. Ahora bien, en ocasiones muy específicas (sobre todo en programas que trabajan con dinero) nos interesará ser muy precisos en nuestros cálculos.

Para este caso, en Swift, tenemos un tipo llamado **Decimal**. Este tipo trabaja en base 10 en vez de base 2, lo que nos permite representar sin problema números como `0.1` pudiendo hacer cálculos más precisos. Tiene también sus inconvenientes así que no es simplemente una versión mejorada de los Double o Floats.

En las referencias dejo un artículo muy interesante donde se habla de las diferencias entre decimal y double.

## Referencias 

- [https://floating-point-gui.de](https://floating-point-gui.de)
- [https://www.jessesquires.com/blog/2022/02/01/decimal-vs-double](https://www.jessesquires.com/blog/2022/02/01/decimal-vs-double)





















