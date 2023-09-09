---
layout: single
title:  "Introducción a targets, schemes y test plans en Xcode"
---

Cuando empiezas a desarrollar para iOS te centras en aprender sobre Swift, UIKit, y las cuatro funcionalidades básicas de Xcode para poder ejecutar tu app. Lo mas probable es que siempre empieces con proyectos en blanco y no toques mucha cosa más.

Inevitablemente, algún día, te tienes que enfrentar a los targets, schemes o tests pero tirarás de alguna respuesta de *stackoverflow*, sin entender mucho lo que estas haciendo, para luego poder seguir como si los targets o schemes no existieran.

En el artículo de hoy haremos una pequeña introducción a los *targets*, *schemes* y *tests plans*, que son y como los podemos usar para organizar nuestros proyectos.

# Targets

Los targets son todos aquellos productos que Xcode puede compilar (apps, frameworks, tests, etc). Cuando creas un proyecto en blanco, normalmente te creará entre uno y tres targets por defecto: un target para tu aplicación iOS (por ejemplo) y otro target para los *unit tests* y otro para los tests de UI.

Si haces click en tu proyecto, verás que a la izquierda aparece un listado de todos los *targets* que contiene. Abajo del todo hay un simbolo “+” desde el cual puedes añadir nuevos *targets*.

![Targets in Xcode](/resources/2023-09-02-targets-list.png)

Te pongo varios ejemplos de *targets* que podrías tener:

- Un *target* para *tests end-to-end*. Son aquellos tests que prueban el recorrido entero del flujo de datos hasta el servidor que contiene la API. Como son tests que suelen ser lentos, nos interesa separarlos del target de *Unit Tests*, de esta manera podremos elegir ejecutarlos de forma separada.
- Un *target* para iOS y otro para macOS. Imagina que estas haciendo una app multiplataforma, pero las dos plataformas tienen código en común. Podrías tener un target para iOS, otro para la app de macOS y otro target con el framework que contenga el código en común, todo esto en el mismo proyecto (o no).

Aparte de los típicos *targets*, tienes muchos más. Por ejemplo, si quisieras hacer un **App Clip**, esto también sería un *target*.

# Schemes

Los *schemes* son configuraciones que le dicen a Xcode como hacer varias acciones (*build, test, profile, run, archive y analyze*) para cada *target*. Puedes tener más de un *scheme* por *target* (como un *scheme* para *debug* y otro para *release*), o un *scheme* que trabaje con varios targets a la vez.

Para entender bien como jugar con los *schemes* veamos que significan las acciones mencionadas anteriormente:

- **Build**. Acción de compilar.
- **Run**. Acción de ejecutar en el simulador.
- **Test**. Acción encargada de ejecutar los tests.
- **Profile**. Acción que ejecuta tu app usando *Instruments*. Si no conoces los *instruments*, son un conjunto de herramientas que Xcode ofrece a los desarrolladores que permiten analizar el uso de memoria, CPU, entre otras muchas cosas.
- **Analyze**. Acción que analiza si tu app esta libre de *memory leaks* y otros problemas. Es una opción lenta y que consume bastantes recursos, así que no siempre nos interesará tenerla activada (según el *scheme*).
- **Archive**. Acción encargada de generar el archivo con tu *target* compilado.

Así pues, podrás configurar cada *scheme* para ejecutar cualquier acción de las descritas, configuradas como desees, en los diferentes *targets* que tienes configurados.

Antes de Xcode 11, se solía crear un scheme para cada tipo de test que querías ejecutar. Es decir, un scheme para *unit tests*, otro para *tests end-to-end*. También era común crear un *scheme* para las herramientas de CI/CD, que se encargara de ejecutar todos los tipos de tests juntos. Actualmente ya no es 100% necesario usar schemes para esto, sino que puedes usar los **test plans** (hablaremos en el siguiente apartado).

Normalmente cada vez que creas un *target* se crea un *scheme* con él. Esto a veces nos puede interesar pero otras muchas veces no (es muy común ver proyectos con muchisimos schemes de los cuales solo se usan tres). Para desactivar esta opción tienes que hacer **click en el scheme actual → Manage schemes → Autocreate schemes**. Desde ahí también podras eliminar o editar todos los que quieras.

![Manage schemes Xcode](/resources/2023-09-02-manage-schemes.png)

# Test plans

Los *test plans* son básicamente archivos JSON con la extension `.xctestplan` que configuran todos los tests a ejecutar para ese plan.

Para crear un test plan tienes que ir a **Product → Test Plan → New test plan**. Una vez hagas esto en el project navigator aparecerá el archivo creado. Si pulsas encima verás una pantalla tipo esta:

![Test plan Xcode](/resources/2023-09-02-testplan.png)

Al crear un *test plan*, esta pantalla te aparecerá vacía. Si haces click en el “+” que aparece abajo te dejará seleccionar que *tests target* quieres añadir. En mi caso he añadido los *Unit Tests* y los tests de UI.

Lo interesante es que puedes configurar que tests individuales ejecutar de cada *target*. Si despliegas la flechita que aparece al lado del *check de enabled* del *target*, se desplegarán todos los tests que contiene y podrás activar/desactivar los que quieras.

Aparte de esto tienes la pestaña de **Configurations.** Por cada configuración que tengas, el conjunto de tests que has añadido en la pestaña de “Tests”, se ejecutarán una vez. Es decir, si tienes un *target* de tests de UI y otro de *Unit Tests* añadidos, y tienes 3 configuraciones (sin contar las *shared settings*), en total se ejecutarán 3 veces cada *target* de tests.

![Test plan configuration xcode](/resources/2023-09-02-test-plan-config.png)

Hay varias configuraciones interesantes que puedes tocar, te anímo a que les eches un vistazo y vayas jugando con ellas para entenderlas bien.

Lo bueno de los *tests plans* es que puedes tener varios configurados para un solo *scheme*. Para poder editar los *tests plans* asociados a un scheme, tenemos que hace click en la **pestaña de los schemes → Edit Scheme → Test**. Entonces veremos la siguiente pantalla:

![Edit scheme test action Xcode.](/resources/2023-09-02-edit-scheme-tests.png)

Como puedes ver este scheme solo tiene asociado un *test plan*, pero podriamos añadir todos los necesarios. Para elegir que scheme ejecutar, lo podemos elegir en **Product → Test Plan → NombreDelTestPlan**. Una vez seleccionado, con `CMD+U` se ejecutará.

Todo esto también lo puedes indicar si ejecutas tu proyecto desde la terminal:

`xcodebuild build test -scheme "MyTestProject" -testPlan "TestPlan”`

En este caso le decimos a Xcode que compile y luego ejecute los tests del *target* “MyTestProject” usando el *test plan* “TestPlan”. Si no se indica el flag “-testPlan” se ejecuta el que esta marcado como por defecto en la configuración del *scheme*.

# Conclusión

Esto ha sido una breve introducción a los *targets*, *schemes* y *test plans*. Te invito a que crees un proyecto vacio y empieces a jugar con las diferentes opciones y veas en directo el resultado que te dan. Esto es la típica cosa que no le pillarás el truco del todo hasta que no lo practiques durante un día.

Espero al menos que le hayáis perdido un poco el miedo a todas estas configuraciones.

Hasta la próxima!

# Referencias

- [https://developer.apple.com/documentation/xcode/configuring-a-new-target-in-your-project](https://developer.apple.com/documentation/xcode/configuring-a-new-target-in-your-project)
- [https://developer.apple.com/documentation/xcode/organizing-tests-to-improve-feedback](https://developer.apple.com/documentation/xcode/organizing-tests-to-improve-feedback)
- [https://developer.apple.com/documentation/xcode/configuring-your-xcode-cloud-workflow-s-actions](https://developer.apple.com/documentation/xcode/configuring-your-xcode-cloud-workflow-s-actions#)
- [https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project](https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project)
- [https://useyourloaf.com/blog/xcode-test-plans/](https://useyourloaf.com/blog/xcode-test-plans/)
- [https://www.swiftlyrush.com/creating-test-plans-in-xcode/](https://www.swiftlyrush.com/creating-test-plans-in-xcode/)