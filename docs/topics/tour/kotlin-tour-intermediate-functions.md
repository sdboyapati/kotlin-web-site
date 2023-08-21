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

### Extension functions or member functions

When you see a function called with a `.`, it's not obvious whether it is a member function or an extension function. Always
check the function definition to understand whether it is a member function or an extension function.

The compiler doesn't allow you to declare an extension function that has the same receiver type, name, and arguments as
an already existing member function. If an extension function and a member function have the same name, the member function
takes priority.

For more information about extension functions, see [Extensions](extensions.md).
