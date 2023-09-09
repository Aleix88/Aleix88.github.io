---
layout: single
title:  "Diferencias entre Factory Method y Abstract Factory"
---

Estos días he estado leyendo el libro **Dependency Injection in .NET by Mark Seemann**. En un punto del libro habla de usar el patrón **Abstract Factory**. Cuando vi que nombraba ese patrón, me asaltó a la mente la pregunta: ¿Cuál es la diferencia entre *Abstract Factory* y *Factory Method*?

Si no te lo miras detenidamente es muy fácil confundirlos, tienen estructuras muy parecidas y dependiendo de donde te informes encontrarás implementaciones bastante distintas. Todo esto hace que sea complicado entender las diferencias.

En este post me he marcado como objetivo ayudar a esas mentes confundidas como yo y resolver el misterio definitivamente.

<aside>
💡 Los ejemplos de código en este artículo están en swift, pero el concepto es extrapolable a otros lenguajes.
</aside>

## Qué es una factory o factoría?

Una factoría maneja la creación de diferentes **tipos concretos** que tienen un padre (ya se otra clase o interfaz) en común. Busca retrasar la toma de esta decisión (que tipo concreto crear) hasta estar en tiempo de ejecución, donde tendremos más información para poder tomarla.

Pongamos un ejemplo. Imagina que tienes una entidad **Vehículo**. Este vehículo podría ser **Coche** o una **Moto**(Vehículo podría ser tanto una clase como una interfaz, no afectaría al patrón en si). 

![Diagrama sin título.drawio.png](/resources/2023-07-29-vehicle-interface.png)

Aquí se podrían dar dos casos:

- Puede que en tu código puedas decidir directamente en que casos te interesará trabajar con un coche o una moto, y lo puedas hardcodear.
- Puede que esta decisión dependa de un valor que aún no conozcas (por ejemplo: la respuesta de una petición a un servidor, el input de un usuario, etc), entonces, no podrás hardcodear la clase. Aquí es donde entran en juego las factorías.

> Mucha gente habla del patrón Factory (sin especificar de cuál de los dos están hablando). Muchas veces se están refieriendo a Factory Method, otras a Abstract Factory. Otros no lo tienen claro, ya que no entienden la diferencia, y prefieren decir Factory para salir del paso.
> 

## Factory Method

Pongamos el ejemplo de un programa que trabaje con diferentes tipos de alimentos. Los tipos posibles serán **carne** o **pescado**. Tal que así:

```swift
// En Swift, un protocolo es el equivalente a una interfaz
protocol Food {
	var name: String { get set }

	func printDescription()
}

// Otra opción sería crear Food como una clase en vez de una interfaz,
// dependerá del contexto y de tus preferencias.

class Meat: Food {
	var name: String

	init(name: String) {
		name = name	
	}

	func printDescription() {
		print("Meat: \(name)")
	}
}

class Fish: Food {
	var name: String

	init(name: String) {
		self.name = name	
	}

	func printDescription() {
		print("Fish: \(name)")
	}
}
```

Como ves, cada alimento implementa su variación de la función *printDescription.* De esta forma podremos trabajar con la abstracción Food en toda la app pero cada tipo concreto tendrá su comportamiento único.

Ahora, imagina que en algún lugar de la app hay esta función, que se llama al hacer clic en un botón y queremos devolver un alimento en concreto según el tipo que nos llega:

```swift
func onClickButtonReturnFood(type: String) -> Food {
	if type == "Fish" {
		return Fish(name: "Tuna")
	} else {
		return Meat(name: "Chicken")
	}
}
```

Pues esto sería el patrón **Factory Method**, decidir en tiempo de ejecución entre diferentes tipos concretos que comparten un mismo padre o interfaz. La aplicación trabajará con la clase o abstracción Food, pero el comportamiento esta sobrescrito por cada tipo. 

Aún se podría haber complicado más, pero solo con esto ya hemos implementado el patrón Factory Method. Una de las posibles alternativas sería crear una clase “Factory” como tal. De esta forma podríamos aislar la creación de la instancia a una clase específica, tal que así:

```swift
class FoodFactory {
	func create(type: String) -> Food {
		switch type {
			case "Fish": 
				// Aquí podríamos hacer cualquier cálculo para saber el nombre,
				// o lo podríamos pedir por parámetros de la función create.
				// Lo hardcodeo con el fin de simplificar el ejemplo.
				return Fish(name: "Tuna")
			case "Meat": 
				return Meat(name: "Chicken")
			default: 
				// Devolver algún valor por defecto
		}
	}
}
```

Entonces en la función anterior lo podríamos usar así:

```swift
func onClickButtonReturnFood(type: String) -> Food {
	return FoodFactory().create(type: type)
}
```

Decidas como decidas implementarlo, las dos implementaciones siguen siendo formas válidas de implementar el patrón.

Resumiendo, los puntos claves del patrón Factory Method son:

- Se aprovecha el polimorfismo para retrasar la decisión del tipo concreto a instanciar hasta el tiempo de ejecución, sobrescribiendo los métodos o propiedades del padre según el tipo específico.
- La clase padre puede ser una interfaz o una clase, dependiendo del contexto. Si necesitas un un comportamiento base o default, te puede interesar usar una clase.
- Lo puedes implementar directamente con un condicional en tu código o puedes hacer una versión más ******fancy****** y crear una clase “Factory”.

## Abstract Factory

**Abstract Factory** no es más que otra vuelta de tuerca al patrón Factory Method.

Con Factory Method hemos podido decidir (en tiempo de ejecución) si instanciar Fish o Meat que eran sub-tipos Food. 

Con Abstract Factory hacemos zoom-out. Que pasaría si ademas de los tipos de comida, la comida en si estuviera dentro de otra familia. Imaginate que ahora nos dicen que nuestra aplicación tiene que soportar dos gamas de comida diferentes, la normal y la gourmet. Dentro de la aplicación se tendrán que comportar igual, lo que pasa es que los productos Gourmet tendrán un sello de calidad mayor que vendrá indicado en la descripción. 

El código sería el siguiente:

```swift
protocol Food {
	var name: String { get set }

	func printDescription()
}

class NormalMeat: Food {
	var name: String

	init(name: String) {
		name = name	
	}

	func printDescription() {
		print("Meat: \(name)")
	}
}

class NormalFish: Food {
	var name: String

	init(name: String) {
		self.name = name	
	}

	func printDescription() {
		print("Fish: \(name)")
	}
}

class GourmetMeat: Food {
	var name: String

	init(name: String) {
		name = name	
	}

	func printDescription() {
		print("(*GOURMET*) Meat: \(name)")
	}
}

class GourmetFish: Food {
	var name: String

	init(name: String) {
		self.name = name	
	}

	func printDescription() {
		print("(*GOURMET*) Fish: \(name)")
	}
}
```

Como vemos, ahora tenemos dos tipos de pescado y dos de carne. El patrón Factory Method ya no nos sirve para decidir que tipo crear, ya que ahora tenemos una agrupación dentro de otra agrupación.

Lo que nos permite Abstract Factory es tener diferentes tipos de factorías según la familia a la que pertenecen (en nuestro caso familia Gourmet o Normal). Es decir, tendremos una factoría para productos Gourmet y otra factoría para productos normales, que internamente funcionarán de la misma manera que en el ejemplo de Factory Method. 

Mediante una interfaz que abstraerá la factoría, podremos retrasar la decisión de que factoría usar, de forma similar a lo que hacíamos con Factory Method.

Vamos al código para verlo más claro:

```swift
protocol FoodFactory {
	func create(type: String) -> Food
}

class NormalFoodFactory: FoodFactory {
	func create(type: String) -> Food {
		switch type {
			case "Fish": 
				return NormalFish(name: "Tuna")
			case "Meat": 
				return NormalMeat(name: "Chicken")
			default: 
				// Devolver algún valor por defecto
		}
	}
}

class GourmetFoodFactory: FoodFactory {
	func create(type: String) -> Food {
		switch type {
			case "Fish": 
				return GourmetFish(name: "Tuna")
			case "Meat": 
				return GourmetMeat(name: "Chicken")
			default: 
				// Devolver algún valor por defecto
		}
	}
}
```

Ahora pongamos el caso anterior del botón, pero en este caso nos llegará la información del usuario y devolveremos la factoría:

```swift
func onClickButtonReturnFoodFactory(user: User) -> FoodFactory {
	if user.isGourmet {
		return GourmetFoodFactory()
	} else {
		return NormalFoodFactory()
	}
}
```

En otro lugar de la app podríamos usar la factoría devuelta de la siguiente manera (sin necesidad de conocer que tipo de FoodFactory es o que tipo de Food devuelve):

```swift
let food = factory.create(type: type)
```

Y esto sería el patrón Abstract Factory. Como podemos ver es similar a Factory Method pero añadiendo un agrupación en familias aparte de los tipos iniciales.

De la misma forma que en el Factory Method, podríamos encapsular el código de la creación de la factoría concreta en una clase llamada *FoodAbstractFactory* o similar, pero no deja de ser lo mismo. Me gustaría destacar la siguente nota sacada del libro *Dependency Injection in Net por Mark Seemann* (nombrado en la introducción):

> Any ABSTRACTION that creates instances of other ABSTRACTIONS is an Abstract Factory. It doesn't need to have a name that ends with Factory.
> 

## Conclusiones

Espero que después de leer este artículo entiendas realmente las diferencias entre los dos patrones. Al fin y al cabo, aplicarás Abstract Factory cuando veas que, según el problema que intentas resolver, Factory Method se queda limitado (por lo que decíamos antes de agrupaciones dentro de agrupaciones).

La próxima vez que alguien de tu entorno hable de el patrón Factory, preguntale a cual de los dos se refiere, a lo mejor descubres que no lo tiene claro y puedes aprovechar para explicarle el concepto y ver si lo has entendido de verdad 🙂 (y si no vuelve a leer el artículo 😂).

Hasta la próxima!