+++
title = 'JDK 22 - What’s up doctor ?'
date = 2024-03-28T12:10:40+01:00
draft = false
+++

I actually discovered what's new in JDK22 and how Java became more attractive day by day.
I'd like to share a compilation of what I have discovered and tried with my short conclusion and point of view.

Before you start, you can find the release note here: https://jdk.java.net/22/release-notes

### JEP 456: Unnamed Variables & Patterns
Sometimes you need to declare variables without using it, for matching patterns or for specific contexts.
With JEP 456, you can now use `_` instead of declaring the variable.
This works for pattern too, and allows you to ignore the type and the name of the registration component  in pattern matching.

####  Unnamed Variables
A very simple example about how you can now skip the variable declaration. Here you count the number of orders in an (bad ? :D) iterable order.
Singular `order`, is never used (and IDE will trigger a warning) you can simplify by replacing `order` by `_`

```java
static int count(Iterable<Order> orders) {
    int total = 0;
    for (Order order : orders)    // order is never unused
        total++;
    return total;
```
Becomes
```java
static int count(Iterable<Order> orders) {
    int total = 0;
    for (Order _ : orders)  // order => _
        total++;
    return total;
```

I already know, I'm going abuse it in `try/catch` clauses:
```java
try { ... }
catch (Exception _) { ... }
catch (Throwable _) { ... }
```

####  Unnamed Patterns
For me, unnamed pattern will empower the Pattern Matching syntax [`JEP 441 Pattern Matching for switch`](https//openjdk.org/jeps/441) delivered in JDK21
Now you can use unnamed patterns and you can transition from this:
```java
sealed abstract class Ball permits RedBall, BlueBall, GreenBall { }
final  class RedBall   extends Ball { }
final  class BlueBall  extends Ball { }
final  class GreenBall extends Ball { }

Ball ball = //...
switch (ball) {
    case RedBall   red   -> process(ball); //<- red var is unused
    case BlueBall  blue  -> process(ball); //<- blue var is unused
    case GreenBall green -> stopProcessing(); // <- green var is unused
}
```

`RedBall` and `BlueBall` call the same function with balls as parameters and  `red` `blue` and `green` are all unused.
So you can inline `RedBall` and `BlueBall` in the same branch and replace `red` `blue` and `green`.

Now suppose that we define a record class Box which can hold any type of Ball, but might also hold the null value:
```java
 switch (box) {
        case Box(RedBall _), Box(BlueBall _) -> processBox(box);
        case Box(GreenBall _)                -> stopProcessing();
        case Box(var _)                      -> pickAnotherBox();
        }
 ```
Note: you added a switch branch for all other `Box` with `var _` that will catch all other patterns.

You can add [Guard](https://openjdk.org/jeps/441#Case-refinement) pattern. Remember this guard covers the whole label branch and not only one pattern.
you can do:
```java
case Box(RedBall _), Box(BlueBall _) when x == 42 -> processBox(b);
```
and not
```java 
case Box(RedBall _) when x == 0, Box(BlueBall _) when x == 42 -> processBox(b);
```

Less common case you will be able to match patterns for `instanceof`, here an example:
```java
if (r instanceof ColoredPoint(Point(int x, int y), Color c)) {
    ... x ... y ... 
}
```
Color is not used here, you can rename it with _:
```java
if (r instanceof ColoredPoint(Point(int x, int y), _)) { ... x ... y ... }
```

### JEP 447: Statements before super(...) - IT'S A FEATURE PREVIEW !

The main goal is to give developers greater freedom to express behaviors of constructors by adding logic while maintaining
the guarantee that constructors run in top-down order during the class instantiation, **ensuring the code in the subclass constructor cannot interfere with the superclass instancation.*

you can now prevent unnecessary work in the `super()` statement before validating an argument. By this you are now able to check or prepare arguments before calling the superclass constructor.

```java
public class PositiveBigInteger extends BigInteger {

    public PositiveBigInteger(long value) {
        super(value);  //  <------------- Potentially unnecessary work
        if (value <= 0)
            throw new IllegalArgumentException("non-positive value");
    }

}

// BECOMES

public class PositiveBigInteger extends BigInteger {

    public PositiveBigInteger(long value) {
        super(verifyPositive(value));
    }

    private static long verifyPositive(long value) {
        if (value <= 0)
            throw new IllegalArgumentException("non-positive value");
        return value;
    }

}

// Preparing the super class constructor arg

public class Sub extends Super {

    public Sub(Certificate certificate) {    // Certificate type  
        super(prepareByteArray(certificate)); // super accept only byte[] 
    }

    // Auxiliary method transform Certificate to byte array
    private static byte[] prepareByteArray(Certificate certificate) {  
        var publicKey = certificate.getPublicKey();
        if (publicKey == null)
            throw new IllegalArgumentException("null certificate");
        return switch (publicKey) {
            case RSAKey rsaKey -> ...
            case DSAPublicKey dsaKey -> ...
            ...
            default -> ...
        };
    }

}
```

## JEP 457: Class-File API FEATURE PREVIEW !

The objective here is providing a standard API for parsing, generating and transforming java class files.
By this API, developers can now: Parse, Generate and transform a classfile

## JEP 458: Launch Multi-File Source-Code Programs

Java app launchers now are able to run programs based on multiple files of java source code. By this the transition for large projects will be more smooth.

## JEP 459: String Templates (Second Preview)  - FEATURE PREVIEW !

Actually developers routinely compose String with concatenation (+), StringBuilder, String::format or MessageFormat. All of these are hard to read or verbose too much.
Template expressions are a new kind of expression in Java and can perform string interpolation but helps the developers compose strings safely and efficiently.

```java 
String name = "Joan";
String info = STR."My name is \{name}";
assert info.equals("My name is Joan");   // true
```

String template comes with arithmetics expressions
```java
int x = 10, y = 20;
String s = STR."\{x} + \{y} = \{x + y}";
| "10 + 20 = 30"
```
Call methods and access fields
```java
String s = STR."You have a \{getOfferType()} waiting for you!";
| "You have a gift waiting for you!"
String t = STR."Access at \{req.date} \{req.time} from \{req.ipAddress}";
| "Access at 2022-03-25 15:34 from 8.8.8.8"
```

**To avoid refactoring, double quote characters can now be USED INSIDE the expressions without being escaped**
```java
String msg = STR."The file \{filePath} \{file.exists() ? "does": "does not"} exist";
| "The file tmp.dat does exist" or "The file tmp.dat does not exist"
```

Multiline is supported in the source file without introducing newlines ! The value of the embedded expression is interpolated `int` the result at the position of the `\ `
The template is considered to continue on the same line.

```java
String time = STR."The time is \{
    // The java.time.format package is very useful
    DateTimeFormatter
      .ofPattern("HH:mm:ss")
      .format(LocalTime.now())
} right now";
| "The time is 12:34:56 right now"
```

####  FMT Processor

FMT is another template processor, it also interpret the format specified in the left of the embedded expressions.

**The format specifiers are the same as those defined in java.util.Formatter** [Documentation](https://www.logicbig.com/tutorials/core-java-tutorial/java-se-api/util-formatter.html)

```java
record Rectangle(String name, double width, double height) {
    double area() {
        return width * height;
    }
}
Rectangle[] zone = new Rectangle[] {
    new Rectangle("Alfa", 17.8, 31.4),
    new Rectangle("Bravo", 9.6, 12.4),
    new Rectangle("Charlie", 7.1, 11.23),
};
String table = FMT."""
    Description     Width    Height     Area
    %-12s\{zone[0].name}  %7.2f\{zone[0].width}  %7.2f\{zone[0].height}     %7.2f\{zone[0].area()}
    %-12s\{zone[1].name}  %7.2f\{zone[1].width}  %7.2f\{zone[1].height}     %7.2f\{zone[1].area()}
    %-12s\{zone[2].name}  %7.2f\{zone[2].width}  %7.2f\{zone[2].height}     %7.2f\{zone[2].area()}
    \{" ".repeat(28)} Total %7.2f\{zone[0].area() + zone[1].area() + zone[2].area()}
    """;
| """
| Description     Width    Height     Area
| Alfa            17.80    31.40      558.92
| Bravo            9.60    12.40      119.04
| Charlie          7.10    11.23       79.73
|                              Total  757.69
| """
```

####  User defined processors
Developers can easily create template processors for use in template expression.

That is useful shorthand, but again it is more accurate to say that a template processor is an object which is an
instance of the functional interface StringTemplate.Processor.

In particular, the object's class implements the single abstract method of that interface, process, which takes a
StringTemplate and returns an object. A static field such as STR merely stores an instance of such a class.

```java 
int x = 10, y = 20;
StringTemplate st = RAW."\{x} plus \{y} equals \{x + y}";
String s = st.toString();
| StringTemplate{ fragments = [ "", " plus ", " equals ", "" ], values = [10, 20, 30] }
```

A template processor that implements the StringTemplate.Processor interface directly can be fully general.

The declaration of the StringTemplate.Processor interface is:
```java
package java.lang;
public interface StringTemplate {
    ...
    @FunctionalInterface
    public interface Processor<R, E extends Throwable> {
        R process(StringTemplate st) throws E;
    }
    ...
}

```

It can return objects of any type, not just String. It can also throw checked exceptions if processing fails, either because the template is invalid or for some other reason, such as an I/O error. If a template processor throws checked exceptions then developers who use it in template expressions must handle processing failures with try-catch statements, or else propagate the exceptions to callers.


```java
var JSON = StringTemplate.Processor.of(
        (StringTemplate st) -> new JSONObject(st.interpolate())
    );

String name    = "Joan Smith";
String phone   = "555-123-4567";
String address = "1 Maple Drive, Anytown";
JSONObject doc = JSON."""
    {
        "name":    "\{name}",
        "phone":   "\{phone}",
        "address": "\{address}"
    };
    """;
```

#### Safely composing and executing database queries
The template processor class below, QueryBuilder, first creates a SQL query string from a string template. It then creates a JDBC PreparedStatement from that query string and sets its parameters to the values of the embedded expressions.
```java
record QueryBuilder(Connection conn)
        implements StringTemplate.Processor<PreparedStatement, SQLException> {

    public PreparedStatement process(StringTemplate st) throws SQLException {
        // 1. Replace StringTemplate placeholders with PreparedStatement placeholders
        String query = String.join("?", st.fragments());

        // 2. Create the PreparedStatement on the connection
        PreparedStatement ps = conn.prepareStatement(query);

        // 3. Set parameters of the PreparedStatement
        int index = 1;
        for (Object value : st.values()) {
            switch (value) {
                case Integer i -> ps.setInt(index++, i);
                case Float f   -> ps.setFloat(index++, f);
                case Double d  -> ps.setDouble(index++, d);
                case Boolean b -> ps.setBoolean(index++, b);
                default        -> ps.setString(index++, String.valueOf(value));
            }
        }

        return ps;
    }
}

    PreparedStatement ps = DB."SELECT * FROM Person p WHERE p.last_name = \{name}";
        ResultSet rs = ps.executeQuery();
```

## JEP 461: Stream Gatherers (Preview)
Stream API now has custom intermediate operations. This will allow stream pipelines to transform data.

###  you introduce the flowing built-in in the java.util.stream.Gatherers class:
- fold is a stateful many-to-one gatherer which constructs an aggregate incrementally and emits that aggregate when no more input elements exist.
- mapConcurrent is a stateful one-to-one gatherer which invokes a supplied function for each input element concurrently, up to a supplied limit.
- scan is a stateful one-to-one gatherer which applies a supplied function to the current state and the current element to produce the next element, which it passes downstream.
- windowFixed is a stateful many-to-many gatherer which groups input elements into lists of a supplied size, emitting the windows downstream when they are full.
- windowSliding is a stateful many-to-many gatherer which groups input elements into lists of a supplied size. After the first window, each subsequent window is created from a copy of its predecessor by dropping the first element and appending the next element from the input stream..

### Parallel evaluation
Parallel evaluation of a gatherer is split into two distinct modes. When a combiner is not provided, the stream library can still extract parallelism by executing upstream and downstream operations in parallel, analogous to a short-circuitable parallel().forEachOrdered() operation. When a combiner is provided, parallel evaluation is analogous to a short-circuitable parallel().reduce() operation.

### Composing gatherers
Gatherers support composition via the andThen(Gatherer) method, which joins two gatherers where the first produces elements that the second can consume. This enables the creation of sophisticated gatherers by composing simpler ones, just like function composition. Semantically,
```java
source.gather(a).gather(b).gather(c).collect(...)
is equivalent to
source.gather(a.andThen(b).andThen(c)).collect(...)
```

## JEP 462: Structured Concurrency (Second Preview)

The main objective is to simplify concurrent programming by introducing an API to structure it. By this you will be able to manage sets of subtasks running on different threads with a better observability.

If you look the shared example in the JEP here:
```java
Response handle() throws ExecutionException, InterruptedException {
    Future<String>  user  = esvc.submit(() -> findUser());
    Future<Integer> order = esvc.submit(() -> fetchOrder());
    String theUser  = user.get();   // Join findUser
    int    theOrder = order.get();  // Join fetchOrder
    return new Response(theUser, theOrder);
}
```

you have two concurrent tasks, each can fail and you need to wait until both have finished before returning.
The developer should manually coordinate the lifetime of these two subtasks and recover if anything goes wrong.


If Virtual thread allows spawning a ton of thread. Structured concurrency proposes to coordinate them and enable observability tools.
With the Structured Concurrency API building a maintainable, reliable and observable code(for a youb server for example) will be easier.


With these tools, you can refactor the previous example like this:
```java
Response handle() throws ExecutionException, InterruptedException {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        Supplier<String>  user  = scope.fork(() -> findUser());
        Supplier<Integer> order = scope.fork(() -> fetchOrder());

        scope.join()            // Join both subtasks
             .throwIfFailed();  // ... and propagate errors

        // Here, both subtasks have succeeded, so compose their results
        return new Response(user.get(), order.get());
    }
}
```

you find here a new StructuredTaskScope here, in a try/catch block, findUser and fetchOrder are forked in the StructuredTaskScope,
As in the first example, these tasks are executed in parallel but here you can use the API to call join methods and throw an exception if something goes wrong.

ErrorHandling, Cancelation, clarity and observability are guarantees by the StructuredTaskScope class API.
If an exception occurs the API will propagate the error and cancel the other thread.

StructuredTaskScope don't come alone, principally:
- StructuredTaskScope creates a new (virtual) thread for each subtask. A subtask can create its own nested StructuredTaskScope to fork its own subtasks, thus creating a hierarchy.
- Shutdown policies deal with concurrent subtasks. It is common to use short-circuiting patterns to avoid doing unnecessary work.
- Processing results: allow to manage composite results and permit processing subtasks results.

I'll let you discover the rest with Fan-in scenarios and Custom shutdown policies [here](https://openjdk.org/jeps/462#Custom-shutdown-policies)

This is the first time I summarize the JDK release, hope you enjoy it and you discover new things.
If I made a mistake let me know and contact me, nobody is perfect.
See you next release :)
