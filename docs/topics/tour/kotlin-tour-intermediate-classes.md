[//]: # (title: Intermediate Classes)

## Class inheritance

In the previous chapter, we covered how to extend classes without modifying the original source code. But what if you want
to have shared code between classes? In this case, class inheritance can help you with that.

In Kotlin, by default, classes can't be inherited. To make a class inheritable, use the `open` keyword before your class
declaration:

```kotlin
open class Vehicle
```

By having to explicitly configure your class for inheritance, this helps prevent unintended inheritance and makes 
your classes easier to maintain. By default, there aren't any subclasses that you need to worry about during your
development.

To create a class that inherits from another, add a colon after your class header followed by the name of the parent class
that you want to inherit from:

```kotlin
class Car : Vehicle
```
{validate="false"}

In this example, the `Car` class inherits from the `Vehicle` class:

```kotlin
open class Vehicle(val make: String, val model: String)

class Car(make: String, model: String, val numberOfDoors: Int) : Vehicle(make, model)

fun main() {
    // Creates an instance of the Car class
    val car = Car("Toyota", "Corolla", 4)
}
```

Just like when creating a normal class instance, if your class inherits from a parent class, then it must initialize
all the parameters declared in the parent class header. So in the example, the `car` instance of the `Car` class initializes
the parent class parameters: `make` and `model`.

Kotlin classes only support **single inheritance**, so it is only possible to inherit from **one class at a time**. Otherwise,
you will see an error:

```kotlin
open class Animal(val species: String)

open class Machine(val model: String)

// The RobotAnimal class tries to inherit from both the Animal class and the Machine class
class RobotAnimal(species: String, model: String) : Animal(species), Machine(model)
// Error: Only one class may appear in a supertype list
```
{validate="false"}

At the highest point in the Kotlin class hierarchy is the common parent class: `Any`. Ultimately, all classes inherit
from the `Any` class:

![An example of the class hierarchy with Any type](any-type-class.png){thumbnail="true" width="200" thumbnail-same-file="true"}

Similar to Data classes, the `Any` class provides some useful member functions automatically like the `.equals()` and 
`.toString()` functions. Therefore, you can use these inherited functions in any of your classes. For example:

```kotlin
open class Vehicle(val make: String, val model: String)

class Car(make: String, model: String, val numberOfDoors: Int) : Vehicle(make, model)

fun main() {
    //sampleStart
    val car1 = Car("Toyota", "Corolla", 4)
    val car2 = Car("Honda", "Civic", 2)
    
    // Uses the .toString() function via string templates to print class properties
    println("Car1: make=${car1.make}, model=${car1.model}, numberOfDoors=${car1.numberOfDoors}")
    println("Car2: make=${car2.make}, model=${car2.model}, numberOfDoors=${car2.numberOfDoors}")
    // Car1: make=Toyota, model=Corolla, numberOfDoors=4
    // Car2: make=Honda, model=Civic, numberOfDoors=2
    
    // Uses the .equals() function via the == operator to check if the make and model are the same
    val areMakeEqual = car1.make == car2.make
    val areModelEqual = car1.model == car2.model
    
    println("Car make is the same: $areMakeEqual")
    println("Car model is the same: $areModelEqual")
    // Car make is the same: false
    // Car model is the same: false
    
    //sampleEnd
}
```
{kotlin-runnable="true" id="kotlin-tour-any-class"}

### Overriding inherited behavior

If you want to inherit from a class but change some of the behavior, you can do so by overriding the inherited behavior.

By default, it's not possible to override a member function or property of a parent class. Just like with classes, you need
to add special keywords.

#### Member functions

To allow a function in the parent class to be overridden, use the `open` keyword before it's declaration in the parent class:

```kotlin
open fun displayInfo() {}
```
{validate="false"}

To override an inherited member function, use the `override` keyword before the function declaration in the child class:

```kotlin
override fun displayInfo() {}
```
{validate="false"}

For example:

```kotlin
open class Vehicle(val make: String, val model: String) {
    open fun displayInfo() {
        println("Vehicle Info: Make - $make, Model - $model")
    }
}

class Car(make: String, model: String, val numberOfDoors: Int) : Vehicle(make, model) {
    override fun displayInfo() {
        println("Car Info: Make - $make, Model - $model, Number of Doors - $numberOfDoors")
    }
}

fun main() {
    val car1 = Car("Toyota", "Corolla", 4)
    val car2 = Car("Honda", "Civic", 2)

    // Uses the overridden displayInfo() function
    car1.displayInfo()
    // Car Info: Make - Toyota, Model - Corolla, Number of Doors - 4
    car2.displayInfo()
    // Car Info: Make - Honda, Model - Civic, Number of Doors - 2
}
```
{kotlin-runnable="true" id="kotlin-tour-class-override-function"}

This example:
* Creates two instances of the `Car` class that inherit from the `Vehicle` class: `car1` and `car2`.
* Overrides the `displayInfo()` function in the `Car` class to also print the number of doors.
* Calls the overridden `displayInfo()` function on `car1` and `car2` instances.

<!-- Mention about final? -->

#### Properties

The syntax for overriding properties is exactly the same as for overriding member functions.

To allow a property in the parent class to be overridden, use the `open` keyword before it's declaration in the parent class:

```kotlin
open val transmissionType: String
```
{validate="false"}

To override an inherited member function, use the `override` keyword before the function declaration in the child class:

```kotlin
override val transmissionType: String
```
{validate="false"}

Properties can be overridden in the class header or in the class body.

<table header-style="top">
   <tr>
       <td>Class header</td>
       <td>Class body</td>
   </tr>
   <tr>
<td>

```kotlin
open class Vehicle(val make: String, val model: String, open val transmissionType: String)

class Car(make: String, model: String, val numberOfDoors: Int, override val transmissionType: String = "Automatic") : Vehicle(make, model)
```

</td>
<td>

```kotlin
open class Vehicle(val make: String, val model: String) {
    open val transmissionType: String
}

class Car(make: String, model: String, val numberOfDoors: Int) : Vehicle(make, model) {
    override val transmissionType: String = "Automatic"
}
```

</td>
</tr>
</table>

> If the property you want to override in the parent class has no default value, you must initialize it in the child class.
>
{type="note"}

For example:

```kotlin
open class Vehicle(val make: String, val model: String) {
    open val transmissionType: String
    open fun displayInfo() {
        println("Vehicle Info: Make - $make, Model - $model, Transmission Type - $transmissionType")
    }
}

class Car(make: String, model: String, val numberOfDoors: Int) : Vehicle(make, model) {
    override val transmissionType: String = "Automatic"
    override fun displayInfo() {
        println("Car Info: Make - $make, Model - $model, Transmission Type - $transmissionType, Number of Doors - $numberOfDoors")
    }
}

fun main() {
    val car1 = Car("Toyota", "Corolla", 4)
    val car2 = Car("Honda", "Civic", 2)

    car1.displayInfo()
    // Car Info: Make - Toyota, Model - Corolla, Transmission Type - Automatic, Number of Doors - 4
    car2.displayInfo()
    // Car Info: Make - Honda, Model - Civic, Transmission Type - Automatic, Number of Doors - 2

}
```
{kotlin-runnable="true" id="kotlin-tour-class-override-property"}

This example:
* Creates two instances of the `Car` class that inherit from the `Vehicle` class: `car1` and `car2`.
* Overrides the `transmissionType` variable in the `Car` class to `"Automatic"`.
* Overrides the `displayInfo()` function in the `Car` class to also print the transmission type and the number of doors.
* Calls the overridden `displayInfo()` function on `car1` and `car2` instances.

For more information about class inheritance and overriding class behavior, see [Inheritance](inheritance.md).

## Classes practice

Exercises TBD.

## Interfaces

When to use interfaces instead of classes?

But what if you want to share some code across multiple classes? Since classes are limited by single inheritance, this could
be challenging to implement using classes alone. However, in Kotlin you can use interfaces to solve this problem.

Interfaces are similar to classes, but they have some differences:

* They don't have a constructor or header.
* They can't contain any state. 
* Their functions and properties are implicitly `open` by default.

You use them when you want to define a set of functions and properties that classes can inherit and implement later.

To declare an interface, use the `interface` keyword:

```kotlin
interface PaymentMethod
```

> In Kotlin, we use the `abstract` keyword to indicate that a function doesn't have an implementation when it is declared.
> The implementation is provided later.
> 
{type="note"}

### Interface inheritance

Interfaces support multiple inheritance so a class can inherit from multiple interfaces at once. First, let's consider
the scenario where a class inherits from one interface.

To create a class that inherits from an interface, add a colon after your class header followed by the name of the interface
that you want to inherit from:

```kotlin
class CreditCardPayment : PaymentMethod
```

For example:

```kotlin
interface PaymentMethod {
    // No open keyword needed because functions are inheritable by default
    fun initiatePayment(amount: Double): String
}

class CreditCardPayment(val cardNumber: String, val cardHolderName: String, val expiryDate: String) : PaymentMethod {
    override fun initiatePayment(amount: Double): String {
        // Simulate processing payment with credit card
        return "Payment of $$amount initiated using Credit Card ending in ${cardNumber.takeLast(4)}."
    }
}

fun main() {
    val paymentMethod = CreditCardPayment("1234 5678 9012 3456", "John Doe", "12/25")
    println(paymentMethod.initiatePayment(100.0))
    // Payment of $100.0 initiated using Credit Card ending in 3456.
}
```

In the example:
* `PaymentMethod` is an interface that has an `initiatePayment()` function without an implementation.
* `CreditCardPayment` is a class that inherits from the `PaymentMethod` interface.
* The `CreditCardPayment` class overrides the inherited `initiatePayment()` function.

* `paymentMethod` is an instance of the `CreditCardPayment` class.
* The overridden `initiatePayment()` function is called on the `paymentMethod` instance with a parameter of `100.0`.

To create a class that inherits from multiple interfaces, add a colon after your class header followed by the name of the interfaces
that you want to inherit from separated by a comma:

```kotlin
class CreditCardPayment : PaymentMethod, PaymentType
```

For example:

```kotlin
interface PaymentMethod {
    fun initiatePayment(amount: Double): String
}

interface PaymentType {
    val paymentType: String
}

class CreditCardPayment(val cardNumber: String, val cardHolderName: String, val expiryDate: String) : PaymentMethod, PaymentType {
    override fun initiatePayment(amount: Double): String {
        // Simulate processing payment with credit card
        return "Payment of $$amount initiated using Credit Card ending in ${cardNumber.takeLast(4)}."
    }
    
    override val paymentType: String = "Credit Card"
}

fun main() {
    val paymentMethod = CreditCardPayment("1234 5678 9012 3456", "John Doe", "12/25")
    println(paymentMethod.initiatePayment(100.0))
    // Payment of $100.0 initiated using Credit Card ending in 3456.

    println("Payment is by ${paymentMethod.paymentType}")
    // Payment is by Credit Card
}
```

In the example:
* `PaymentMethod` is an interface that has an `initiatePayment()` function without an implementation.
* `Refundable` is an interface that has a `processRefund()` function without an implementation.
* `CreditCardPayment` is a class that inherits from the `PaymentMethod` and `Refundable` interfaces.
* The `CreditCardPayment` class overrides the inherited `initiatePayment()` function and the `paymentType` property.

* `paymentMethod` is an instance of the `CreditCardPayment` class.
* The overridden `initiatePayment()` function is called on the `paymentMethod` instance with a parameter of `100.0`.
* The overridden `paymentType` property is accessed on the `paymentMethod` instance.

## Delegation

## Objects

## Special classes

### Abstract classes

### Sealed classes

### Enum classes

### Inline classes



## Next step

