# Core Java Notes

---

## Table of Contents

1. [Procedural Programming vs OOP](#1-procedural-programming-vs-oop)
2. [Object vs Class](#2-object-vs-class)
3. [Principles of OOP](#3-principles-of-oop)
4. [Object Relationships](#4-object-relationships)
5. [3 Main Components of Java](#5-3-main-components-of-java)
6. [Main Method](#6-main-method)
7. [Comments](#7-comments)
8. [Compile and Run](#8-compile-and-run)
9. [Why Only 1 Public Class Per Java File](#9-why-only-1-public-class-per-java-file)
10. [Variables](#10-variables)
11. [Wrapper Classes](#11-wrapper-classes)
12. [Constant Variables](#12-constant-variables)
13. [Methods](#13-methods)
14. [Constructor](#14-constructor)
15. [Types of Classes](#15-types-of-classes)

---

## 1. Procedural Programming vs OOP

### Procedural Programming

1. Code organized as a sequence of functions/procedures
2. Data and functions are separate
3. Top-down approach
4. Data is passed between functions
5. No access control on data (everything is accessible)
6. Example: C, Pascal

### OOP

1. Code organized around objects (data + behavior together)
2. Data is bundled with the methods that operate on it
3. Bottom-up approach
4. Objects communicate via messages (method calls)
5. Access control via encapsulation (public, private, protected)
6. Supports inheritance, polymorphism, abstraction
7. Example: Java, C++, Python

> Key difference in one line: Procedural thinks in steps, OOP thinks in things.

> Top-down breaks the big problem into smaller sub-problems first, bottom-up builds small reusable pieces first and combines them into the bigger solution.

---

## 2. Object vs Class

### Class

1. Blueprint/template for creating objects
2. Defines properties and methods
3. No memory allocated until object is created
4. Declared once
5. Example: `Car` (the design)

### Object

1. Instance of a class
2. Holds actual data in memory
3. Created multiple times from the same class
4. Has state (values) and behavior (methods)
5. Example: `myCar = Car("Toyota", "Red")` (the actual car)

---

## 3. Principles of OOP

### 3.1 Encapsulation

- Wrapping data and methods together in a class
- Hiding internal details, exposing only what's needed
- Achieved via access modifiers (private, public, protected)
- Also known as data hiding

**Steps to achieve encapsulation:**

1. Declare class variables as private
2. Provide public getter methods to read the data
3. Provide public setter methods to modify the data
4. Add validation/logic inside setters if needed to control how data is changed

> In one line: private data + public getters/setters = encapsulation

**Advantages:**

1. Data hiding — internal state is protected from direct outside access
2. Controlled access — getters/setters let you validate before changing data
3. Flexibility — change internal implementation without breaking external code

**Access Modifiers:**

| Modifier | Same Class | Same Package | Subclass (diff pkg) | Everywhere |
|-----------|-----------|-------------|-------------------|-----------|
| private | ✅ | ❌ | ❌ | ❌ |
| default (no keyword) | ✅ | ✅ | ❌ | ❌ |
| protected | ✅ | ✅ | ✅ | ❌ |
| public | ✅ | ✅ | ✅ | ✅ |

- **private** — accessible only within the same class. Most restrictive.
- **default (package-private)** — accessible within the same package only. Applied when no modifier is specified.
- **protected** — accessible within the same package + subclasses (even in different packages).
- **public** — accessible from everywhere. Least restrictive.

### 3.2 Abstraction

- Showing only essential features, hiding complexity
- Example: you drive a car without knowing engine internals
- Achieved via abstract classes and interfaces
- Advantage: increases security and confidentiality

### 3.3 Inheritance

- One class acquires variables/methods of another
- Promotes code reuse
- Example: `Dog extends Animal`
- Parent class cannot access child class methods or variables
- Inheritance can be achieved using `extends` keyword or through interface

```java
// Class inheritance
class Animal {
    void eat() { System.out.println("eating"); }
}
class Dog extends Animal {
    void bark() { System.out.println("barking"); }
}

// Interface implementation
interface Runnable {
    void run();
}
class Dog extends Animal implements Runnable {
    void bark() { System.out.println("barking"); }
    public void run() { System.out.println("running"); }
}
```

**Key points:**

1. `extends` — inherit from a class (single inheritance only, one parent)
2. `implements` — inherit from interface(s) (multiple allowed)
3. Java doesn't support multiple class inheritance (no `extends A, B`)
4. A class can extend one class and implement multiple interfaces at the same time

**Types of Inheritance:**

1. **Single** — one child, one parent (`Dog extends Animal`)
2. **Multilevel** — chain (`Dog extends Animal`, `Puppy extends Dog`)
3. **Hierarchical** — multiple children, one parent (`Dog extends Animal`, `Cat extends Animal`)
4. **Multiple** — one child, multiple parents (`C extends A, B`) — not supported with classes in Java

**Diamond Problem:**

```
      A
     / \
    B   C
     \ /
      D
```

1. Both B and C inherit from A
2. D inherits from both B and C
3. If A has a method `show()`, and both B and C override it
4. D doesn't know which version to use — B's or C's?
5. This ambiguity is the diamond problem

**Solution in Java:** Use interfaces to achieve multiple inheritance.

**Why interfaces solve this?** Because in interfaces there is no implementation of methods, only definition of method is present in interface. The class which implements the interface provides the implementation. So there is no ambiguity.

**Advantages of Inheritance:**

1. Code reusability — write once in parent, use in all child classes
2. Reduces code duplication — common logic stays in one place
3. Extensibility — easily add new features by extending existing classes
4. Maintainability — fix a bug in parent, all children get the fix
5. Supports polymorphism — parent reference can hold child objects
6. Models real-world relationships naturally (Dog is an Animal)

### 3.4 Polymorphism

- poly = many, morph = forms → polymorphism = many forms
- Same method behaves differently based on context
- **Compile-time:** method overloading (same name, different params)
- **Runtime:** method overriding (child redefines parent's method)

#### Method Overloading

- Same method name, different parameter lists within the same class
- Resolved at compile time (compile-time polymorphism / static binding)
- Can differ by: number of params, type of params, or order of params
- Return type alone is not enough to overload

```java
class Calculator {
    int add(int a, int b) { return a + b; }           // 2 ints
    int add(int a, int b, int c) { return a + b + c; } // 3 ints
    double add(double a, double b) { return a + b; }   // 2 doubles
}
```

**Rules:**

1. Method name must be the same
2. Parameters must be different
3. Return type can be same or different (but can't overload by return type only)
4. Access modifiers can be different
5. Can throw different exceptions

> Why it is called compile time? Because at compile time only, based on how many parameters or type of parameter we pass, the compiler knows which method out of all overloaded methods to call.

#### Method Overriding

- Child class redefines a method from the parent class with the same signature
- Resolved at runtime (runtime polymorphism / dynamic binding)
- Same method name, same parameters, same return type (or covariant)

```java
class Animal {
    void sound() { System.out.println("some sound"); }
}
class Dog extends Animal {
    @Override
    void sound() { System.out.println("bark"); }
}
```

**Rules:**

- Method signature must be exactly the same
- Access modifier in child can be same or less restrictive (not more)
- Cannot override `static`, `final`, or `private` methods
- `@Override` annotation is optional but recommended (catches mistakes at compile time)
- Return type must be same or a subtype (covariant return)
- Constructor cannot be overridden
- Method overriding is only applicable in the context of inheritance, where the subclass inherits from a superclass

> When a child class overrides a parent's method, Java decides which version to run based on the actual object created (`new Dog()`), not the variable type (`Animal obj`) — this decision happens at runtime, which is why it's called runtime polymorphism.

```java
Animal obj = new Dog();  // reference type: Animal, object type: Dog
obj.sound();             // outputs: "bark" (Dog's version, not Animal's)
```

**What's happening:**

- `Animal obj` — the reference variable type is Animal (compile time)
- `new Dog()` — the actual object created is Dog (runtime)
- At compile time, compiler checks: does Animal have `sound()`? Yes, so it compiles
- At runtime, JVM looks at the actual object (Dog), not the reference (Animal)
- So Dog's overridden `sound()` runs

```java
// real use case
Animal[] animals = { new Dog(), new Cat(), new Cow() };
for (Animal a : animals) {
    a.sound(); // each calls its own version — decided at runtime
}
```

> The reference type decides what you can call, the object type decides what actually runs.

---

## 4. Object Relationships

### Is-a Relationship

- Is-a relationship = inheritance
- Car is inheriting from Vehicle class. So car is a vehicle.
- Achieved through inheritance.

### Has-a Relationship

- When an object of one class is used inside another class
- Has-a relationship is a type of association

**Association is of 2 types:**

1. **Aggregation (weak relation):** Both objects can survive individually. Ending one object will not end the other object. Eg: School class has `List<Students>`. If we delete the school object, student objects can still survive.

2. **Composition (strong relation):** Ending of one object will end another object. Eg: School class has Room objects. If the school object is destroyed, room objects will also get destroyed.

> NOTE: We are talking about object lifecycles, not classes. The classes still exist in code — it's the objects whose existence depends on each other.

---

## 5. 3 Main Components of Java

### JVM (Java Virtual Machine)

- It's an abstract machine — does not exist physically
- JVM is platform dependent. For macOS there is a different JVM, for Windows there is a different JVM.

```
Java program → compiler (javac) → bytecode → JVM (JIT compiler) → machine code → CPU → output
```

- When Java code is compiled, we don't get machine code directly. We get bytecode.
- `Student.java` → compile using `javac Student.java` → bytecode: `Student.class`
- JVM takes bytecode as input and uses JIT compiler (Just-In-Time compiler) to convert bytecode to machine code
- The bytecode is platform independent — the same `.class` file can run on any machine that has a JVM installed
- This is why Java is platform independent. Major advantage of Java is portability: **WORA (Write Once Run Anywhere)**

### JRE (Java Runtime Environment)

- JRE = JVM + class libraries (e.g., `Math.sqrt()`)
- Libraries: `java.lang.Math`, `java.util`, etc.
- When bytecode uses inbuilt functions/libraries, JVM links those to the class libraries
- With JRE any bytecode can run, but we cannot write or compile code with JRE alone

### JDK (Java Development Kit)

- JDK = JRE + development tools (compiler, debugger, etc.)
- If you download JDK you get everything — JVM, libraries, compiler, debugger

> JVM, JDK, JRE = platform dependent. But bytecode i.e. `.class` file is platform independent — we can take it to any platform that has a JVM and run it.

---

## 6. Main Method

- Main method is the starting point of a Java program. JVM calls the main method to start the application.

**Signature:** `public static void main(String[] args)`

**Breakdown:**

- `public` — this method can be called from anywhere (JVM needs to access it from outside the class)
- `static` — no need to create an object of the class to call this method. JVM can call it directly using the class name
- `void` — there is no return type
- `String[] args` — accepts command line arguments as an array of strings

**Why static?**

JVM calls `main()` without creating an object. If it weren't static, JVM would need to instantiate the class first.

```java
// Because main is static, JVM does this:
Student.main(args);

// Instead of needing to do:
Student s = new Student();
s.main(args);
```

---

## 7. Comments

```java
// single line comment

/*
multi line
comment
*/
```

---

## 8. Compile and Run

```
To compile: javac Student.java
To run:     java Student
```

---

## 9. Why Only 1 Public Class Per Java File

- You can have multiple classes in the same file, but only one can be public.
- Other classes in the same file can be default (package-private) access only — not private or protected (top-level classes cannot be private or protected).
- The public class name must exactly match the file name, including case sensitivity.

```java
// File: Student.java
public class Student { }   // must match file name
class Helper { }            // default access, allowed
class Utils { }             // default access, allowed
```

**Why this rule exists:**

- **Consistency:** Enforces a one-to-one relationship between the public class and its file, making it easier to locate and organize code.
- **Ease of access:** Developers can identify the purpose of a file without opening it, improving maintainability.
- **Avoids confusion:** Prevents ambiguity when importing or using classes, as each public class is uniquely tied to its file. The compiler knows exactly which file to look in.

---

## 10. Variables

- A variable is a container which holds a value.
- Java is a statically typed language — for every variable we must specify the datatype.
- Java is a strongly typed language — we have restrictions on what values can be assigned to a variable. For each datatype there is a fixed range.

```java
int x = 10;
```

> 1 Byte = 8 bits

### Variable Naming Conventions

- Variable name is case sensitive. `a` and `A` are different.
- Variable name can start with `$`, `_`, or a letter.
  - `int $x;` → valid
  - `int _x;` → valid
  - `int x;` → valid
  - `int *x;` → not valid
- Variable name cannot be Java reserved keywords like `new`, `class`, `while`, `for`, etc.
- Single word → all lowercase. Multiple words → camelCase.
  - `int maximum;` → correct
  - `int maximumPrice;` → correct
  - `int MaximumPrice;` → not recommended
  - `int maximumprice;` → not recommended
- For constants, use all uppercase with underscores:
  - `final int MAX_PRICE = 100;`

### Types of Variables

- Primitive types
- Non-primitive types (reference types)

### Primitive Types (8 total)

| Type | Size | Default Value | Range |
|---------|---------|---------------|-------|
| char | 2 bytes | `'\u0000'` | 0 to 65,535 |
| byte | 1 byte | `0` | -128 to 127 |
| short | 2 bytes | `0` | -32,768 to 32,767 |
| int | 4 bytes | `0` | -2^31 to 2^31-1 |
| long | 8 bytes | `0L` | -2^63 to 2^63-1 |
| float | 4 bytes | `0.0f` | ~7 decimal digits precision |
| double | 8 bytes | `0.0d` | ~15 decimal digits precision |
| boolean | 1 bit (JVM allocates 1 byte) | `false` | true / false |

**char:**

- Stores Unicode characters (ASCII is a subset)
- `char c = 48;` → stores `'0'` (ASCII value of `'0'` is 48)
- `'a'` to `'z'` → 97 to 122
- `'A'` to `'Z'` → 65 to 90
- `'0'` to `'9'` → 48 to 57
- char, byte, short, int, long can all store integer values. char stores the Unicode/ASCII integer representation of a character.

> NOTE: Default values are only valid at class level (instance/static variables). At local level we must assign a value before use.

**long:**

```java
long var = 100L; // add 'L' at the end
```

**float and double:**

```java
float f = 63.20f;   // add 'f' at the end
double d = 63.20d;  // 'd' is optional, double is default for decimals
```

**Floating Point Precision Issue:**

```java
float var1 = .3f;
float var2 = .1f;
float var3 = var1 - var2;
System.out.println(var3); // Output: 0.20000002 (not 0.2)
```

- `float` and `double` introduce rounding errors due to their floating-point representation.
- In industry, `BigDecimal` is used for precise decimal arithmetic (e.g., currency, financial calculations).
- `BigInteger` is used for integers too large for primitive types.

**boolean:**

- Size is 1 bit logically, but JVM allocates 1 byte because modern hardware doesn't allow addressing individual bits efficiently.
- Default value is `false`.

### Type Conversion

**Widening (Automatic Conversion):**

Smaller type → larger type happens automatically.

```java
byte b = 10;
int x = b; // byte (1 byte) automatically converts to int (4 bytes)
```

**Narrowing (Explicit Casting):**

Larger type → smaller type requires manual casting.

```java
int x = 10;
byte b = (byte) x; // must explicitly cast
```

**Promotion During Expression:**

If one operand is of a higher type, the other is automatically promoted.

```java
int a = 10;
double doubleVar = 5.5;
double sum = a + doubleVar; // 'a' is promoted to double
// or if we want int:
int sum2 = (int)(a + doubleVar); // must typecast
```

**Expression Promotion with byte:**

```java
byte a = 127;
byte b = 1;
// byte sum = a + b; // ERROR: a + b is promoted to int, not short
int sum = a + b; // works
// or typecast:
byte sum2 = (byte)(a + b);
```

- If the value overflows the range, it wraps around (overflow cycle).
- byte max is 127. If value becomes 128, it wraps to -128.

### Kinds of Variables

- Local variable
- Instance / member variable
- Static / class variable


**Instance Variable (Member Variable):**

- Declared at class level (outside methods)
- Default values are assigned automatically (e.g., `int x;` defaults to `0`)
- Each object gets its own copy of instance variables
- Different objects can have different values for their instance variables

```java
class Student {
    int age; // instance variable, default = 0
}
Student s1 = new Student();
Student s2 = new Student();
s1.age = 20;
s2.age = 25; // each object has its own copy
```

**Local Variable:**

- Declared inside a method, block, or constructor
- Scope is limited to that method/block only
- No default value — must be initialized before use

```java
void display() {
    int count = 5; // local variable
}
```

**Static Variable (Class Variable):**

- Declared at class level with the `static` keyword
- Only one copy exists, shared across all objects
- Should be accessed using the class name
- Can also be accessed via objects, but accessing through class name is recommended

```java
class Student {
    static String school = "ABC School"; // one copy for all
}
// access via class name (recommended)
System.out.println(Student.school);
// access via object (works but not recommended)
Student s1 = new Student();
System.out.println(s1.school); // compiles and works, but bad practice
```

### Non-Primitive (Reference) Data Types

4 types:

1. Class
2. String
3. Interface
4. Array

#### 1. Class

- When you create a new object using the `new` keyword, memory is allocated in heap memory.
- `Employee emp = new Employee();` — the variable `emp` holds a reference (address) to the object in heap memory.
- In Java there is no concept of pointers like in C and C++.

**Pass by Value in Java:**





- In Java, everything is passed by value.
- That means a copy of the variable is passed to the method, not the original variable.
- If you change the parameter inside the method, the original variable outside the method does not change.

---

### Example 1: Primitive Type

```java
class Test {
    static void changeValue(int x) {
        x = 50;
    }

    public static void main(String[] args) {
        int a = 10;
        changeValue(a);
        System.out.println(a);
    }
}
```

**Output:** `10`

**Explanation:**

1. `a = 10`
2. Method call `changeValue(a)`
3. Copy of `a` is passed → `x = 10`
4. `x` becomes `50`
5. Original `a` remains `10`

---

### Example 2: Object in Java

Even objects are passed by value, but the value passed is the reference.

```java
class Student {
    int age;
}

class Test {
    static void changeAge(Student s) {
        s.age = 30;
    }

    public static void main(String[] args) {
        Student s1 = new Student();
        s1.age = 20;
        changeAge(s1);
        System.out.println(s1.age);
    }
}
```

**Output:** `30`

**Why did it change?**

Because:

- A copy of the reference is passed
- Both references point to same object in heap
- Changing object property affects original object

**But if we reassign the object:**

```java
static void changeAge(Student s) {
    s = new Student();
    s.age = 40;
}
```

**Output:** `20`

Because the reference copy now points to a new object.

---

### Comparison with C++

#### 1. Pass by Value (C++)

Works similar to Java primitives.

```cpp
#include <iostream>
using namespace std;

void changeValue(int x) {
    x = 50;
}

int main() {
    int a = 10;
    changeValue(a);
    cout << a;
}
```

**Output:** `10`

#### 2. Pass by Reference (C++)

C++ supports true pass by reference using `&`.

```cpp
#include <iostream>
using namespace std;

void changeValue(int &x) {
    x = 50;
}

int main() {
    int a = 10;
    changeValue(a);
    cout << a;
}
```

**Output:** `50`

Here original variable changes.

---

### Key Difference

| Feature | Java | C++ |
|---------|------|-----|
| Pass by value | Yes | Yes |
| Pass by reference | ❌ Not supported | ✅ Supported |
| Objects | Reference copy passed | Can pass object by reference |
| Direct modification of variable | ❌ Not possible | ✅ Possible |

---

### Interview One-Line Answer

Java is strictly pass-by-value, meaning a copy of the variable is passed. For objects, the value passed is the reference to the object, while C++ supports both pass-by-value and pass-by-reference using reference variables (`&`).


- When you create two objects of the same class, each object is stored in a separate location in heap memory with its own unique address.

#### 2. String

- String is a reference data type because it is a class (`java.lang.String`), and string variables hold a reference to an object in heap memory.

**String Constant Pool:**

- Inside heap memory there is a special area called the String Constant Pool.
- String literals are stored in this pool.

```java
String s1 = "hello"; // "hello" is a string literal, stored in string constant pool
String s2 = "hello"; // checks pool first, "hello" already exists, so s2 points to same location as s1
String s3 = new String("hello"); // new keyword forces a new object in heap memory (outside the pool)
```

**Comparing Strings:**

```java
s1 == s2      // true — both point to the same location in string constant pool
s1 == s3      // false — s1 points to pool, s3 points to a different heap location
s1.equals(s3) // true — .equals() compares the actual value, not the reference
```

- `==` compares references (memory addresses).
- `.equals()` compares the actual content/value.

**Why Strings are Immutable:**

- Because multiple references (like `s1` and `s2`) can point to the same string in the pool. If strings were mutable, changing the value through `s1` would unexpectedly change it for `s2` as well. Immutability ensures safety when sharing string objects.
- Once a string is created, its value cannot be changed.
- If we update a string variable, a new string object is created — the original is not modified.

```java
String s1 = "hello";
s1 = "helloWorld"; // "hello" still exists in pool, s1 now points to new "helloWorld"
```

- The old `"hello"` will be removed by the garbage collector only if no other reference is pointing to it.

**Reasons for Immutability:**

1. **Security** — sensitive data (passwords, file paths) cannot be altered once created.
2. **String Pool / Caching** — since strings can't change, the same literal can be safely shared among multiple references, improving memory efficiency.
3. **Thread Safety** — immutable objects are inherently thread-safe. Multiple threads can access the same string without synchronization.
4. **HashCode Caching** — strings cache their hash code on first calculation. Since the value never changes, the cached hash remains valid, improving performance in hash-based collections like `HashMap`.

**How Immutability is Achieved in the String Class:**

1. **Private final field** — `private final char[] value` ensures the internal array cannot be reassigned.
2. **No setter methods** — no public method exists to modify the content.
3. **Methods return new strings** — `concat()`, `replace()`, `substring()`, etc. return new string objects instead of modifying the original.
4. **Class is final** — `String` class is declared `final`, so it cannot be subclassed to alter its behavior.

> Why String is a Reference Data Type: Because string variables store a reference (address) to an object in heap memory, not the actual value itself.

#### 3. Interface

- An interface is a contract. You cannot instantiate an interface directly.

```java
// Person is an interface
Person p = new Person(); // ERROR — cannot create object of an interface
```

- Any class that implements an interface must provide implementations for its methods.
- However, you can use an interface type as a reference to hold objects of implementing classes:

```java
interface Person { }
class Teacher implements Person { }
class Engineer implements Person { }

Person p1 = new Teacher();   // valid — holding reference to Teacher object
Person p2 = new Engineer();  // valid — holding reference to Engineer object
```

- This works because `Person` is the parent type, and it can hold a reference to any of its child (implementing) objects. This is polymorphism.

#### 4. Array

- A sequence of contiguous memory that stores elements of the same data type.
- Array objects are stored in heap memory.

**Creating Arrays:**

```java
// using new keyword
int[] arr = new int[5];
// without new keyword (array literal)
int[] arr = {10, 20, 30, 40, 50};
```

- Both `int[] arr` and `int arr[]` are valid syntax. `int[] arr` is the preferred style in Java.
- The variable `arr` holds a reference (address) to the array object in heap memory.
- `arr` points to the starting address of the array (0th index).

> Why Array is a Reference Data Type: Because the `arr` variable does not hold the actual data — it holds a reference to the array object stored in heap memory.

---

## 11. Wrapper Classes

For each of the 8 primitive data types, Java provides a corresponding reference type (wrapper class).

| Primitive | Wrapper |
|-----------|---------|
| char | Character |
| byte | Byte |
| short | Short |
| int | Integer |
| long | Long |
| float | Float |
| double | Double |
| boolean | Boolean |

**Why Do We Need Wrapper Classes?**

1. **Object-Oriented Features** — allows primitives to be treated as objects, enabling use of methods, collections, and generics.
2. **Nullability** — wrapper types can be `null`, useful when you need to represent the absence of a value. Primitives cannot be null.
3. **Collections** — Java collections like `ArrayList` work only with objects, not primitives. Wrapper classes are needed to store primitive values in collections.
4. **Utility Methods** — wrapper classes provide useful methods like `Integer.parseInt()`, `Double.parseDouble()`, etc.

**Autoboxing:**

Automatic conversion from primitive to its wrapper class.

```java
int a = 10;
Integer a1 = a; // autoboxing: int → Integer
```

**Auto-unboxing:**

Automatic conversion from wrapper class to its primitive.

```java
Integer x = 10;
int x1 = x; // auto-unboxing: Integer → int
```

---

## 12. Constant Variables

- A `static` variable is shared across all objects, but it is not constant — any object can change its value.

```java
static int empId = 10;
// object1 can change it to 20
// object2 can change it to 30
```

- To make it constant, add the `final` keyword:

```java
static final int EMP_ID = 10; // constant, cannot be changed
```

- No object can modify a `static final` variable after initialization.
- Naming convention for constants: `UPPER_SNAKE_CASE`.

---

## 13. Methods

**Advantages of Methods:**

- Code readability — breaks logic into meaningful, named blocks.
- Code reusability — write once, call multiple times.

**Return Type:**

- For `void`: either don't return anything, or use `return;` to exit early.

**Method Naming:**

- Name should be a verb (represents an action), e.g., `calculateSum`, `getName`.
- Start with lowercase, follow camelCase for multiple words.

### Types of Methods

1. **System defined method** — present in JRE libraries (JRE = JVM + libraries). E.g., `Math.sqrt(25);`
2. **User defined method** — methods created by the programmer.
3. **Overloaded method** — more than one method with the same name in the same class, but different parameters.
4. **Overridden method** — subclass redefines a method from the parent class with the same signature.
5. **Static method** — associated with the class, not with objects.
6. **Final method** — cannot be overridden by child classes.
7. **Abstract method** — defined only in abstract classes, no body.

### Static Method

- Associated with the class, not with objects.
- No need to create an object to call it — call using class name.
- Common for all objects since it belongs to the class.

```java
ClassName.staticMethod(); // called via class name
```

**Restrictions of static methods:**

- Cannot access non-static instance variables.
- Cannot access non-static methods.

```java
class Stock {
    int stockPrice = 100; // instance variable
    static void display() {
        // System.out.println(stockPrice); // ERROR — cannot access instance variable
    }
}
```

- Reason: instance variables and non-static methods are associated with objects. A static method doesn't know which object's variable/method to use.
- If we specify the object explicitly, then it works:

```java
static void display() {
    Stock s = new Stock();
    System.out.println(s.stockPrice); // works — specific object provided
}
```

**Why static methods cannot be overridden:**

- Method overriding is a runtime concept — at runtime, JVM decides which method to call based on the actual object type.
- Static methods are associated with the class, not objects, so there is no runtime decision. They are resolved at compile time (method hiding, not overriding).

**When to make a method static:**

- When the method does not depend on instance variables or instance methods (e.g., utility/helper methods).

### Final Method

- When you don't want a child class to change the method implementation, declare it `final`.

```java
final void display() {
    System.out.println("cannot be overridden");
}
```

### Abstract Method

- Defined only in abstract classes.
- Only method declaration — no body.
- Implementation is provided by the child class.

```java
abstract class Animal {
    abstract void sound(); // no body
}
class Dog extends Animal {
    void sound() {
        System.out.println("bark");
    }
}
```

### Variable Arguments (Varargs)

- Used when you don't know how many arguments will be passed.
- Replaces multiple overloaded methods with a single method.

```java
int sum(int... numbers) {
    int total = 0;
    for (int n : numbers) {
        total += n;
    }
    return total;
}
sum(1, 2);        // works
sum(1, 2, 3);     // works
sum(1, 2, 3, 4);  // works
```

**Varargs rules:**

- Only one varargs parameter is allowed per method.
- It must be the last parameter in the list.

```java
void method(String name, int... numbers) { } // valid — varargs is last
// void method(int... numbers, String name) { } // ERROR — varargs must be last
```

- Reason: if varargs is first, the compiler can't determine where the varargs values end and the next parameter begins.

---

## 14. Constructor

- A constructor is used to create an instance of a class and initialize its instance variables.
- Constructor does not have an explicit return type.
- Constructor cannot be `static`, `final`, `abstract`, or `synchronized`.

**Is the constructor or the `new` keyword responsible for creating an instance?**

The `new` keyword allocates memory and tells Java to call the constructor. The constructor then initializes the object.

**Why does a constructor have the same name as the class?**

So the compiler can easily identify it as a constructor and not a regular method.

**Why does a constructor not have a return type?**

Implicitly, Java treats the return type as the class itself.

> Note: you can have a method with the same name as the class, but it will have a return type. A method with no return type and the class name is the constructor.

**Why can a constructor not be `final`?**

`final` prevents overriding. But constructors are never inherited by subclasses, so they can't be overridden in the first place. Making a constructor final is meaningless — it is already non-overridable by design.

**Why are constructors never inherited?**

If a constructor were inherited by a subclass, it would violate the rule that a constructor must have the same name as its own class.

**Why can a constructor not be `abstract`?**

`abstract` means no implementation — the child class must provide it. Since constructors are not inherited, no child class can provide the implementation.

**Why can a constructor not be `static`?**

A static constructor could only access static variables, so it could not initialize instance variables (which is the main job of a constructor). A static context cannot use the `super` keyword, which is needed for constructor chaining.

**Do interfaces have constructors?**

No. Interfaces only have method declarations (no implementation). Also, the job of a constructor is to create an object, and you cannot create an object of an interface.

### Types of Constructors

1. Default constructor
2. No-arg constructor
3. Parameterized constructor
4. Constructor overloading
5. Private constructor
6. Constructor chaining (`this` and `super`)

#### Default Constructor

- If you don't define any constructor, Java internally provides a default constructor.
- It sets default values for all instance variables.

> Note: local variables (inside methods) do not get default values — you must assign a value before use.

```java
class Student {
    int age;    // default value = 0
    String name; // default value = null
    // Java provides a default constructor here
}
```

#### No-arg Constructor

- Same as default constructor, but manually defined by the programmer.

```java
class Student {
    Student() {
        System.out.println("No-arg constructor called");
    }
}
```

#### Parameterized Constructor

- Accepts parameters to initialize instance variables with specific values.

> Note: when you manually define any constructor, Java will not provide the default constructor.

```java
class Student {
    int age;
    Student(int age) {
        this.age = age;
    }
}
```

#### Constructor Overloading

- Same as method overloading — multiple constructors in the same class with different parameters.
- The correct constructor is called based on the number or type of arguments.

```java
class Student {
    Student() { }
    Student(int age) { }
    Student(String name, int age) { }
}
```

#### Private Constructor

- When a constructor is marked `private`, no outside class can create an object of this class.
- Only the class itself can call its own constructor.
- Useful in patterns like Singleton, where object creation is controlled.

```java
class Database {
    private Database() { }
    public static Database getInstance() {
        return new Database(); // only this class can call the constructor
    }
}
// Outside:
// Database db = new Database(); // ERROR — constructor is private
Database db = Database.getInstance(); // works
```

#### Constructor Chaining

Two types:

- `this()` — chaining within the same class
- `super()` — chaining between parent and child class

**`this()` — calls another constructor in the same class:**

```java
class Student {
    Student() {
        this("Unknown"); // calls parameterized constructor
        System.out.println("No-arg constructor");
    }
    Student(String name) {
        System.out.println("Name: " + name);
    }
}
```

**`super()` — calls the parent class constructor:**

```java
class Person {
    Person() {
        System.out.println("Person constructor");
    }
}
class Manager extends Person {
    Manager() {
        // super(); ← hidden here, added by Java automatically
        System.out.println("Manager constructor");
    }
}
// Output:
// Person constructor
// Manager constructor
```

- The parent constructor is always invoked first, then the child constructor.
- Java automatically adds `super()` as the first line of every constructor if you don't provide it.
- `super()` or `this()` must be the first statement in the constructor.

**When parent has a parameterized constructor:**

- If the parent has only a parameterized constructor (no no-arg), the implicit `super()` will fail.
- You must explicitly call `super(args)` with the required arguments.

```java
class Person {
    String name;
    Person(String name) {
        this.name = name;
    }
}
class Manager extends Person {
    Manager(String name, int id) {
        super(name); // must explicitly pass required arguments
        System.out.println("Manager created");
    }
}
// From main:
Manager m = new Manager("John", 101); // must provide all arguments
```

---

## 15. Types of Classes

### Concrete Class

- A regular class that can be instantiated.
- All methods have implementations.

```java
class Car { void drive() { } }
```

### Abstract Class

- Cannot be instantiated.
- Can have both abstract (no body) and concrete methods.

```java
abstract class Animal { abstract void sound(); }
```

### Super Class and Sub Class

- Super class (parent) — the class being inherited from.
- Sub class (child) — the class that inherits.

```java
class Animal { }           // super class
class Dog extends Animal { } // sub class
```

### Object Class

- The root class of all classes in Java (`java.lang.Object`).
- Every class implicitly extends `Object`.
- Provides methods like `toString()`, `equals()`, `hashCode()`, `clone()`.

### Nested Classes

#### a. Inner Class (Non-static Nested Class)

- Defined inside another class without `static`.
- Has access to all members (including private) of the outer class.

**Member Inner Class** — declared at class level inside the outer class.

```java
class Outer {
    class Inner { }
}
```

**Local Inner Class** — declared inside a method.

```java
class Outer {
    void display() {
        class Local { }
    }
}
```

**Anonymous Inner Class** — no name, declared and instantiated in one step. Often used for quick interface/abstract class implementations.

```java
Runnable r = new Runnable() {
    public void run() { System.out.println("running"); }
};
```

#### b. Static Nested Class

- Declared with `static` inside another class.
- Cannot access non-static members of the outer class.

```java
class Outer {
    static class Nested { }
}
```

### Generic Class

- Works with different data types using type parameters.

```java
class Box<T> {
    T value;
}
Box<String> b = new Box<>();
```

### POJO Class (Plain Old Java Object)

- Simple class with private fields, getters, setters, and no special framework dependency.

```java
class Employee {
    private String name;
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

### Enum Class

- Represents a fixed set of constants.
- Cannot be instantiated with `new`.

```java
enum Day { MONDAY, TUESDAY, WEDNESDAY }
```

### Final Class

- Cannot be extended (no subclass allowed).
- Example: `String` class is final.

```java
final class Utility { }
// class SubUtility extends Utility { } // ERROR
```

### Singleton Class

- Only one instance can exist.
- Private constructor + static method to get the instance.

```java
class Database {
    private static Database instance = new Database();
    private Database() { }
    public static Database getInstance() { return instance; }
}
```

### Immutable Class

- Once created, state cannot be changed.
- All fields are `private final`, no setters, class is `final`.

```java
final class Money {
    private final int amount;
    Money(int amount) { this.amount = amount; }
    public int getAmount() { return amount; }
}
```

### Wrapper Class

- Wraps primitive types into objects.
- Enables primitives to be used in collections, generics, etc.

```java
int a = 10;
Integer obj = a; // autoboxing
```
