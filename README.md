# Core Java (String)



| No   | Content                                                      |
| ---- | ------------------------------------------------------------ |
| 1    | [What is a String?](#1)                                      |
| 2    | [Why String is immutable?](2)                                |
| 3    | [Java String concat() Method](3)                             |
| 4    | [Substring, length, indexOf and toCharArray in java](4)      |
| 5    | Why sensitive data not recommended to store in String?       |
| 6    | Equals vs == in java                                         |
| 7    | How String class works?                                      |
| 8    | Difference between StringBuilder and StringBuffer?           |
| 9    | How does `String.intern()` work?                             |
| 10   | What are the performance implications of using `+` to concatenate Strings in loops? |
| 11   | How does substring() work internally?                        |
| 12   | How is String hashing implemented? Why is it cached?         |
| 13   | Can a String be modified using reflection?                   |
| 14   | How does Java optimize string concatenation at compile time? |
| 15   | Explain String deduplication in Java 8+?                     |
| 16   | What is the difference between `isEmpty()` and `isBlank()` in Java? |
| 17   | How to extract substring between two delimiters?             |
| 18   | Remove duplicate characters from a string                    |
|      |                                                              |



<h3 id="1">1. What is a String</h3>

The `String` class represents character strings. How String can be represented shown below:

```java
String sample = "sample"
//is equivalent to: 
char sample[] = {'s', 'a', 'm', 'p', 'l', 'e'}
```



> **String literals**: are stored in the **String Constant Pool** (within the Heap) and are shared for identical content. 
>
> Strings created with `new`: are stored in the **main Heap memory** as distinct objects, even if their content is identical to existing strings in the SCP. 
>
> All `String` objects, regardless of creation method, **ultimately store their character data in an internal `char` array**.



<h3 id="2">2. Why String is immutable?</h3>

The `String` class in Java is **immutable**, meaning once a `String` object is created, **its value cannot be changed**.

###### What's happening under the hood:

- The `String` class is declared as `final`, which means **it cannot be subclassed**.
- This prevents others from overriding its behavior and possibly introducing mutability.

```java
public final class String {...}
```

- Internally, Java `String` stores characters in a **character array** (`char[] value`).
- Array is declared as `private` and `final`.

```java
private final char value[];
```



> The `String` class **does not provide any method** that can **change the contents** of the character array.
>
> All modifying methods like `toUpperCase()`, `concat()`, or `substring()` return a **new String object**, leaving the original unchanged.
>
> Because `String` is immutable, it is **automatically thread-safe** — multiple threads can access the same string without synchronization issues.



###### String Interning Mechanism:

- Java reuses strings from the **intern pool** to save memory.
- This is only safe if strings are **immutable**, otherwise multiple variables would point to the same mutable object.

```java
String s1 = "hello";
String s2 = "hello";
System.out.println(s1 == s2); // true (same object from string pool)
```



<h3 id="3">3. Java String concat() Method?</h3>

The `concat()` method in Java is used to **join two strings**. It's part of the `String` class and creates a **new String** that is the combination of the original and the provided string.

```java
String s1 = "Hello";
String s2 = " World";

String result = s1.concat(s2);
System.out.println("Original: " + s1);  // Hello
System.out.println("Concatenated: " + result); // Hello World
```



###### Equivalent Using + Operator

```java
String s3 = s1 + s2;
```

> This does the same thing as `s1.concat(s2)` — under the hood, the `+` operator is converted to `StringBuilder.append()` for performance.



<h3 id="4">4. Substring, length, indexOf and toCharArray in java?</h3>

These all are String methods. Each explained below:

`substring()`:

```java
/* String substring(int beginIndex, int endIndex) */
String str = "Hello World";
System.out.println(str.substring(6));       // Output: "World"
System.out.println(str.substring(0, 5));    // Output: "Hello"
```

`length()`:

```java
/* int length() */
String str = "Java";
System.out.println(str.length()); // Output: 4
```

`indexOf()`:

```java
String str = "Programming";
System.out.println(str.indexOf('g'));      // Output: 3 (first 'g')
System.out.println(str.indexOf("gram"));   // Output: 3
System.out.println(str.indexOf("z"));      // Output: -1 (not found)
```

> It is Case-sensitive.

`toCharArray()`:

```java
/* char[] toCharArray() */
String str = "ABC";
char[] chars = str.toCharArray();

for (char c : chars) {
    System.out.println(c);
}
// Output:
// A
// B
// C
```

