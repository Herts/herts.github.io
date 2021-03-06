---
layout: post
title: "Java 8 New Features With Codes"
date: "2019-07-21 19:00:57 -0700"
---

### Default Methods For Interfaces
Non-abstract methods can be added to interfaces:
```Java
interface Formula {
  default double calculate(int a) {return a + 2;}
}

public class MyFormula implements Formula { /* calculate does not need to be implemented*/}
```

### Lambda Expressions
[Lambda Exercises]()
```Java
public static <T> void sort(List<T> list, Comparator<? super T> c) {
    list.sort(c);
}

List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```
Instead of creating anonymous object ``new Comparator<String>``, lambda expression can be used here:
```Java
Collections.sort(names, (String a, String b) -> {
  return b.compareTo(a);
});

Collections.sort(names, (String a, String b) -> b.compareTo(a));

```
Using List.sort():
```Java
names.sort((a, b) -> b.compareTo(a));
```

#### Debugging
Use peek to check intermediate value
``.peek(val -> System.out.println(val))``

### Functional Interfaces
- Interfaces including one and only one abstract method.
- Annotation @FunctionalInterface is optional but will throw a compiler error when adding more methods to this interface.

```Java
@FunctionalInterface
interface Converter<T, F>{
  T convert(F from);
}
```
```Java
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123"); // 123
```

### References of Methods and Constructors
Passing references of methods:
```Java
Convertor<String, Integer> converter = Integer::valueOf;
```
Passing references of constructors:
```Java
class Person{
  String name;
  Person() {}
  Person(String name) { this.name = name; }
}

@FunctionalInterface
interface PersonFactory<P extends Person> {
  P create(String name);
}

PersonFactory<Person> personFactory = Person::new;
Person person = personFactory.create("han");
```

### Lambda Scopes
#### Local variables
Final local variables can be accessed by lambda expressions.
- It does not need to be declared final
```Java
 final int num = 1;
 Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
 stringConverter.convert(3); // 4
```
- But it should be implicitly final.
```Java
int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
num = 3; // this violates implicitly final, so will not be compiled
```

#### Fields and static variables
Both can be accessed by lambda expressions.

#### Default Interface Methods
Cannot be accessed.

### Built-in Functional interfaces
#### Consumer
Takes one input, and produces one result.
```Java
Consumer<Integer> display = System.out::println;
display.accept(10); // => System.out.println(10)

Consumer<Integer> modify = i -> i * 2;
modify.andThen(display).accept(10); // => System.out.println(10*2)
```
#### BiConsumer
Takes two inputs, and produces one result.
```Java
BiConsumer<List<Integer>, List<Integer>> displayList = (list1, list2) -> list1.forEach(System.out::println);

BiConsumer<List<Integer>, List<Integer>> add = (list1, list2) -> {
    int min = list1.size() > list2.size() ? list2.size() : list1.size();
    for (int i = 0; i < min; i++) {
        list1.set(i, list1.get(i) * list2.get(i));
    }
};

List<Integer> list1 = IntStream.range(1,8).boxed().collect(Collectors.toList());
List<Integer> list2 = IntStream.range(3,9).boxed().collect(Collectors.toList());

add.andThen(displayList).accept(list1, list2);
```


### Optional
Nifty utilities to prevent ``NullPointerException``. Instead of returning ``null``, return an ``Optional``.


### Stream
Exercises for Stream: [Exercises to study Java Stream API](http://binkurt.blogspot.com/2017/10/exercises-to-study-java-stream-api.html)
#### The difference between ``IntStream.range(1,5)`` and ``StreamOf(1,2,3,4)``
boxed() must be called with ``IntStream``.
```Java
List<Integer> integerList =
        Stream.of(1,2,3,4)
        .map(i -> i * i)
        .collect(Collectors.toList());

integerList.forEach(System.out::println);

List<Integer> collect = IntStream.range(1, 5)
        .map(i -> i * i)
        .boxed()
        .collect(Collectors.toList());

collect.forEach(System.out::println);
```
#### Method: Eager or Lazy?
Lazy: return Stream object, like: ``.filter`` ``.map``
Eager: like: ``.collect``

#### Higher-Order Functions
A function either takes a function as input or returns a function as result.
