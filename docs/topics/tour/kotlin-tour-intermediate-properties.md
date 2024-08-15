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

For more information about backing fields, see [Backing fields](properties.md#backing-fields).


## Extension properties

Just like extension functions, there are also extension properties. Extension properties allow you to add new properties
to existing classes without modifying their source code. However, extension properties in Kotlin do **not** have backing
fields. This means that Kotlin doesn't provide default `get()` and `set()` functions automatically. You have to write them
yourself. Additionally, the lack of a backing field means that they can't hold any state.

To declare an extension property, write the name of the class that you want to extend followed by a `.` and the name of
your property. Just like with normal class properties, you need to declare a receiver type for your property. 
For example:

```kotlin
val String.lastChar: Char
```

Extension properties are most useful when you want a property to contain a computed value without using inheritance.
For example, let's say that you have a data class called `Person` that has two properties: `firstName`, `lastName`.

```kotlin
data class Person(val firstName: String, val lastName: String)
```

You want to be able to access the person's full name without modifying the `Person` data class or inheriting from it.
You can do this by creating an extension property with a custom `get()` function:

```kotlin
data class Person(val firstName: String, val lastName: String)

// Extension property to get the full name
val Person.fullName: String
    get() = "$firstName $lastName"

fun main() {
    val person = Person(firstName = "John", lastName = "Doe")
    
    // Use the extension property
    println(person.fullName)
    // John Doe
}
```

> It's important to note that extension properties can't override existing properties of a class.
> 
{type="note"}

## Delegated properties

You already learned about delegation in the [Classes](kotlin-tour-intermediate-classes.md#delegation) chapter. You can
also use delegation with properties to delegate their `get()` and `set()` functions to another object. This is useful
when you have more complex requirements for storing properties that a aimple backing field can't handle, such as storing
values in a database table, browser session, or map. Using delegated properties also reduces boilerplate code because the
logic for getting and setting your properties is contained in the object that you delegate to.

The syntax is similar to using delegation with classes but operates on a lower level. Declare your property, followed by
the `by` keyword and the object you want to delegate to. For example:

```kotlin
val displayName: String by Delegate
```

Suppose you want to have a computed property, like a user's display name, that is calculated only once because
the operation is expensive and your application is performance sensitive. You can use a delegated property to cache the
display name so that it is only computed once but can be accessed anytime without performance impact.

First, create the object to delegate to that contains the cached value and defines its `get()` function.
In this case, the object is a class called `CachedStringDelegate`:

```kotlin
class CachedStringDelegate {
    var cachedValue: String? = null
}
```

Within the class, add the behavior that you want from the `get()` function to the `getValue()` operator function.
For the delegated property to work, every delegate **must** have a `getValue()` operator function. If the property is 
mutable, you must also have a `setValue()` function.

In the code sample below, the signature of the `getValue()` function is always the same `(thisRef: Any?, property: KProperty<*>)`.
You don't have to understand it to use it.

```kotlin
class CachedStringDelegate {
    var cachedValue: String? = null

    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        if (cachedValue == null) {
            cachedValue = "Default Value"
            println("Computed and cached: $cachedValue")
        } else {
            println("Accessed from cache: $cachedValue")
        }
        return cachedValue ?: "Unknown"
    }
}
```

The `getValue()` function checks whether the `cachedValue` property is `null`. If it is, the function assigns the value of]
`"Default value"` as well as printing a string for logging purposes. If the `cachedValue` property isn't `null`, so it's already been computed,
another string is printed for logging purposes. Finally, the function uses a safe call to return either the cached value
or `"Unknown"` if the value is `null`.

Now you can delegate the property that you want to cache (`val displayName`) to an instance of the `CachedStringDelegate` class:

```kotlin
import kotlin.reflect.KProperty

class CachedStringDelegate {
    var cachedValue: String? = null

    operator fun getValue(thisRef: User, property: KProperty<*>): String {
        if (cachedValue == null) {
            cachedValue = "${thisRef.firstName} ${thisRef.lastName}"
            println("Computed and cached: $cachedValue")
        } else {
            println("Accessed from cache: $cachedValue")
        }
        return cachedValue ?: "Unknown"
    }
}

//sampleStart
class User(val firstName: String, val lastName: String) {
    val displayName: String by CachedStringDelegate()
}

fun main() {
    val user = User("John", "Doe")
    
    // First access computes and caches the value
    println(user.displayName)  
    // Computed and cached: Default Value

    // Subsequent accesses retrieve the value from cache
    println(user.displayName)  
    // Accessed from cache: Default Value
}
//sampleEnd
```
{runnable="true"}

<--! Add note about `thisRef` being set to `User`-->


Delegating to another property  `::` qualifier

Storing properties in a map

Local delegated properties

### Lazy properties

### Observable properties

Notifying listeners

## Next step

