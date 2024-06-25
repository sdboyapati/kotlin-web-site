[//]: # (title: Intermediate Functions)

## Extension functions

When programming, you may find yourself wanting to work with code that is outside your control. You could duplicate
the code and then customize it the way you want. However, this is time-consuming and inefficient. Kotlin offers an 
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
* `String` is the class that is extended, also known as the receiver type.
* the `.bold()` extension function's return type is `String`.
* an instance of `String` is the receiver object.
* the receiver object is accessed by the [keyword](keyword-reference.md): `this`.
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

You can find many examples of extension functions in Kotlin's [standard library](https://kotlinlang.org/api/latest/jvm/stdlib/).
For example, the `String` class has many [extension functions](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/#extension-functions)
to help you work with strings.

### Extension function or member function?

When you see a function called with a `.`, it's not immediately obvious whether it's an extension function or a member function.

For example:
```kotlin
fun main() {
//sampleStart
    val testString = "hello"
    // hashCode() is a member function
    println(testString.hashCode())
    // count() is an extension function
    println(testString.count())
//sampleEnd
}
```

Always check the function definition to understand whether it's an extension function or a member function:

<table header-style="top">
   <tr>
       <td>Member function</td>
       <td>Extension function</td>
   </tr>
   <tr>
<td>

```kotlin
class String : Comparable<String>, CharSequence {
    fun hashCode(): Int
}
```

</td>
<td>

```kotlin
fun CharSequence.count(): Int {
    return length
}
```

</td>
</tr>
</table>

The compiler doesn't allow you to declare an extension function that has the same receiver type, name, and arguments as
an already existing member function. If an extension function and a member function have the same name, the member function
takes priority.

For more information about extension functions, see [Extensions](extensions.md).

## Extension functions practice

## Scope functions

In programming, a scope is the area in which your variable or object is recognized. The most commonly referred to scopes
are global scope and local scope. In Kotlin, there are scope functions that allow you to create a temporary scope around
an object and execute some code.

Scope functions make your code more concise because you don't have to refer to the name of your object within the temporary
scope. Kotlin has five scope functions: `.apply()`, `.also()`, `.run()`, `with()`, and `.let()`.

Depending on the scope function, you can access the object either by referencing it via the keyword `this` or using it as an
argument via the keyword `it`.

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
{type = "note"}

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
data class Person(var name: String, val langs: MutableSet<String>, var age: Int = 0, var about: String= "")

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

Unlike the other scope functions, `with()` is not an extension function, so the syntax is different. You pass the receiver
object to `with()` as an argument. Use `with()` to call multiple functions on an object:

```kotlin
class WifiConnection {
    fun listenBeacon() = println("Looking for Wi-Fi")
    fun authenticate() = println("Authenticating with Wi-Fi network")
    fun connect() = println("Connecting to Wi-Fi network")
    fun sendPackets() = println("Transferring data")
    fun disconnect() = println("Disconnecting from Wi-Fi network")
}

fun main() {
    val wifiDevice = WifiConnection()
    with(wifiDevice) {
        listenBeacon() 
        // Alternatively, this.listenBeacon()
        authenticate()
        connect()
        sendPackets()
        disconnect()
    }
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-with"}

This example:
* Creates `wifiDevice` as an instance of the `wifiConnection` class.
* Passes `wifiDevice` as an argument to the  `with()` scope function.
* Uses a lambda expression with the `with()` scope function that calls multiple member functions of the class.

#### Let

Use the `.let()` scope function to:
* perform null checks.
* introduce local variables with a limited scope.

```kotlin
fun customPrint(s: String) {
    print(s.uppercase())
}

fun main() {
    fun printNonNull(str: String?) {
        println("Printing \"$str\":")

        str?.let {
            customPrint(it)
            println()
        }
    }

    printNonNull(null)
    // Printing "null":
    printNonNull("my string")
    // Printing "my string":
    // MY STRING
//sampleEnd  
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-let-non-null"}

This example:
* Uses the `printNonNull()` function with argument `null`.
* Prints a string, including `null` by using a string template.
* Uses a safe call `?.` to check if `null` is `null`.

|---|---|

* Uses the `printNonNull()` function with argument `my string`.
* Prints a string, including `my string` by using a string template.
* Uses a safe call `?.` to check if `my string` is `null`.
* Uses the `.let()` scope function with a lambda expression to:
  * pass `my string` as an argument to the `customPrint()` function via `it`.
  * use the `println()` function to print a new line.

```kotlin
fun customPrint(s: String) {
    print(s.uppercase())
}

fun main() {
    val empty = "test".let {
        customPrint(it)
        it.isEmpty()
    }
    println(" is empty: $empty")
    // TEST is empty: false
}
```
{kotlin-runnable="true" id="kotlin-tour-scope-function-let-local-variable"}

This example:
* Creates a local variable `empty`.
* Assigns the string `test` to `empty` and uses the `.let()` scope function with a lambda expression to:
  * pass `empty` as an argument to the `customPrint()` function via `it`.
  * use the `.isEmpty()` extension function on `empty` by referencing it via `it`.
* Prints a string, including `empty` by using a string template.

## Scope functions practice

## Function literals with receiver

In the beginner's tour, you learned how to use [lambda expressions](kotlin-tour-functions.md#lambda-expressions) to make
your code more concise. A lambda expression is an example of a **function literal** in Kotlin. 

A function literal is a function that is declared:
* without a name.
* as an expression.

For example, consider the first lambda expression that you saw in the beginner's tour:

```kotlin
fun main() {
    println({ text: String -> text.uppercase() }("hello"))
    // HELLO
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-function-literal"}

The lambda expression has no name and the function is defined within curly braces `{}`: `text: String -> text.uppercase()`.

Function literals are useful not only for making your code more concise but also because they make it easy to pass behavior
as parameters. For example:

```kotlin
fun performOperation(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

fun main() {
  val sum = performOperation(5, 3) { x, y -> x + y }
  println(sum) // Output: 8
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-function-literal-behavior"}

In this example, the `performOperation()` function has a parameter `operation` that accepts any function with function type:
`(Int, Int) -> Int)`. The `performOperation()` function is called with a function literal in the form of a lambda expression: 
`{ x, y -> x + y }`. The result of the lambda expression and integers is assigned to a variable: `sum`.

In addition, you can use function literals where the function is passed as a parameter to be executed at a certain point
in time, like for callbacks or event listeners. To see examples of these use cases, try [our exercises](#function-literals-with-receiver-practice).

Now that you understand what a function literal is, you're ready to learn about **function literals with receiver**.
As the name implies, a function literal **with receiver** is a function literal that can be called on a **receiver object**.
This means that you can access the receiver's properties and member functions without explicitly referring to the receiver
object. 

Like extension functions, function literals with receiver are a convenient way to extend a class without modifying the class
directly.

The syntax for a function literal with receiver is different when you define the function type: write the receiver object
that you want to extend followed by a `.` and the rest of your function type definition. For example: `MutableList<Int>.() -> Unit`.

This function type has:
* `MutableList<Int>` as the receiver object.
* no function parameters `()`.
* no return value `Unit`.

Let's consider an example that extends the `StringBuilder` class:

```kotlin
fun main() {
    // Function literal with receiver definition
    val appendText: StringBuilder.() -> Unit = {
        append("Hello!")
    }

    // Use the function literal with receiver
    val stringBuilder = StringBuilder()
    stringBuilder.appendText()
    println(stringBuilder.toString())  
    // Hello!
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-function-literal-with-receiver"}

In this example:
* `StringBuilder` is the class that is extended, also known as the receiver type.
* The function type of the function literal has no function parameters `()` and has no return value `Unit`.
* The function literal is a lambda expression.
* The lambda expression calls the `append()` member function from the `StringBuilder` class and uses the string "Hello!" as the function parameter.
* The function literal is assigned to the `appendText` variable.

|---|---|

* An instance of the `StringBuilder` class is created.
* The lambda expression assigned to `appendText` is called on the `stringBuilder` instance.
* The `stringBuilder` instance is converted to string with the `.toString()` function and printed via the `println()` function.

> Compared to extension functions, function literals with receiver are more flexible as they can be defined locally, passed
> to another function as a parameter, or assigned to a variable.
>
{type="note"}

Function literals with receiver are helpful when you want to create a domain-specific language (DSL). Since you have
access to the receiver object's member functions and properties without explicitly referencing the receiver, your code 
becomes more concise and readable.

Let's consider an example that configures items in a menu. Let's begin with a `MenuItem` class, and a `Menu` class that contains
a function to add items to the menu called `item()` as well as a list of all menu items `items`:

```kotlin
class MenuItem(val name: String, val action: () -> Unit)

class Menu(val name: String) {
  val items = mutableListOf<MenuItem>()

  fun item(name: String, action: () -> Unit) {
    items.add(MenuItem(name, action))
  }
}
```

Let's use a function literal with receiver passed as a function parameter (`init`) to the `menu()` function that builds 
a menu as a starting point. You'll notice that the code follows a similar approach to the previous example with the 
`StringBuilder` class:

```kotlin
fun menu(name: String, init: Menu.() -> Unit): Menu {
    // Creates an instance of the Menu class
    val menu = Menu(name)
    // Calls the function literal with receiver init() on the class instance
    menu.init()
    return menu
}
```

Now let's use the DSL to configure a menu and create a `printMenu()` function to print the menu structure to console:

```kotlin
class MenuItem(val name: String, val action: () -> Unit)

class Menu(val name: String) {
  val items = mutableListOf<MenuItem>()

  fun item(name: String, action: () -> Unit) {
    items.add(MenuItem(name, action))
  }
}

fun menu(name: String, init: Menu.() -> Unit): Menu {
  val menu = Menu(name)
  menu.init()
  return menu
}

//sampleStart
fun printMenu(menu: Menu) {
  println("Menu: ${menu.name}")
  menu.items.forEach { println("  Item: ${it.name}") }
}

// Use the DSL
fun main() {
    // Create the menu
    val mainMenu = menu("Main Menu") {
        // Add items to the menu
        item("Home") { println("Home selected") }
        item("Settings") { println("Settings selected") }
        item("New File") { println("New file created") }
        item("Open File") { println("File opened") }
        item("Exit") { println("Exiting program") }
    }
    
    // Print the menu
    printMenu(mainMenu)
    // Menu: Main Menu
      // Item: Home
      // Item: Settings
      // Item: New File
      // Item: Open File
      // Item: Exit
}
//sampleEnd
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-function-literal-with-receiver-dsl"}

## Function literals with receiver practice

### Exercise 1 {initial-collapse-state="collapsed" id="function-literals-exercise-1"}

Exercise for function literals as callbacks

```kotlin
fun fetchData(callback: (String) -> Unit) {
    // Simulate network request
    callback("Data received")
}

fetchData { data -> println(data) } // Output: Data received
```

### Exercise 2 {initial-collapse-state="collapsed" id="function-literals-exercise-2"}

Exercise for function literals as event listeners

```kotlin
button.setOnClickListener {
  println("Button clicked!")
}
```

### Exercise 3 {initial-collapse-state="collapsed" id="function-literals-exercise-3"}

Exercise for function literals with receiver with `buildString()` and `buildList()` functions.

## Infix notation

Another way that you can make the code for your member or extension functions more concise and readable is to use infix 
notation. When you add the `infix` keyword to your function declaration, it means that you don't need to use the `.` or
parentheses when you call your function. This turns your function into an infix function. For example:

```kotlin
class Point(val x: Int, val y: Int) {
  // Add the infix keyword to the moveBy() function
  infix fun moveBy(delta: Point): Point {
    return Point(x + delta.x, y + delta.y)
  }
}

fun main() {
  val p1 = Point(2, 3)
  val p2 = Point(4, 5)

  // Use infix notation
  val p3 = p1 moveBy p2
  println("New Point: (${p3.x}, ${p3.y})")
  // New Point: (6, 8)

  // Alternatively, use full function call
  val p4 = p1.moveBy(p2)
  println("New Point: (${p4.x}, ${p4.y})")
  // New Point: (6, 8)
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-intermediate-tour-function-infix"}

Similar to function literals with receiver, infix notation is a handy feature to use when you want to create expressive 
DSLs. Infix notation helps your code read more like a natural language.

There are some rules to keep in mind when using infix notation:
* The function must be a member or extension function.
* The function must have only a **single** function parameter.
* The function parameter mustn't accept a variable number of arguments.
* The function parameter can't have a default value.
* When calling an infix function on a receiver object, you must refer to the receiver object by using `this`.

Now that you've learned new ways to extend classes, it's time to learn more about classes, their special types, alternatives,
and how class inheritance works.

### Exercise {initial-collapse-state="collapsed" id="infix-notation-exercise"}

Convert the `append()` extension function into an infix function and use it in a Kotlin program.

|---|---|
```kotlin
// Change the code here
fun MutableList<Int>.append(item: Int) {
  this.add(item)
}

fun main() {
  val list = mutableListOf(1, 2, 3)
  // Change the code here
  list.append(4)
  println(list)
}
```
{validate="false" kotlin-runnable="true" kotlin-min-compiler-version="1.3" id="kotlin-tour-infix-notation-exercise"}

|---|---|
```kotlin
infix fun MutableList<Int>.append(item: Int) {
  this.add(item)
}

fun main() {
  val list = mutableListOf(1, 2, 3)
  list append 4
  println(list)  // Output: [1, 2, 3, 4]
}
```
{initial-collapse-state="collapsed" collapsed-title="Example solution" id="kotlin-tour-infix-notation-solution"}

## Next step

