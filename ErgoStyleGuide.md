## 1. General Rules

### 1.1. Reasonable line length

120 chars, as IntelliJ IDEA is configured by default, may be too wide as it doesn't help readability and with shorter lines we can put these
wide monitors to good use when doing side-by-side diffs. And with long
lines it takes effort to notice important details that happen at the
end of those lines.

So as a balance:

- strive for 80 chars as the soft limit and if it gets ugly,
- then 100 chars is enough, except for ...
- function signatures, which can get really ugly if limited
- anything that goes beyond 120 chars is an abomination.

### 1.2. Spacing and indentation

Put one space before and after operators, including the assignment operator.
```scala
def add(int1: Int, int2: Int): Int = int1 + int2
```

Put one space after commas.
```scala
Seq("a", "b", "c") // do this

Seq("a","b","c") // don't omit spaces after commas
```

Put one space after colons.
```scala
// do this
def getConf(key: String, defaultValue: String): String = {
  // some code
}

// don't put spaces before colons
def calculateHeaderPortionInBytes(count : Int) : Int = {
  // some code
}

// don't omit spaces after colons
def multiply(int1:Int, int2:Int):Int = int1 * int2
```

Use 2-space indentation in general.
```scala
if (true) {
  println("Wow!")
}
```

### 1.3. Blank lines

Separate with single blank line:
 - package declaration
 - java imports
 - third party libs imports
 - scala imports
 - main code
```scala
package app

import java.util.concurrent.atomic.AtomicLong

import app.util._
import com.google.common.math.IntMath

import scala.annotation.unchecked.uncheckedVariance
import scala.util.Try

object Main {
  // code
}
```

Separate `class`/`object`/`trait` body after opening brace `{` and closing brace `}` with single blank line if any of its methods have curly braces:

```scala
// Bad practice
class Foo {
  def bar(a: Int, b: Int): Int = {
    // code
  }

  def bar2(a: Int, b: Int): Int = {
    // code
  }

  def bar3(a: Int, b: Int): Int = {
    // code
  }
}

// Instead do this
class Foo {

  def bar(a: Int, b: Int): Int = {
    // code
  }

  def bar2(a: Int, b: Int): Int = {
    // code
  }

  def bar3(a: Int, b: Int): Int = {
    // code
  }  

}

// If neither methods have braces blank lines are redundant
class Foo {
  def bar(a: Int, b: Int): Int = // code
  def bar2(a: Int, b: Int): Int = // code
}
```

### 1.4. Calls chaining

Break line before each call in the chain when its length exceeds 2 (in case all calls except the last are single-line)

```scala
// Ok
val result = obj.attr.map(x => x * 2).init

// Not ok
val result = obj.attr.map(x => x * 2).find(x => x < 0).init

// Right way
val result = obj.attr
  .map(x => x * 2)
  .find(x => x < 0)
  .init

// Ok
val result = list.zipWithIndex.map { case (elem, i) =>
  // code
}

// Not ok, `map` has multi-line body in the middle of the chain,
// logical flow is broken
val result = obj.attr.map { case (k, v) =>
  // code
}.reduce(_ * _)

// Right way
val result = obj.attr
  .map { case (k, v) =>
    // code
  }
  .reduce(_ * _)
```

### 1.5. Pattern matching

When calling a function with a closure (or partial function), if there is only one case, put the case on the same line as the function invocation.

```scala
val result = list.zipWithIndex.map { case (elem, i) =>
  // code
}
```

If there are multiple cases, indent and wrap them.

```scala
val result = list.zipWithIndex.map {
  case (elem: Foo, i) => // code
  case (elem: Bar, i) => // code
}
```

### 1.6. Calls with parameters

In case paramenters don't fit into soft limited line we could have this version, which is not readable:

```scala
    val dp = new DispatchPlan(Set(filteredAssets), start =
      startDate, end = endDate, product, scheduleMap, availabilityMap,
      Set(activationIntervals), contractRepository, priceRepository)
```

So in this case it would be better to do formatting like this:

```scala
    val dp = new DispatchPlan(
      Set(filteredAssets),
      startDate,
      endDate,
      product,
      scheduleMap,
      availabilityMap,
      Set(activationIntervals),
      contractRepository,
      priceRepository
    )
```

But it's not always good in other cases:

```scala
    // awful because that transform call is not visible
    val result = service.something(
      param1,
      param2,
      param3,
      param4,
      param5,
      param6).map(transform)

    // awful because it breaks the logical flow
    val result = service.something(
      param1,
      param2,
      param3,
      param4,
      param5,
      param6
    ).map(transform)
```

This would be much better:

```scala
    val result = service
      .something(param1, param2, param3, param4, param5, param6)
      .map(transform)
```

In case parameters don't even fit into 100-chars limit, it's better to refactor that part of code a bit:

```scala
val result = {
  val instance =
    object.something(
      myAwesomeParam1,
      otherParam2,
      someSeriousParam3,
      anEvenMoreSoParam4,
      lonelyParam5,
      catchSomeFn6,
      startDate7
    )

  instance.map(transform)
}
```

### 1.7. Documentation

```scala
/** Correct single-line comment. */
def foo(x: Int): Int = {
  // code
}

/** Wrong single-line comment.
  */
def foo(x: Int): Int = {
  // code
}

/**
  * Correct multi-line comment
  * bla bla.
  */
def foo(x: Int): Int = {
  // code
}

/** Wrong multi-line comment.
  * bla bla.
  */
def foo(x: Int): Int = {
  // code
}
```

### 1.7. Break long functions

Ideally functions should only be a couple of lines long. If the lines
get too big, then we need to break them into smaller functions and
give them a name.

### 1.8. Avoid spelling errors in names and comments

Spelling errors are freakishly annoying, interrupting a reader's flow.
Use a spell-checker. Intelligent IDEs have built-in
spell-checkers. Note the underlined spelling warnings and fix them.

### 1.9. Use meaningful names

We've got three guidelines here:

1. give descriptive names, but don't go overboard, four words is a
   little too much already
2. you can be terse in naming if the type / purpose can be easily
   inferred from the immediate context, or if there's already an
   established convention
3. if going the descriptive route, don't do bullshit words that are
   meaningless

For example this is acceptable:

```scala
people.flatMap(p => transformed(p))
```

We can see that `p` is a person from the immediate context, so a short
one letter name is OK.

This is in general not acceptable, because usually with tuples the
naming of the collection doesn't reflect well what's contained (if you
haven't given those elements a name, then as a consequence the
collection itself is going to have a bad name):

```
someCollection.map(_._2)
```

Implicit parameters on the other hand are OK with short names, because
being passed implicitly, we don't care about them unless they are
missing:

```scala
def query(id: Long)(implicit ec: ExecutionContext, c: WSClient): Future[Response]
```

This is not acceptable because the name is utterly meaningless, even
if there's a clear attempt at being descriptive:

```scala
def processItems(people: Seq[Person]) = ???
```

It's not acceptable because the naming of this function indicates a
side-effect (`process` is a verb indicating a command), yet it doesn't
describe what we are doing with those `people`. The `Items` suffix is
meaningless, because we might have said `processThingy`,
`processRows`, `processStuff` and it would still say exactly the same
thing - absolutely nothing.