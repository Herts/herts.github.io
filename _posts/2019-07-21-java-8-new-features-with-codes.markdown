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
