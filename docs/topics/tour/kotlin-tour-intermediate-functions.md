[//]: # (title: Intermediate: Functions)


## Extension functions

When programming, you may find yourself wanting to work with code that is outside your control. You could duplicate
the code and then customize it the way that you want. However, this is time-consuming and inefficient. Kotlin offers an 
alternative approach via **extension functions**.

Extension functions allow you to extend a class with additional functionality. You call extension functions as if they
are member functions of a class, but you control what the function does.

Before introducing the syntax for extension functions, you need to understand the terms **receiver type** and 
**receiver object**.

Say we have a function that shares some information. This function is called by a sender. The receiver is what the function
is called on. In other words, the receiver is where or with whom the information is shared.

The receiver has a **type** so that the compiler understands when the function can be used. The **receiver object** is
the instance of that type.

To declare an extension function, write the name of the class that you want to extend followed by a `.` and the name of
your function. For example: `fun String.bold()`

In the following example:
* `String` is the class to be extended, also known as the receiver type.
* the `.bold()` extension function's return type is `String`.
* an instance of `String` is the receiver object.
* the receiver object is accessed by [keyword](keyword-reference.md): `this`.
* a string template (`$`) is used to access the value of `this`.
* the `.bold()` extension function takes a string and returns it in a `<b>` HTML element for bold text.

```kotlin
fun String.bold(): String = "<b>$this</b>"

fun main() {
    // "hello" is the receiver object
    println("hello".bold())
    // <b>hello</b>
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-extension-function"}

You can find many examples of extension in Kotlin's [standard library](https://kotlinlang.org/api/latest/jvm/stdlib/).
For example, the `String` class has many [extension functions](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/#extension-functions)
to help you work with strings.

### Extension function or member function?

When you see a function called with a `.`, it's not obvious whether it is an extension function or a member function. Always
check the function definition to understand whether it is an extension function or a member function.

The compiler doesn't allow you to declare an extension function that has the same receiver type, name, and arguments as
an already existing member function. If an extension function and a member function have the same name, the member function
takes priority.

<!-- Add example? -->

For more information about extension functions, see [Extensions](extensions.md).

## Scope functions

In programming, a scope is the area in which your variable or object is recognized. The most commonly referred to scopes
are global scope and local scope. In Kotlin, there are scope functions which allow you to create a temporary scope around
an object and execute some code.

Scope functions make your code more concise because you don't have to refer to the name of your object within the temporary
scope. Kotlin has five scope functions: `.apply()`, `.also()`, .`run()`, `with()`, and `.let()`.

Depending on the scope function, you can access the object either by referencing it via keyword `this` or using it as an
argument via keyword `it`.

Each scope function takes a lambda expression and returns either the object or the result of the lambda expression. In 
this tour, we start with the scope functions that return their objects.

### Return objects

#### Apply

Use the `.apply()` scope function to initialize objects, like a class instance:

```kotlin
data class Person(var name: String, var age: Int = 0, var about: String= "")

fun main() {
    Person("Jake").apply {
        this.age = 30
        // Alternatively, this.about
        about = "Android developer"
        println(this)
        // Person(name=Jake, age=30, about=Android developer)
    }
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-apply"}

This example:
* Creates an instance of the `Person` data class.
* Uses the `.apply()` scope function with a lambda expression to update the `age` and `about` properties.
* Prints the instance by referencing it via `this`.

> Within the scope function, to access an object's properties or member functions you don't have to use `this`.
>
{type = "info"}

Since the `.apply()` function returns the object, you can use that object in further function calls. For example:

```kotlin
data class Person(var name: String, var age: Int = 0, var about: String= "")

fun main() {
    val james = Person("Jake").apply {
        age = 30
        about = "Android developer"
    }.copy(name = "James")
    println(james)
    // Person(name=James, age=30, about=Android developer)
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-apply-chain"}

This example:
* Creates `james` as an instance of the `Person` data class.
* Uses the `.apply()` scope function with a lambda expression to update the `age` and `about` properties.
* Uses the `.copy()` function to create a copy of `james` and update the `name` from `Jake` to `James`.
* Prints the updated `james` instance.

#### Also

Use the `.also()` scope function to complete an additional action with an object, like writing a log:

```kotlin
data class Person(var name: String, var age: Int = 0, var about: String = "")
         
fun writeCreationLog(p: Person) {
    println("A new person ${p.name} was created.")              
}
         
fun main() {
    val jake = Person("Jake", 30, "Android developer")
        .also {
            writeCreationLog(it)
            // A new person Jake was created.
        }
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-also"}

This example:
* Creates `jake` as an instance of the `Person` data class.
* Uses the `.also()` scope function with a lambda expression to call the `writeCreationLog()` function.
* Passes `jake` as an argument to the `writeCreationLog()` function via `it`.
* Accesses the `name` property of `jake` in the `writeCreationLog()` function and uses a string template to print: `A new person Jake was created.`

Since the `.also()` function returns the object, you can use that object in further function calls.

### Return lambda results

#### Run

Similar to `.apply()` you can use `.run()` to initialize an object. Use `.run()` to initialize an object **and** compute
a result:

```kotlin
data class Person(var name: String, val langs: MutableSet<String>, var age: Int = 0, var about: String= "", )

fun main() {
    val jake = Person("Jake", mutableSetOf("Kotlin")).run {
        age = 30
        about = "Android developer"
        langs.add("C++")
        
        // Alternatively, println(this.langs)
        println(langs)
        // [Kotlin, C++]
    }
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-run"}

This example:
* Creates `jake` as an instance of the `Person` data class.
* Uses the `.run()` scope function with a lambda expression to:
  * update the `age` and `about` properties.
  * use the `.add()` function to add `"C++"` to the mutable set of `langs`.
* Prints the `langs` property.

#### With 

#### Let