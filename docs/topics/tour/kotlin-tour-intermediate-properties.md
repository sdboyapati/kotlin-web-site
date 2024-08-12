[//]: # (title: Intermediate Properties)

In the beginner's tour, you learned how properties are used to declare characteristics of class instances and how to access
them. This chapter digs deeper into how properties work in Kotlin and explores other ways that you can use them in your code.

## Backing fields

Under the hood, properties are set using a `set()` function and retrieved using a `get()` function. These functions work
with the actual value of the property, known as a **backing field**. In Kotlin, the backing field is automatically hidden
in Kotlin, but you can access it by using the keyword `field`. For example, this code:

```kotlin
class Contact(val id: Int, var email: String) {
    val category: String = ""
}
```

Is the same as this code:

```kotlin
class Contact(val id: Int, var email: String) {
    val category: String = ""
        get() = field
        set(value) {
            field = value
        }
}
```

The `get()` function retrieves the property value from the field: `""`.
The `set()` function accepts `value` as a parameter and assigns it to the field, where `value` is `""`. 

It's important to note that you can't explicitly call the `get()` and `set()` functions on properties in Kotlin. By default,
Kotlin provides `get()` and `set()` functions for properties, but you can create your own custom `get()` and `set()` functions.

> `get()` and `set()` functions are also called getters and setters. 
> 
{type="tip"}

Access to the underlying property value is especially useful when you want to perform additional logic
in your `get()` or `set()` functions without causing an infinite loop. For example, you have a 
`Person` class with a property called `name`:

```kotlin
class Person {
    var name: String = ""
}

```

You want to ensure that the first letter of the `name` property is capitalized, so you create a custom `set()` function
that uses the [`.replaceFirstChar()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/replace-first-char.html) 
and [`.uppercase()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/uppercase-char.html) extension functions. 
However, if you refer to the property directly in your `set()` function, you create an infinite loop and see a `StackOverflowError`
at runtime:

```kotlin
class Person {
    var name: String = ""
        set(value) {
            // This causes a runtime error
            name = value.replaceFirstChar { firstChar -> firstChar.uppercase() }
        }
}

fun main() {
    val person = Person()
    person.name = "kodee"
    println(person.name)
}
```
{validate="false"}

To fix this, you can use the backing field in your `set()` function instead:

```kotlin
class Person {
    var name: String = ""
        set(value) {
            field = value.replaceFirstChar { firstChar -> firstChar.uppercase() }
        }
}

fun main() {
    val person = Person()
    person.name = "kodee"
    println(person.name)
    // Kodee
}
```

Backing fields are also useful when you want to add logging, send notifications when a property value changes,
or use additional logic that compares the old and new property values.

<!-- Mention backing properties? Needs intro on visibility modifiers -->

## Delegated properties

## Lazy properties

## Extension properties

## Overriding properties

## Next step

