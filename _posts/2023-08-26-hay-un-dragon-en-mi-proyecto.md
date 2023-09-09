---
layout: single
title:  "Hay un dragón en mí software"
---

Hace un tiempo leí un cuento llamado *“There’s no such thing as a dragon”* de *Jack Kent*. El cuento trata de un dragón que aparece en la casa de una familia. Al principio del cuento el dragón es pequeño. Cuando el niño avisa a su madre sobre su presencia, la madre le dice que los dragones no existen. El dragón va creciendo a cada momento que pasa, y la conversación entre madre e hijo se repite. La madre nunca reconoce que el dragón esta ahí. Llega un momento que es tan grande que se lleva la casa volando. Finalmente, la madre no puede ignorar más el problema y reconoce que el dragón esta ahí, que existe. Al hacerlo, éste empieza a hacerse pequeño otra vez.

Los proyectos de software siempre tiene un dragón volando alrededor. Al principio es pequeño y no molesta a nadie. Cada vez que tomamos una mala decisión (porque nos resulta conveniente) o aplicamos malas prácticas, lo alimentamos y el dragón crece. Si nadie se atreve a reconocerlo a tiempo, se hará grande, hasta un punto que el proyecto sea incontrolable.

> Cuanto mas dejes crecer al dragón, más miedo te dará enfrentarte a él.
> 

En este artículo aprenderemos a como hacer frente al dragón de tu proyecto. A lo mejor te da miedo empezar a aplicar CLEAN, TDD o lo que sea. Por eso, en vez de escribir otro artículo enumerando los principios de CLEAN, lo he organizado en un conjunto de puntos que puedes aplicar para mejorar tu proyecto, para que puedas empezar poco a poco. El objetivo es que cada día mejores un poco sin llegar a paralizarte.

Así que dicho esto, vamos al lío!

# Identifica los detalles y no dependas de ellos

Los **detalles** son todo aquello que es volátil. Normalmente, son aquellas cosas que cuando empiezas el proyecto no están decididas del todo o que pueden cambiar con facilidad.

El software que estes haciendo nunca debería depender de detalles, de esta forma, tu proyecto no será vulnerable cuando estos cambien (que lo harán). Más bien serán como módulos que puedes conectar y desconectar de tu aplicación.

Algunos detalles que puedes empezar a identificar son:

- La **UI**. Si te paras a pensarlo, suele ser bastante volátil. Los diseñadores, cada 2x3 están buscando que cambiar para mejorar la experiencia de los usuarios (cosa que está muy bien). Sabiendo esto puedes preparar tu aplicación para que no dependa de la UI, sino que la UI dependa de los demás módulos. Esto también te dará flexibilidad por si quieres migrar entre UIKit o SwiftUI (en caso de iOS).
- **Frameworks**. Es muy típico empezar una aplicación diciendo: “Usaremos *Alamofire* para hacer las llamadas a la API y *Realm* para la cache en local”. Igual que la UI, si no haces tu software independiente a estos frameworks (o los que sean), luego cualquier cambio en estos va a romper el resto de la app. Me estoy imaginando aplicaciones donde los modelos (que viajan desde los servicios, hasta la UI, pasando por los módulos de network) están implementados con Realm. Una dependencia así puede hacer que tu código sea dificilísimo de testear, 0% modular y muy difícil de migrar a otra BD el día que se requiera (entre otras cosas).

El caso contrario a los detalles de la app serían las reglas de negocio, esas que difícilmente cambiarán. En tu aplicación, no todo serán o reglas de negocio o detalles, si no que habrá todo un umbral de niveles. Aquí es cuando aparece el típico gráfico de las capas de cebolla de la arquitectura CLEAN (te animo a que busques más sobre esto si te interesa).

Ser dependiente de estos detalles también complica que el trabajo se pueda paralelizar. Te obliga a que otros terminen su parte (API de backend, diseños de la UI) para poder empezar a la tuya.

**Y como se aplica todo esto en la práctica?**

Invirtiendo dependencias! Vamos a ver un ejemplo sencillo para entenderlo mejor:

![Inversión de dependencias.](/resources/2023-08-26-depencency-inversion.png)

En el diagrama de la izquierda, vemos que `LoadBookUseCase` (una clase encargada de devolver un listado de libros) usa un tipo concreto de cliente implementado con *Alamofire*. Esto genera una dependencia directa. La flecha grande indica el flujo de llamadas.

En el caso de la derecha, el cliente se abstrae mediante una interfaz llamada `NetworkClient`. `AlamofireNetworkClient` implementará esta interfaz y se inyectará a `LoadBookUseCase`. Si te fijas el flujo de llamadas (la flecha grande) seguirá siendo el mismo, pero la dependencia se ha invertido. Al caso de uso ya no le importa cuál es la implementación concreta, siendo totalmente independiente. Perfectamente podríamos tener la clase `AlamofireNetworkClient` en otro proyecto separado y sacar nuevas versiones sin afectar al resto de la app. Ahora también será mucho más facil de testear el caso de uso, ya que podrás crear un test double del `NetworkClient` y evitar tener que hacer llamadas reales a través de internet.

> Se llama “invertir dependencias” porque antes la flecha iba de un módulo de alto nivel (LoadBookUseCase) a uno de bajo nivel (AlamofireNetworkClient), y ahora va del módulo de bajo nivel (AlamofireNetworkClient) al módulo de alto nivel (LoadBookUseCase y NetworkClient). También podrías pensar, que se invierte la dependencia respecto al flujo del llamadas.
> 

# Piensa en casos de uso, no en pantallas

Cuando empezamos una app estamos demasiado pendientes a los diseños. Es normal, al final lo primero que se ve de una aplicación es la interfaz gráfica.

El problema es que, sobre el diseño, todo se aguanta muy bien, pero nos da una visión incompleta del problema a resolver.

Ademas, pensar así nos hace querer empezar a implementar primero la UI, y como hemos dicho en el punto anterior, puede ser peligroso ya que es bastante volátil.

> Una de las cosas principales que busca CLEAN es posponer la toma de decisiones todo lo que se pueda, consiguiendo así mayor flexibilidad.
> 

Pensar en casos de uso te hará hacerte más preguntas y te enfocará a entender mejor el problema que tienes que resolver. Puede que hasta encuentres alguna errata en el concepto y se pueda rectificar antes de empezar a programar.

Otra ventaja de empezar por los casos de uso es que, com hemos dicho, las reglas de negocio son bastante rígidas y es menos probable que después te encuentres con sustos.

Además, separar bien los diferentes conceptos de la funcionalidad a implementar, nos puede ayudar a paralelizar el trabajo.

# Da preferencia a lo simple ante lo fácil

Uno de los enemigos de una buena arquitectura es elegir siempre aquellas soluciones que son más convenientes en ese momento o fáciles de implementar.

**Por ejemplo**: en vez tener una interfaz para el network client y inyectar el tipo concreto dónde lo necesite, usaré mejor un *singleton*, que es mucho más fácil y lo puedo llamar desde cualquier sitio.

Tenemos que evitar a toda costa este tipo de acciones (aunque sea lo más fácil y rápido en ese momento). Si no lo hacemos, estas decisiones empezarán a complicarnos el proyecto a medida que se quieran añadir funcionalidades nuevas e irán apareciendo bugs difíciles de corregir.

> Aquello fácil no siempre resulta lo más simple a largo plazo.
> 

Otro problema oculto detrás de las malas decisiones es que facilita que otros programadores también tomen malas decisiones. Imagina que estoy escribiendo unos tests. Conforme estoy escribiendo cada test me empiezo a quedar sin tiempo y empiezo a nombrar métodos de forma poco autoexplicativa, saltandome todas las convenciones establecidas en el proyecto y las buenas prácticas. Cuando otro programador del equipo (un junior por ejemplo) vea los tests que he escrito, dará por hecho que, si ese código se llegó a mergear, seguramente sea correcto. Entonces procederá a implementar sus tests cogiendo los otros como ejemplo.

# No te atasques, empieza pequeño y luego escala

Una de las razones por las que la gente no aplique buenas arquitecturas es que no saben por dónde empezar. Te has leído varios libros de CLEAN architecture, dependency injection, patrones de diseño y piensas que estas listo, tienes le conocimiento de un dios de la programación. Vas a Xcode preparado para darlo todo, creas un proyecto en blanco y … te quedas en blanco. Pero no pasa nada, vas a draw.io porque primero tienes que hacer tu diagrama de clases. Empiezas a añadir clases, módulos, interfaces, aplicando todo lo que has leído. Al final has acabado con un diseño con 6 módulos, 30 clases, cuando lo único tiene que hacer tu aplicación es mostrar una TO-DO list en una tabla.

A mí esto me pasaba, y me sigue pasando (culpable 🤚). Una cosa que me ayuda a evitarlo es empezar pequeño, intentando no implementar nada que aún no necesite. Poco a poco, conforme la aplicación va creciendo, me voy preocupando de separar todo en módulos y voy refactorizando lo que necesite. Aplicar TDD te ayuda a empezar poco a poco, solo implementando aquello que necesitas cuando lo necesitas.

> Reconoce que no lo sabes todo, no intentes hacer un proyecto perfecto. Acepta el error como parte del proceso y no como un resultado aparte.
> 

Esto no significa que empiece el proyecto tomando cualquier decisión y gestionando mal las dependencias. En todo momento tengo en mente los principios mencionados, pero tengo claro que: algo es mejor que nada. **Hay que encontrar el balance entre, solucionar el problema y prepararnos para futuros cambios**.

# Conclusiones

Si te asusta empezar a aplicar CLEAN architecture de golpe en tu proyecto, ve poco a poco, aplica los principios mencionados y verás que las cosas empiezan a estar mejor. Simplemente proponte como objetivo empezar a tomar mejores decisiones y con esto el proyecto ya tomará otro rumbo, sin esperar cambiarlo todo el primer día.

En cuanto tengas la oportunidad empieza a informarte sobre TDD y ponlo en práctica poco a poco. Al principio seguramente te hará trabajar más lento, pero luego empezarás a encontrarle muchas ventajas.

Y recuerda que todo lo que haces sirve de ejemplo para otros miembros del equipo, así que tenlo en cuenta cuando tomes una decisión.

Espero que llegados a este punto tengas alguna herramienta mas para enfrentarte al dragón o al menos que hayas podido reconocer que está ahí.

Hasta la próxima!