# Chapter 1 - Advanced Class Design

## Access modifiers
 * private - only the same class can access
 * default - package private, can be viewed by all classes in same package
 * protected - default + subclasses can view
 * public - all goes

## Overloading and Overriding
 * Both happen when the name of the method is the same, case sensitive
 * overriding 
   - same arguments
   - access modifier same, or more open
   - return type must be covariant, ie. same or more restrictive (subclass of parent return)
   - if any checked exception is thrown, only same or more restrictive can be thrown. Unchecked exceptions are free game. Also, one can skip throwing the same exception. No new or broader.
 * overloading
   - different arguments, or types of arguments
   - priority - exact, superclass, larger primitive, autoboxed, varargs

## Static and final
 * final class can't be inherited from
 * final method can't be overriden
 * final abstract doesn't make any sense
 * static methods can be hidden via 'overriding', but can be accessed via instance/class name of a parent still
 * variables can be hidden similarly to static methods, and accessed via either inside parent class, or super

## Imports
 * `import static` is for importing static methods, so you don't have to use class name for calling the method.
 * normal imports are for classes
 * both can use wildcards

## instanceof
 * if there is no way for something to be of class X, compiler will fail. This only happens with classes, because for the moment of compiling a class the compiler has full picture of hierarchy.
 * `instanceof someInterface` will compile, and maybe fail at runtime - because some subclass can implement the interface

## toString, equals, hashCode
 * public String toString()
 * public boolean equals(Object obj)
 * public int hashCode()
 * equals has some 'rules' to be correct:
   - x.equals(x) (for nonnull)
   - y.equals(x) == x.equals(y) (for nonnull)
   - x.y and y.z then x.z (for nonnull)
   - many invocations of the same return the same
   - for any null reference passed to equals it should return false
   - for reference of wrong object passed, it should return false
 * hashCode shoudl return the same value when equals returns true, but doesnt have to return different for false

## Enums
 * name, ordinal, valueOf, last is case sensitive
 * Enums can have private/package private contructors, which are fired when first using the enum, with parameters specified next to a declaration of an enum value
 * specification of enum value can contain mini-abstract-class implementation, if the main body of enum has any abstract methods for example, or for overriding

## Nested classes
 * member inner class - defined where other members of a class are
   - any access modifier, any extend or implement, can be abstract, can be final
   - can have static final variables
   - cannot declare static anything - it only exists in context of an instance of the outer class
   - can access private members of a parent.
   - compiling the outer class will produce two .class files, one with $InnerName somewhere in there.
   - referencing members of parent class is weird, A.this.x
 * local inner classes - defined in a method
   - cant have access modifiers, as they only exist in a scope of a method invocation
   - can have static final variables
   - no static anything, even the class cant be static
   - can access all fields and methods of enclosing class
   - can access local variables of a method only when they are final, or effectively final
 * anonymous local inner classes - no name, only one instance, created at declaration point
   - can't both extend or imnplement interface, or implement many interfaces - declaration only mentions one class/interface we are instaciating
 * static nested class - a class defined as static, at members level of another class
   - can use access modifiers, be final, abstract
   - can't access members of enclosing class - it isn't tied to an instance of it, so it needs to be provided an instance to work on, just like any other code.
   - parent class creates sort-of namespace, as the inner class has to be calles something like Outer.Inner


# Design Patterns and Principles
 ## Interfaces
  * all things are public abstract, unless stated otherwise
  * can have public static final variables
  * can have public default methods with implementation
  * can have public static methods
  * can extend other interfaces
  * functional interface is an interface that contains a single abstract method, useful for lambda expressions. It can have other stuff too, ie. static fields, static methods, default methods.
  * class implementing many of the same default method must override the method, to provide a solution to multiple inheritance problem

 ## Lambda syntax
  * (Type name) -> { return something;}
  * Type and parentheses can be skipped if single parameter. 
  * {} block requires use of ; and return for the last statement, and ; for any other statemenets. If just one statement, it can be ommited, and the last and only thing will be returned.
  * if we want to return nothing, we do need to use a block, and {} or {return;}
  * if one argument mentions a type,, full on (Type a, Byte b) is required. All must mention a type. None or all. Types are usually taken from context, so if someone needs to add something specific, the compiler gives up and want all the info up front.

 ## Java beans
  * design principle for encapsulating data in an object
  * properties are private
  * getters for boolean may begin with is or get
  * setters begin with set
  * set/get ers start with set/get, then Pascal-cased property name

 ## Singleton
  * one object for a class, usually via static method and private constructor
  * lazy by creating the instance only when the static method is called
  * can require synchronized code block

 ## Immutable objects
  * private and final variables
  * all properties set in constructor
  * no setters
  * don't return mutable objects, copy muitable stuff in constructor
  * prevent overrides

 ## Builder pattern
  * chaining methods that return the modified builder, and finishing with build() to get a proper final product.

 ## Factory pattern
  * provide some parameters, and the factory will decide which object to create. The user doesn't really need to know what class exactly was used, it just needs a reference to a superclass or an interface.



# Generics and Collections
 * Arrays.asList(array) creates a List that is not an ArrayList, but is array-backed. Can't be resized, but most of other List operations work. Modifying the array modifies the list.
 * Collections.binarySearch/Arrays.binarySearch(list/array, thingToSearch) either reutrns index of found, or where-it-should-be, negated, and then subtracted one.

 ## Generic Classes/Interfaces
  * Created by added formal type parameter in angle brackets `public class Crate<T> `. Can have more than one!
  * parameter can be used anywhere in the class definition, specifying the return type, parameter type, etc.
  * specified when creating a reference to a object, eg. `Crate<Robot> robotCrate`
  * Erasure is used for (among other things/reason) compatibility. All mentions of formal type parameters are replaced with Object, and casts are added in appriopriate places by the compiler.
  * with parameters, due to erasure, we can't: call constructor, create an array, call instanceof, use primitive as generic, create static variables with generic type.

 ## Generic Methods
  * Can be used both for static and non static methods.
  * formal type parameter is declared before return type, eg. `public static <T> Crate<T> ship(T t){}`
  * Such methods can be called very explicitly, but dont have to. eg. `Box.<String>ship("package")` to call `public static <T> void ship(T t){}`

 ## Legacy
  * When using legacy code that doesn't use generics in eg. collections definitions, it can lead to some ClassCastException - and it is totally on user to fix it all, as the compiler can't deal with it.

 ## Bounds
  * Wildcard can be any type `?`. Means that any type is ok. eg. when we want to write a method that take list with anything. It is still better than writing a list without `<parameter>`, as we won't get any warning. 
  * Wildcard with an upper bound, `? extends SomeType` means that type can be used if it is SomeType, or something that is lower in hierarchy.
  * Both just-wildcard, and upper-bound wildcard are really dumb, thanks to erasure. They don't really know what they have, so they can't allow adding objects to the collection.
  * Lower-bounded wildcards, `? super SomeType`. SomeType or superclasses of it can be used.
  * Dumb thing about lower bounds is that the compiler doesnt really know what was asigned to `List<? super IOException>` was it `List<IOException>`, or `List<Exception>`, or `List<Object>`? Thus, it blocks adding Object or Exception to the list, only allowing the lowest (or lower than lowest) to be added - as they will definitely fit to whatever the list actually is.
  * Also, wildcards of any boundness can't be used on right hand side, when creating an instance of something. Or when defining a return type of a method - at these points it has to be concrete info!
  * Using a name for formal type parameter totally covers any meaning it had previously. Eg. if we had a class called B, and used <B> somewhere, it would be a generic parameter, not a class name in the context of that declaration.
  * < super Cos> or < extends Cos> MUST have `?`, cant use some other generic parameter instead of it

 ## Collections
  * List - ordered, with duplicated allowed, accesible by index
  * Set - no duplicates
  * Queue - ordered, processed in a specific manner, can have duplicates
  * Map - associate key to value, no duplicate keys

  * All Lists, Sets, Queues, etc are based on java.util.Collection, and maps are based on java.util.Map

  ### Collection methods
   * `boolean add(E element)`
   * `boolean remove(Object o)`
   * `boolean isEmpty()`
   * `int size()`
   * `void clear()`
   * `boolean contains(Object o)`


  ### List methods additions
   * `boolean add(E element)` - adds at the end
   * `void add(int index, E element)` - adds at specific position, pushing the rest by one
   * `E set(int index, E element)` - sets at index, returns whatever it replaced, or throwns IndexOutOfBound if used on index that doesn't exist in the list yet.
   * `E get(int index)`
   * `int indexOf(Object o)`
   * `int lastIndexOf(Object o)`
   * `void remove(int index)`

  ### NavigableSet methods
   * `E lower(E e)` - return element that is < e, or null if none
   * `E floor(E e)` - <= e, or null
   * `E ceiling(E e)` - >= e, or null
   * `E higher(E e)` - > e, or null
   
  ### ArrayDeque methods
   * `boolean add(E e)` - add at the end, true of exception
   * `E remove()` - remove and return, exception if empty
   * `E element()` - return next, exception if empty
   ---
   * `boolean offer(E e)` - add at the end, success returned
   * `E poll()` - remove and return, null if empty
   * `E peek()` - return, null if empty
   ---
   * `void push(E e)` - add at the top/front
   * `E pop()` - remove and return, exception when empty (same as remove)
   * `E peek()` - return, null if empty

  ### Map methods (since it isnt a Collection)
   * `void clear()`
   * `boolean isEmpty()`
   * `int size()`
   * `V get(Object key)`
   * `V put(K key, V value)`
   * `V remove(Object key)`
   * `boolean containsKey(Object key)`
   * `Set<K> keySet()`
   * `Collection<V> values()`
---
  * Nulls dont go to any queue, because methods use null as a special meaning
  * They also dont go to sorted stuff - TreeMap keys or TreeSet elements
  * Also cant go to an old implementation of HashTable


 ## Comparator vs Comparable

  ### java.lang.Comparable
   * `int compareTo(T t)`
   * implemented by classes, technically a functional interface, but only makes sense in classes.
   * Compares the instance being called to the one being provided.
     - equal == 0
     - current object is smaller than argument < 0
     - current is larger than argument > 0
   * Should tell the same as `boolean equals(Object o)`

  ### java.util.Comparator
   * `int compare(T o1, T o2)`
   * this is also functional interface, and it makes perfect sense to use it as such. Comparators gets passed around as a thing that will do the thinking part of sorting to many functions. Eg. to Arrays.sort, or Collections.sort
   * likewise, 0 if equal (this should be consistent with equals), negative if less than second, positive if greater than second
---
 * One thing to remember, binarySearch methods take a collection/array that is already sorted. By default with the natural order, but if the comparator is supplied, the array/collection should be sorted before the call, using the same comparator. Otherwise chaos, and answer is undefined.

 ## Method references
  * Omit the arguments, and just pass them to the method.
  * Has four formats
  * Static methods: `Collections::sort`
  * Instance method: `str::startsWith`
  * Instance method, without knowing the instance in advance: `String::isEmpty`. First argument is treated as the instance to be run on.
  * Constructors: `ArrayList::new`

 ## Java 8 additions to collection methods
  * `boolean removeIf(Predicate<? super E> filter)`
  * `void replaceAll(UnaryOperator<E> o)` - basically a map operation from streams, but on a collection
  * `void forEach(Consumer<E> c)` - take every element, do something, return nothing.
  * (On Map interface) `V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remapping function)` - called on a map instance, takes key, and value that is a candidate for new value. If key doesnt exist, or has null value, the remapping function is run, taking both old and new value and returning something else, but compatibile with Map instance, eg. concat. If the merge function is used, and returns null, the key is removed. Otherwise the computed result is set as the value of that key
  * (On map interface) computeIfPresent - run merge function only if key is found, and has nonnull value, using bifunction, returning a new one based on old and new value. If null is returned from mapping, remove the key, otherwise set the result
  * (On map interface) comoputeIfAbsent - likewise, but other way around.



# Functional Programming

|Interface|# of parameters|Return type|Single abstract method|Description|
|---------|-----|----|----|-|
|`Supplier<T>`|0|T|get|Take nothing, return something|
|`Consumer<T>`|1|void|accept|Eat argument|
|`BiConsumer<T, U>`|2|void|accept|Eat two things|
|`Predicate<T>`|1|boolean|test|Test argument, return bool|
|`BiPredicate<T, U>`|2|boolean|test|Test with two things, return one result|
|`Function<T, R>`|1|R|apply|Take something, return something else|
|`BiFunction<T, U, R>`|2|R|apply|Take two somethings, return yet another something|
|`UnaryOperator<T>`|1|T|apply|Take something, and return same type|
|`BinaryOperator<T>`|2 (T + T)|T|apply|Take two of something, and return something of the same type|

* Predicate/BiPredicate comes with handy default methods `and`, `negate`, `or` - for chaining predicates together.

## Optional
 * A box, can have value or not
 * Handful of methods
   - get(), value or exception
   - isPresent(), boolean
   - ifPresent(Consumer c)
   - orElse(T other)
   - orElseGet(Supplier s)
   - orElseThrow(Supplier s)
 * Optionals also have some of the stream-like methods for chaining, like map, filter, of. They do return an optional of result inside, so be wary of creating an Optional<Optional<Integer>> when you dont mean to. There is ofc flatMap as well, which would remove unnecessary layers

---
* All functional interfaces and optionals also come with separate classes/interfaces for primitives, or most of them.
* To be more precise for interfaces - only for double, int, long. Most of it is logical. There are no Bi functions, predicates, consumers. Sometimes the methods of the interface are as applyAsDouble. Only in suppliers, and unary/binary operator
* There are also some extra functions. Used for mapping between types of streams, for example. There are also ToXFunction/toXBiFunction, that takes two objects, T, U and returns a primitive. There are also Obj + primitive consumers, that ofc return void.
* The optionals for primitives come with such methods as getAsDouble(), getAsInt() etc.


## Streams
 * Basically there are three parts for using streams.
   - source - something that generates the stream
   - intermediate operations - some actions we take on the data
   - terminal operation - one, that generates something more meaningful, and is the actual call to action for a stream.
 * Streams are as lazy as they can be. In extreme, if there is no terminal operation, nothing will happen.
 * Infinite streams are a thing, and behave a bit more infinite. Sometimes, with some terminal operations, they just wont stop.

### Generating a stream
 
 * from list `list.stream()`, `list.parallelStream()`
 * from Array `Arrays.stream(array)`, `Stream.of(array/varargs/singleelement)`
 * `Stream.generate(Supplier)`
 * `Stream.iterate(1, n -> n + 2)`

### Intermediate Operations

 * `filter(Predicate<? super T> p)` - only streams what passed the predicate
 * `distinct()` - unique values
 * `limit(int n)`
 * `skip(int n)`
 * `<R> Stream<R> map(Function<? super T, ? extends R> mapper)`
 * `<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)` - takes every element of a stream, maps it to another stream, and then joins the resulting streams together.
 * `sorted()`. `sorted(Comparator<? super T> c)` - sorts the stream, either naturally, of with passed comparator. This is a good place to take note that method references are tricky bunch, and we cannot pass Comparator::reverseOrder here, as it doesnt fit the execution of that functional interface - method sorted expects some method, either lambda or method ref, that is aligned with Comparator functional interface, ie. two parameters with  <? super T> type. So we can rather write (Comparator.reverseOrder()) as that passes the whole comparator, and the sorted method witll find proper method on its own.
 * `peek(Consumer<? super T> action)` useful for, well, peeking into a stream. Be careful of side effects.

### Terminal operations
 
 * `count()`, returns long, is technically a reduction, doesn't work for infinite
 * `min(sth)`,`max(sth)` take a comparator, and return an Optinal<T>. Is a reduction, doesn't work for infinite.
 * `findAny()`, `findFirst()`, return Optional<T>. Work for infinite. Not reductions, as they dont always process all elements. FindAny is more usfeul in parallel, otherwise it works the same.
 * `allMatch(pred)`,`anyMatch(pred)`,`noneMatch(pred)`. All return boolean, they check if all/any/none elements match provided predicate. Might work for infinite, not reductions.
 * `forEach(consumer)` return nothing, not for infinite, not reduction
 * `reduce()` combines a stream into a single object. There are three signatures of this method. Two pertrain for normal processing, the last one is used for parallel processing:
   - `T reduce(T identity, BinaryOperator<T> accumulator)` - we start with an initial value, so if the stream is empty at that point, we have something to return.
   - `Optiona<T> reduce(BinaryOperator<T> accumulator)` - if we omit the identity, then we do have to return optional, otherwise either one element, or result of accumulator
   - `<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner)` - and the parallel one. It does use the accumulator to create intermediate reductions, and then combiner to combine them to a final result. Identity has to be an identify for the combiner. It can happen taht the identity is used multiple times by accumulator, making some weird questions possible.
 * `collect()`, a special case of reduction, called mutable reduction. Called so because we use a mutable object while collecting, eg. StringBuilder, ArrayList. Two method signatures:
   - `<R> R collect(Supplier<R> supplier, BiConsumer<R, ? super T> accumulator, BiConsumer<R, R> combiner)` - very down to the metal method invocation, step by step - supplier usually is what creates an object, mutable, that will be used to collect. Accumulator take that mutable thing, and stream element, and mush it together. Collector takes two bits of mutable objects, and merges them together - for the parallel world out there.
   - `<R, A> R collect(Collector<? super T, A, R> collector)` - this one uses collectors provided. Usually in `Collectors` class.
 * There are many Collectors.X to choose from. We use them as such : `someStream.collect(Collectors.joining(", "))`:
   - Double averagingDouble(ToDoubleFunction) - all three first map whatever you have to primitive with provided function, then return an average
   - Double averagingInt(ToIntFunction)
   - Double averagingLong(ToLongFunction)
   - X summingX(toXFunction) - calculates sum
   - XSummaryStatistics summarizingX(ToXFunction) - gets the summary, mapping stuff to primitive first
   - Long counting() - counts elements
   - Optional<T> minBy/maxBy(Comparator c) - returns min/max
   - toList() - gets you any list
   - toSet() - gets you any set
   - toCollection(Supplier s) - you can specify the type
   - toMap(Function K, Function V, BinaryOperator m, Supplier s) - K and V are functions to generate key, and value, from stream element. Supplier is optional, and provides a way of creating map object. BinaryOperator is also optional, and is a mapping function used when two thingies have the same key, and the method doesn't know which value to pick.
   - goupingBy(Function f, Supplier s, Collector dc) - creates a map, using the result of f as a function, and list of stream values that returned that key as a value for that key. Optional supplier of type of returned collection. And optional downstream collector, for for example changing the type of value-collection to something else :
     - `Collectors.groupingBy(String::length, TreeMap::new, Collectors.toList())`
   - partitioningBy(Predicate p, Collector dc) - takes a predicate, and optional downstream collector, and returns a map of <Boolean, Something<T>>. This is pretty much groupingBy, but specialized. Note that true and false keys will always be in the result map.
   - mapping(Function f, Collector dc) - first maps the elements of a stream, then collects them using the downstream collector. Useful forr... some chakra magic, or eg. sub operation of yet another collecting.
     

### Primitives
 
 * There are special versions of stream class for three most used primitive types:
   - IntStream
   - LongStream
   - DoubleStream
 * They come with some handy methods, like average, sum, or whatnot. They do make use of primitive optional types at times.
 * There is also summaryStatistics, of class IntSummaryStatistics (or similar) - it calculates such things as min, max, avg, size, number of values in one go.
 * There are many ways of creating such streams:
   - empty
   - of
   - generate
   - iterate
   - range
   - rangeClosed
   - via mapping from other stream type. To create X from X, you use map. To create X from Y, you use either mapToObj in case of Y being normal stream, or mapToY in any other stream. The methods are overloaded with many types of parameters, such as IntToDouble, DoubleFunction, ToDoubleFunction
   - There are also flatMapToInt etc

 
 
# Dates, Strings, Localization

  * Four classes, in java.time.*, LocalDate, LocalTime, LocalDateTime, ZonedDateTime
  * Factory static methods (usually of(parameters)) for construction. All these classes are immutable.
  * *Date* classes support plusDays/Months/Weeks/Years,
  * *Time* classes support plusHours/Minutes/Nanos/Seconds
---
  * Period class is used to represent a long period of time, years, months, weeks, days. Stored are years, months, and days.
  * Many static methods exist, ofYears, etc. but they are not chainable. There is of(int years, int months, int days), used for more sensible creation.
  * Instance of a period also has plus/minus methods
  * String representation of a period looks like this : `P1Y2M3D`, with empty being skipped, eg. `P10Y40D`
---
  * Duration class is used to represent smaller units of time. Days, hours, Minutes, seconds, nanoseconds. Stored only hours, minutes, and seconds. The last ones can have fractions.
  * Similar static methods. Book also mentions the use of more generic factory method (which is available for Period as well) of(long amount, TemporalUnit unit)
---
  * Duration can be added to any Time class, Period to any Date class
---
  * Instant is used to represent a point in time. You can create it from ZonedDateTime, for example. Or from Epoch second. You can also do some math on it, but just on Day or smaller units..
---
  * ZonedDateTime knows about daylight saving, and adding some time/ creating instance of a time that should exist will automagically switch to correct moment/timezone
---
  * String literals are pooled
  * String BUFFER is thread safe. fff = saffe

## Localization
 * Internationalization == i18n, process of desigining your program so it can be adapted to different languages/etc
 * Localization == l10n means actually supporting multiple locales
 * Locale lives in java.util package.
 * Default for jvm running can be got via Locale.getDefault()
 * `language_COUNTRY` usually 2 (or someetimes 3 for language) letters each. Language must be lower case. Country uppercase. Country (and underscore) are optional
 * Locale comes with Locale.Builder().setRegion("US").setLanguage("en").build() methods.
 * Default locale can be changed for current runtime.
---
 * Resource Bundle is a local specific pack of objects to be used by the program. Essentialy a properties file, or java class, to hold all the strings that might require some translation or other preparation.
 * ResourceBundle.getBundle("Zoo", new Locale("en", "US")). If locale not provided, default will be used.
 * For more than just string values, java classes can be used, by creating a class with a name that you would use for a property file, and extend ListResourceBundle. It has one abstract method, getContents that returns Object[][] - there we will return whatever.
 * Name of property file/java class looks something like `Name_country_REGION.java/properties`
 * java classes have higher priority
 * exact match have higher priority
 * then we drop the region, and look for just language, again, java first.
 * then we go to default, again, java first
 * then we drop region on that
 * then we drop locale, and look for default bundle with just Name.java, Name.properties,
 * then we give up and MissingResourceException.
 * properites can also come from parent of the found bundle. Eg. if found one is Zoo_fr_FR, the props can come from Zoo_fr_FR, Zoo_fr, Zoo
---
 * java.text package comes with ways of formatting different things than just text. One of them is formatting Numbers and Currency, etc.
 * NumberFormat.getInstance(), .getNumberInstance, getCurrencyInstance, getPercentInstance, getIntegerInstance
 * `format()` to turn into string, `parse()` to read string. Parse will throw ParseException if fails, and it is checked exception! But it only does so when it didn't get anything good to return. So eg. 123123213xsdosdf will parse correctly to a number, but x12312 wont.
---
 * Formatting dates and times is made more sane with java.time.DateTimeFormatter.
 * It has some static variables, like ISO_LOCAL_DATE, ISO_LOCAL_TIME, ISO_LOCAL_DATE_TIME
 * And some static methods for creating formatters, `ofLocalized``Date/Time` + some `FormatStyle`, and used to format date/time classes as such `formatter.format(dateObj)`. It will go tits up if provided with wrong object. Throws runtimeException, DateTimeException. It can do all-or-less, but cant go beyond what is available. 
 * LocalDate/Time objects have .parse method, that takes string and optional formatter to parse the input. Don't have to specify the formatter if we have standard input.


# Exceptions and Assertions

 * Every exception is a Object, and a Throwable.
 * then we have a split to Errors, which are SERIOUS SHIT, and should not really be handled.
 * and to Exceptions, which further have RuntimeException.
 * If something is an Exception, but not a RuntimeException as well, then it is checked exception and has to be handled or declared.
 * Need to know these checked exceptions:
   - `java.text.ParseException`
   - `java.io.IOException`
   - `java.io.FileNotFoundException`
   - `java.io.NotSerializableException`
   - `java.sql.SQLException`
   - `java.lang.InterruptedException`
   - `java.util.concurrent.TimeoutException`
 * And these unchecked exceptions:
   - `java.lang.ArithmeticException`
   - `java.lang.ArrayIndexOutOfBoundsException`
   - `java.lang.ClassCastException`
   - `java.lang.IllegalArgumentException`
   - `java.lang.NullPointerException`
   - `java.lang.NumberFormatException`
   - `java.lang.ArrayStoreException`
   - `java.time.DateTimeException`
   - `java.util.MissingResourceException`
   - `java.lang.IllegalStateException`
   - `java.lang.UnsupportedOperationException`
   - `java.lang.ConcurrentModificationException`
 * try clause has to have either catch or finally or both
 * catch blocks are processed in order
 * declaring a subclass of an exception defined above is not legal, and wont compile
 * declaring a checked exception in cathc block that can not be thrown by anything in the try block is not legal, and wont compile
 * Multi-catch looks like `catch (Exception1 | Exception2 identifier)`. Just one identifier, at the end.
 * Multi-catch is intended for classes that are not in one inheritance tree. So eg. FileNotFoundException and IOException in one multicatch wont compile.
 * You cant reassign the value of identifier in multi catch. As it might be two or more different thing, java stops you from causing even more chaos
 * try-with-resources has implicit finally block, so you can have nothing in try-with-resources, but you can add as many catch blocks, and up to one finally blcok yourself
 * You can only put stuff that implements AutoCloseable into try-with-resoures. It has just one method, `public void close() throws Exception;`. Remember, that interface can declare throws, and we can omit it in implementation, or add sub-exception, but never add another checked exception.
 * If that interface method declares a checked exception, then it needs to be handled or declared
 * Old interface, `Closeable` has two more requirements - only allows IOException, and requires implementations to be idempotent.
 * There is a way to accumulate exceptions. All but first thrown are called *suppressed exceptions*. Eg. in try block in try-with-resources something causes an exception. We catch that, but at the moment of leaving try block, the resources get auto-closed. If they throw exception, it gets tacked to the main exception as suppressed, and can be accessed via e.getSuppressed()
 * This is not the case everywhere, as finally block throwing a different exception is another stroy, another exception altogether, and wont add the exceptions as suppressed.
 * Resources are closed in reverse order, at the moment code execution leaves the try block, before catch/finally

## Assertions
 
 * syntax:
   - `assert boolean_expression;`
   - `assert boolean_expression: error_message;`
 * can have optional parenthesis around the boolean expression. Error message is a string. Throws java.lang.AssertionError when false!
 * Assertions have to be enabled via `java -enableassertions Rectangle` or `java -ea Rectangle`. One can also add arguments:
   - `java -ea:com.wiley.demos... my.programs.Main` - to enable it for com wiley demos package, and any subpackage, etc.
   - `java -ea:com.Wat my.programs.Main` to enable it only for com.Wat class
 * You can also disable them with `-da` for specific classes/packages



# Concurrency
 * Functional interface Runnable (method run())takes nothing, and returns nothing. Often used to define a piece of work to be executed by the thread.
 * Thread is used to execute a task. Either by providing a Runnable object/lamba in constructor of the Thread class, or by extending Thread and overriding run method.
 * start() to start, at some point in time in future
 * Thread.sleep(int x) throws checked InterruptedException

## ExecutorService
 * An interface, which creates and manages threads, pooling, scheduling, etc.
 * In java.util.concurrent
 * Obtained via factory methods
 * single thread executor `Executors.newSingleThreadExecutor();`
 * You pass a runnable to `.execute()`
 * order of tasks preserved, but other threads (like main of the program) still run independently
 * Need to be closed with `.shutdown()` method. It is a long process, so it takes some time. There are two bool methods, `isShutdown` and `isTerminated`. Terminated means it is done, and shutdown means that the process of shutdown started. During that process, no new tasks can be scheduled, and RejectedExecutionException runtime exception will be thrown. All tasks that were already submitted will still be processed!
 * There is also `shutdownNow()` that attempts to stop all running tasks
 * Other ways of submitting tasks to ExecutorService implementations :
   - `void execute(Runnable r)`
   - `Future<?> submit(Runnable r)` - future represents the task, no result
   - `<T> Future<T> submit(Callable<T> c)` - future represents pending result of the task
   - `<T> List<Future<T>> invokeAll(Collection<? Callable<T>> tasks) throws InterruptedException` - execute all tasks, returning list in the same order as collection, when all complete. Also comes with timeout version, TimeUnit parameter.
   - `<T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException` - returns the result of one that has completed successfully, without exception. This also comes with version with timeout. It cancels the unfinished tasks.
 * Future comes with useful methods :
   - `boolean isDone()`
   - `boolean isCancelled()`
   - `boolean cancel()`
   - `V get()` - waits for result if not available
   - `V get(long timeout, TimeUnit unit)` - waits a specified time, or TimeoutException, checked.
 * Callable can throw Exception, Runnable cannot.
 * Callable lives in java.util.concurrent, Runnable in java.lang.
 * ExecutorService has `awaitTermination` for waiting for all tasks to finish in shutdown mode, for example.
 * Also comes with a way to schedule tasks in the future. Either callable(only for one off) or runnable, one off after delay, or repeating at scheduled interval, after initial delay, or with specified delay after finishing last one.
 * Moar ExecutorServices:
   - newSingleThreadedExecutor
   - newCachedThreadPool
   - newFixedThreadPool(int num)
   - newSingleThreadedScheduledExecutor
   - newScheduledThreadPool(int num)
 * Last two return ScheduledExecutorService, and only they can schedule commands

## Synchronizing Data Access

 * java.util.concurrent.atomic package to help coordinate access to primitive values and object references.
 * Comes with AtomicBoolean/Integer/IntegerArray/Long/LongArray/Reference/ReferenceArray
 * and a handful of methods, that behave like an atomic operation get/set/getAndSet/incrementAndGet/getAndIncrement/decrementAndGet/getAndDecrement
---
 * synchronized blocks 'lock' on a specified object
 * as long as the same object is used, many executions will wait for a block to be free of any other execution
 * synchronized method is syntax sugar, same as block inside of whole of method, with *this* as object
 * for static methods the ClassName.class is used
---

## Concurrent Collections
 * There are few types
  - Concurrent
  - Blocking
  - SkipList
  - CopyOnWrite
  - Synchronized
 * ConcurrentHashMap, ConcurrentLinkedQueue, ConcurrentLinkedDeque are prtty much like normal. These are the full-on concurrent, all operations are thread safe.
 * Blocking Queues, as LinkedBlockingDeque and LinkedBlockingQueue - they are like a regular queues, but they include methods with timeouts:
   - `offer(E e, long timeout, TimeUnit unit)` return false if timeout 
   - `poll(long timeout, TimeUnit unit)` return null if timeout
   - offerFirst, offerLast, pollFirst, pollLast
 * SkipList collections, ConcurrentSkipListSet, ConcurrentSkipListMap - They are pretty much just TreeSet TreeMap. SkipList is a nod to implementation detail?
 * CopyOnWriteArrayList, CopyOnWriteArraySet - they copy all of their elements to a new structure any time an element is added/swapped/removed. The reference to the collection doesnt change - so for us it is transparent, but still useful for multi-threading that need to iterate - iterator iterates over the original elements, prior to any modifications taht happend after its creation.
 * Synchronized collections are created via `Collections.synchronizedX(X x)`, out of a standard non-synchronized collection. Should not be used if we know up front that we need concurent one. They only synchronize access to data elements, such as get and set, but DO NOT synchronize access on any iterators!

## Parallel Streams
 * Created either from existing stream with `parallel()` or from a collection with `parallelStream()`
 * forEachOrdered comes into more handy here, as it brings back the lism back to orderness of normal stream
 * stateful operations are not recommended
 * parallel reductions are unpredictable, when used with operations that are not based on order. Some operations are though, eg. findFirst, limit, skip - they might perform slower in parallel, thanks to overhead, but do return predictable result.
 * side note, any stream can be forced to be unordered, and typcial order-liking operations will just do whatever, skip random 5 elements.
 * parallel reduce() works better than one would think. Java is smart enough to not lose some order, as long as some rules are adhered. As you remember, there is (identity, accBiFunction, combinerBinaryOperator). First all stream elements are accumulated, then the intermediate results are combined. Rules:
   - identity must be an identity for combiner
   - accumulator must be associative and stateless - a acc (b acc c) == (a acc b) acc c
   - combiner also associative and stateles, and compatibile with identity comb(u, i acc t) == u acc t
 * parallel collect() - also weird, sometimes will fall back on ordered, sync way. Three argument operation works nicely if you provide proper stuff, rules from previous point applying. Each Collector has some characteristics, Collector.Characteristics.CONCURRENT and UNORDERED, that will work nicely for ordered stream, and concurrent stream. There are two defined that are like that, toConcurrentMap and groupingByConcurrent

## Managing Concurrent Processes
 * CyclicBarrier is used as a waiting point for a specified number of threads. `new CyclicBarrier(int num, optionalRunnable r)
 * Can lead to deadlock if you cant math
 * Fork/Join framework requires us to perform three steps:
   - Create a ForkJoinTask (via extending RecursiveAction or RecursiveTask)
   - Create ForkJoinPool
   - Start the ForkJoinTask
 * RecursiveAction returns void
 * RecursiveTask returns generic type
 * they come with compute() method to perform bulk of the work
 * In this method in RecursiveAction we decide if chunk of work is too big, and if it is, create two (or mroe) instances of our class, and invoke them with invokeAll(childTask1, childTask2)
 * Then we can initiate ForkJoinPool, and tell it to invoke our main task, which might spawn child tasks etc.
 * The RecursiveTask has the 'Join' part, as it returns the result. It works basically the same, but we need to invoke it a bit differently.
 * Eg. we create two child tasks. Run one with task.fork(), to tell the framework to run it in separate thread (of the pool), then another with .compute(), to run it in this thread, then task.join() to wait for result from second thread. And of course the pool.invoke(parentTask) will return a result.

## Threading problems
 * Deadlock - when two threads are waiting for things, blocked by the other thread
 * Starvation - when a thread is denied access to shared resource, or processing power
 * Livelock - two or more threads are blocked, but still active. Eg. they cant access a lock, so they start over. Often difficult to detect, as some work seems to be getting done, but not quite.
 * Race conditions - can lead to invalid data, if not handled properly. Eg. when two threads want to do the same thing, and two accounts with the same would be created.



# IO
 * File class can represent a file or directory in the system
 * Useful methods that might come up:
   - `exists()`
   - `getName()`
   - `getAbsolutePath()`
   - `isDirectory()`
   - `isFile()`
   - `length()`
   - `lastModified()` returns num of miliseconds
   - `delete()` if directory, it has to be empty, otherwise exception
   - `renameTo(File)`
   - `mkdir()`
   - `mkdirs()`
   - `getParent()` - abstract pathname of this abstract pathname's parent, or null if this pathname does not name a parent directory
   - `listFiles()` - returns a File[] array of files in this directory
 * Streams are all in the end binary, but they are represented in java as either byte, or character based.
   - InputStream/OutputStream abstract classes represent the byte ones
   - Reader/Writer abstract classes represent the character ones
 * We also categorize them into Low-level, and High-level streams. 
   - Low-level (for the exam) start with `File`, as in, they are the simplest streams that read stuff from file, or write to it. So FileInputStream, FileOutputStream, FileReader, FileWriter. There are other low level streams, that talk to other sources of data, such as Array, String
   - High-level is any other stream, that uses lower-level or different high-level stream in constructor (wrapping)
 * Most are symmetrical, as in they come with input and output, reader and writer. There are some exceptions (from what we need to know at the exam)
 * Other than low level stuff, we have:
   - BufferedReader
   - BufferedWriter
   - BufferedInputStream
   - BufferedOutputStream
   - ObjectInputStream
   - ObjectOutputStream - object stuff only comes with byte version, as objects cant really be represented with characters easily
   - PrintStream 
   - PrintWriter - outputing to a console/terminal is one way only
   - InputStreamReader - combos, that make a Reader/Writer by wrapping InputStream/OutputStream
   - OutputStreamWriter
 * There are few basic operations on streams:
   - `close()` - we do need to close the stream after we are done with it. try-with-resources does a good job here
   - `flush()` - to push the data that was sent to outputstream to actual data source/file. close() automagically calls it as well
   - `mark(int n)` - supported by some of the stream classes, checked with `markSupported()` method. Useful for marking a point and a distance ahead, and then using `reset()` to revert to marked point. If we go over what we declared, exception MIGHT be thrown.
   - `skip(long)` for skipping data
 * ObjectInputStream and ObjectOutputStream make use of serialization. For an object to be serializable, it has to implement empty interface Serializable, and have all members either serializable, or marked transient
 * Deserializing a object skips any constructors, only invoking first found in hierarchy empty argument constructor, in non-serializable class. Transient fields wont be initialized, any default initializations are skipped too.
 * PrintStream and PrintWriter have a nice methods for printing stuff, that, unlike write() do not throw IOException:
   - `print(stuff)`
   - `println(stuff)`
   - `format(String format, Object args...)`
   - `printf(String format, Object args...)`
 * System.out returns PrintStream
 * System.in returns some InputStream, if you chain it with BufferedReader, you can use readLine()
 * There is new nice java.io.Console, which is basically a singleton for all that communication, better, newer, and yet still everyone uses System.in and out.. System.console() might return null...
   - `reader()` - this one uses Reader
   - `writer()` - and this one Writer
   - `format()`, `printf()` - where format is just format, args. No version with locale like in PrintStream/PrintWriter
   - `flush()`
   - `readLine()`
   - `readPassword()` - returns stuff into an char array, coz strings are cached.



# NIO.2
 * java.nio.file.Path is an improvement over java.io.File. It comes with support for symbolic links, a handful more of nice methods, etc. They are also used for representing other things than just files, like URIs
 * Path objects are created via factory method in Paths class, or from underlying FileSystem classes.
 * `Paths.get()`
 * URI constructor has to have an absolute path for files, the constructor of URI will throw exception at runtime if not provided such path. The exception is a checked URISyntaxException
 * `FileSystems.getDefault().getPath(...)` to get path of a filesystem, or we can also get a FileSystem of a different system, via `FileSystems.getFileSystem(new URI(...))`
 * File file = new File(...); `file.toPath()`
 * `path.toFile()`
 * Common optional options for methods:
   - `NOFOLLOW_LINKS` - used by reading, copying, moving, and checking if file exists
   - `FOLLOW_LINKS` - used by traversing a directory tree, which might add a lot of work, or lead to some cyclic stuff, and exceptions.
   - `COPY_ATTRIBUTES` - used by copy
   - `REPLACE_EXISTING` - used by copy and move
   - `ATOMIC_MOVE` - used by move, not supported everywhere, if not, exception.
 * Path methods:
   - `toFile()`
   - `toUri()`
   - `toString()`
   - `getNameCount()` returns number of segments of current path
   - `getName(int)` retuns i-th segment, as a Path object!
   - `getFileName()` basically the same as getName(last index)
   - `getParent()` parent or null, if no parent. End-of-relative means that there is no parent, it wont look into default location or whatnot
   - `getRoot()` root, or null, if relative
   - `isAbsolute()`
   - `toAbsolute()` joins the current directory with path provided, if not absolute already
   - `subpath(int int)` is like get name, but with range, closed:open, or runtime exception IllegalArgument
   - `relativize(path2)` constructs a new Path object, a Path between two Paths!. Both paths need to be of the same absolute status. If one is absolute and one isnt, an exception will be thrown.
   - `resolve(path2)` creates a new path by joining two paths. It basically appends the path2 at the end of path1. If path2 is absolute, it totally overwrites the path1.
   - `normalize()` gets rids of . and .. 
   - `toRealPath()` takes a Path, that we are not sure is pointing to a real path, and converts the path to an absolute, normalized, path that points to an actual thing in file system, ooor throws IOException at runtime if not found. By default it follows links
* Files methods:
  - `exists(Path)` checks whether path thing exsts, and returns bool
  - `isSameFile(Path, Path)` checks if two paths point at the same thing. Doesnt actually check the content. If the equal() test fails, it will check whether the files exist in the filesystem
  - `createDirectory(Path)`, `createDirectories(Path)`. IOException if something goes wrong, ie. no parent for 1st one, or already existing
  - `copy(Path, Path)` - copy file, by default follows links, and doesnt replace, and doesnt copy attributes. IOException if not found, or cant be copied etc.
  - `move(Path, Path)` moves, follows by default, not replaces, not atomic. Can be changed, some IOExceptions like copy, and AtomicMoveNotSupportedException
  - `delete(Path)` tries to delete, or fails with IOExc
  - `deleteIfExists(Path)` false if doesnt exist, true if success, exception if something else is wrong
  - `newBufferedReader(Path, Charset)`
  - `newBufferedWriter(Path, Charset)`
  - `readAllLines(Path)` returns a list of strings
  - `walk(Path)` returns a stream of files in directory, or itself only, if it is just this file. Depth first, max Integer.MAX_VALUE, or specified. 0 is just that file. Does NOT follow links by default, to avoid circular paths.
  - `find(Path, int, BiPredicate)` - walk and filter basically, but we have BiPredicate that gets the BasicFileAttributes as well!
  - `list(Path)` streams paths of one level deep walk
  - `lines(Path)` streams file lines
---
 * Attributes are additional info of a file, or a thing in the file system. 
 * Files class comes with some static methods. These three check wether file exists, and return fale if so:
   - `isRegularFile(Path)`
   - `isDirectory(Path)`
   - `isSymbolicLink(Path)`
   - `isHidden(Path)` this one might throw IOException, as there might be a issue while reading that info
   - `isReadable(Path)` also false if no file
   - `isExecutable(Path)` also false if no file
   - `size(Path)`
   - `getLastModifiedTime(Path)` returns instance of FileTime
   - `setLastModifiedTime(Path, FileTime)` FileTime has a method fromMillis
   - `getOwner(Path)` return UserPrincipal
   - `setOwner(Path)`
   - `readAttributes(Path, BasicFileAttributes.class)`
   - `getFileAttributeView(Path, BasicFileAttributeView.class)`
 * Access views is an object with all that shiet, so we dont have to make many trips to filesystem. One pair of classes we need to know is 
  - `BasicFileAttributes` - comes with methods above, and few new ones, for reading attributes
    - `isOther()`  checks if file is something other than dir/file/symlink
    - `lastAccessTime()`
    - `creationTime()`
    - `fileKey()` returns unique id for the file within file system, or null if not supported
  - `BasicFileAttributeView` - for modifying set of date time values, and has readAttributes to get BasicFilesAttributes for reading. Comes with setTimes(lastMod, lastAccess, create). Nulls wont change what is already set.




# JDBC
 * Interfaces to remember in JDBC
  - `Driver`
  - `Connection`
  - `Statement`
  - `ResultSet`
 * DB Driver comes with implementations of these interfaces, but we dont really have to know about exact class names, as we will be using factories for most stuff.
 * Exam requires to use `DriverManager` for getting connection via `DriverManager.getConnection(URL)`
 * JDBC URL is made up like this :
   - `protocol:product/vendor name:db specific connection details`
   - the details have to actually make sense, network or name wise
   - protocol will usually be jdbc
   - product/vendor name doestn really matter, can have multiparts with more :
 * Driver should be found automgaically via the jar with the driver having `META-INF/services/java.sql.Driver` file with fully qualified package name of the Driver implementation. In older jdbc (<= 3) implementations, the presence of that file was optional, and we had to use `Class.forName("qualifiedPackageNameAndClass")`
 * Once we have a connection, we can create a statement via (optional parameters) `conn.createStatement(TYPE_OF_RESULT_SET, RESULT_SET_CONCURRENCY_MODE)
   - `TYPE_FORWARD_ONLY` - cant go backwards
   - `TYPE_SCROLL_INSENSITIVE` - can go both ways
   - `TYPE_SCROLL_SENSITIVE` - can go both ways, and gets fresh data if the stuff changes in db. mostly unimplemented in drivers.
   - `CONCUR_READ_ONLY`
   - `CONCUR_UPDATABLE` - usually not implemented, used for updating result via ResultSet
 * `stmt.executeUpdate("asdf")` returns an int
 * `stmt.executeQuery("asdf")` returns a ResultSet
 * `stmt.execute("asdf")` can run everything, and returns true if it was select, false if something else. Then you can get ResultSet via `stmt.getResultSet()` or count by `stmt.getUpdateCount()`
 * Columns count from 1
 * or by column name
 * a method for most primitives. Date/Time sql classes come with nice methods converting them to java8 java.time classes `toLocalDate`, `toLocalTime`, `toLocalDateTime`
 * ResultSet starts before first record, must be moved with `rs.next()`
 * Scrollable result sets come with more methods. If run on non-scrollable, an exception will be thrown. Boolean response represent whether the result set point to a record, or somewhere outside.
   - `boolean absolute(int)` move to specified row number. Also uses negative numbers to go from the end, -1 being the last record. 0 is before first record.
   - `void afterLast()`
   - `void beforeFirst()`
   - `boolean first()`
   - `boolean last()`
   - `boolean next()`
   - `boolean previous()`
   - `boolean relative(int)` relative to current
 * DB resources need to be closed, preferably in proper order. The higher-up things should close their own kids themselves, but it isnt bad to close them.
 * Result set is closed once another statement is run from the same statement object
 * SQLExceptions are all checked, and have some methods:
   - `e.getMessage()` of course
   - `e.getSQLState()`
   - `e.getErrorCode()`
