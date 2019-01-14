## 2. Language rules

### 2.1. Do not define useless traits

In this example `Person` is perfectly described by its case class as a data-structure without behavior so there is no need for trait definition.

```scala
trait PersonLike {
  def name: String
  def age: Int
}

case class Person(name: String, age: Int) extends PersonLike
```

### 2.2. Do not catch Throwable when catching Exceptions

Avoid catching `Throwable` as it could be an extremely fatal exception that should never be caught and that should crash the process:

```scala
try {
  // code
} catch {
  case e: Throwable =>
    // code
}
```

Instead do this:

```scala
import scala.util.control.NonFatal

try {
  // code
} catch {
  case NonFatal(e) =>
    // code
}
```

### 2.3. Do not use `Option.get`

```scala
val someValue: Option[Int] = ???
val result = someValue.get + 1
```

Alternatives:

 - using `Option.getOrElse`
 - using `Option.fold`
 - using pattern matching and dealing with the `None` branch explicitly
 - not taking the value out of its optional context

### 2.4. Public functions should have an explicit return type

```scala
def someFunction(param1: T1, param2: T2): Result = {
  // code
}
```