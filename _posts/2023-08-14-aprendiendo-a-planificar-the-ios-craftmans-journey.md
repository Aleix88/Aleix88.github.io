---
layout: single
title:  "Aprendiendo a planificar"
---

Bienvenidos al primer post de “The iOS Craftman’s Journey”. Como ya dije en el blog introductorio anterior, conforme vaya adquiriendo conocimiento, mediante el curso de *iOS Lead Essentials* y otros libros que vaya leyendo, quiero empezar a ponerlos en práctica y compartir mi progreso con vosotros.

En este post plantearemos una app a partir de una idea muy sencilla, y haremos una planificación inicial usando diferentes estrategias.

# Idea básica de la aplicación

La aplicación que quiero crear tendrá que ver con libros. Últimamente estoy leyendo muchos libros, tanto novelas (alguien más esta enganchado a *Brandon Sanderson*?), como libros más técnicos. Quiero una aplicación que me permita buscar libros y marcarlos como leídos. Conforme vaya marcando los libros como leídos quiero que me aparezca una pantalla con el listado de estos.

Esta sería la idea inicial, aunque no descarto añadir otras funcionalidad mas adelante. Por lo tanto, tengo que programar una app que sea flexible y admita futuros cambios (sin que sea una odisea).

> Una buena arquitectura ofrece soluciones simples y elegantes al problema a resolver. Es clave encontrar el balance entre solucionar el problema planteado y prepararse para posibles cambios.
> 

# Especificación de requerimientos

El primer paso para un buen diseño es tener claros los requerimientos. Si estos no se especifican bien, los desarrolladores (y otros trabajadores del proyecto) empezarán a hacer malas asunciones sobre lo que creen que se debe implementar, y ya sabemos como acabará un proyecto así.

> Bad software starts with bad assumptions.
> 

Especificar los requerimientos es especialmente útil sobretodo cuando estas trabajando en equipo. Tanto las historias de usuario como los casos de uso, pueden ser escritos por los analistas, o en conjunto con los desarrolladores. Dependiendo de quién los escriba podemos esperar que sean más o menos técnicos. Si estas trabajando solo, es tu elección decidir si hacer una especificación formal o usar una simple to-do list. En mi caso prefiero la especificación formal por dos motivos. Primero, para acabar de aterrizar la idea que tengo en mente pero no definida al 100%. Segundo, poner en práctica el método, ver si me es fácil o no, y entender las ventajas que me ofrece (spoiler: me han surgido bastantes dudas durante el proceso y a resultado más confuso de lo que esperaba).

## Behaviour Driven Development (BDD)

Empezaremos definiendo los comportamientos que debe tener la app desde la perspectiva del usuario (*Behaviour Driven Development o BDD*). Cada definición de comportamiento seguirá la siguiente estructura:

| Narrative: Descripción de la acción a realizar por el usuario. |
| --- |
| Scenarios: El/los criterios de aceptación. |

Durante la especificación de las historias, tendré que vigilar de no hacerlas demasiado técnicas, evitando nombrar BBDD, UI, o otros detalles específicos de la infraestructura. Empecemos:

![Narrative-1](/resources/2023-08-14-narrative-1.png)

![Narrative-2](/resources/2023-08-14-narrative-2.png)

![Narrative-3](/resources/2023-08-14-narrative-3.png)

---

Estas serían las historias de usuario basadas en mi idea inicial. Como se puede apreciar, he pasado de algo muy abstracto a algo un poco más concreto. Por ejemplo, esto deja claro que para buscar libros será necesario que el usuario tenga conexión (mediante algún tipo de API), pero esta conexión no será necesaria para obtener la lista de libros leídos (necesitaré crear una BBDD local).

Obviamente, en un proyecto individual esto pierde valor (ya que inicialmente ya tenía pensado cómo hacer las cosas). Aún así, hay que remarcar que, en un proyecto real este proceso puede resultar muy enriquecedor, ya que cuando hay varias mentes pensando a la vez (sobretodo si son de diferentes ámbitos) tendemos a tener ideas distintas que pueden llevar resultados también distintos.

Al finalizar las historias de usuario, me he dado cuenta que aún hay cosas que no están del todo definidas, como por ejemplo, los datos a mostrar de cada libro. Realmente, no creo que haya una teoría que diga exactamente cuándo has especificado muy poco o demasiado (y si la hay tampoco creo que tengamos que grabarla en piedra). Al final, esto no es más que una herramienta para conseguir alinearte con el equipo, si tu método funciona, úsalo, si no, busca una alternativa.

## Casos de uso

Otra alternativa a BDD son los casos de uso. Aunque también puede ser visto como el siguiente paso de BDD, donde conseguimos especificar un poco más.

La ventaja de los casos de uso es, que si se hace correctamente, muchos de ellos se podrán traducir a código fácilmente. Nos pueden servir para guiar nuestros tests cuando hacemos *TDD o Test Driven Development*.

Una posible forma de definirlos sería la siguiente:

| Data | Es el input, los datos necesarios para poder realizar la acción. |
| --- | --- |
| Primary Course | Es una enumeración de los pasos a seguir para realizar el conocido como happy path. |
| Error course | Lo mismo pero para el sad path. Puede haber más de uno. |

Empecemos entonces a definir esos casos de uso:

![Load books list from remote use case](/resources/2023-08-14-use-case-1.png)

![Load books list from remote use case](/resources/2023-08-14-use-case-2.png)

![Load books list from remote use case](/resources/2023-08-14-use-case-3.png)

![Load books list from remote use case](/resources/2023-08-14-use-case-4.png)


# Conclusiones

Con esto ya hemos definido todos los requerimientos de la app. 

Cosas a destacar que me han surgido o he visto durante el proceso:

- Al estar trabajando como persona única es muy fácil que los casos de uso acaben siendo demasiado técnicos. De hecho, he tenido que volverlos a escribir varias veces porque los estaba complicando con detalles de la BBDD que no eran necesarios en esta etapa.
- Un proceso de especificación tan detallado pierde valor en un proyecto individual (sobretodo tan sencillo como este).  Aún así me ha ayudado a aterrizar un poco más el concepto y ver puede ser muy útil para alinear ideas entre personas que piensan de forma distinta.
- Se puede empezar a intuir cómo gracias a los casos de uso, es más probable que la aplicación cumpla los principios SOLID y siga buenas prácticas, ya que cada caso de uso tiene un objetivo muy claro y acotado, dando pie a soluciones simples y elegantes.

En todo caso me he dado cuenta que me falta mucha más practica y más conocimiento en esta área. Tengo dudas decidiendo si crear un caso de uso o no para ciertas funcionalidades. Me han recomendado dos libros sobre este tema que seguramente dedicaré un tiempo a leer (si me recomiendas alguno más estaré encantado de leerlo): 

- *Writing Effective Use Cases by Alistair*
- *Object-Oriented Software Engineering: A Use Case Driven Approach by Ivar Jacobson.*

Esto a sido todo por este artículo. Me gustaría saber que pensáis sobre las historias de usuario y casos de uso. Hay alguno que eches de menos o alguno que crees que sobra? 

Hasta la próxima! 🙂