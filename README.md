# Core Java (String)



| No   | Content                                                      |
| ---- | ------------------------------------------------------------ |
| 1    | [What is a String?](#1)                                      |
| 2    | [Why String is immutable?](#2)                               |
| 3    | [Java String concat() Method](#3)                            |
| 4    | [Substring, length, indexOf and toCharArray in java](#4)     |
| 5    | [Why sensitive data not recommended to store in String?](#5) |
| 6    | [`equals` vs `==` in java](#6)                               |
| 7    | [How String class works?](#7)                                |
| 8    | [Difference between StringBuilder and StringBuffer?](#8)     |
| 9    | [what is `String.intern`? How does `String.intern()` work?](#9) |
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



<h3 id="5">5. Why sensitive data not recommended to store in String?</h3>

Sensitive data is **not recommended to be stored in a `String`** for several important **security-related reasons**. 

###### Immutability of Strings:

- **Strings are immutable**, meaning once created, their contents cannot be changed.
- So if you store a password in a `String`, that value will **remain in memory until garbage collected**.
- Even after you're "done" using it, you **cannot overwrite the contents** (e.g., with zeros or random data).

###### Garbage Collection Delay:

- You can’t control when the **Garbage Collector (GC)** will remove the string from memory.
- Until GC decides to clean it up, the sensitive string can remain in memory — potentially **exposed to memory dumps or heap analysis tools**.

###### Memory Dump Vulnerability:

- If an attacker gets access to a **heap dump**, they can easily **search for sensitive strings**, since:
  - Strings are stored as **plain text in memory**.
  - Tools like `jmap`, `jconsole`, or even malware can extract them.

###### Interning and Caching:

- Some runtimes (like Java) **intern strings**, meaning common strings may be stored in a global pool.
- This increases the chance that the sensitive string **remains in memory indefinitely**, especially if reused.



> [!IMPORTANT]
>
> Use **character arrays (`char[]`) or byte arrays (`byte[]`)** for sensitive data:
>
> - You can **manually overwrite** the contents when done (e.g., set all elements to `'\0'` or `0`).
> - Offers **greater control over memory** and **less risk of data leaks**.

```java
// Bad: Using String for password
String password = "mySecretPassword";

// Better: Use char array
char[] password = {'m','y','S','e','c','r','e','t'};
Arrays.fill(password, '\0'); // erase when done
```



<h3 id="6">6. equals vs == in java?</h3>

`==` Operator compares **memory addresses** (object references). 

While `==` Compares **object content/logical equality**.

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);       // false (different memory references)
System.out.println(a.equals(b));  // true (same content)
```

###### When to Use What

- Use `==` when:
  - You want to check if two references point to **the same object**.
  - You're comparing **primitive values**.
- Use `.equals()` when:
  - You want to compare **contents/logical equality**.
  - You're comparing **objects like String, Integer, List**, etc.



<h3 id="7">7. How String class works?</h3>

Key Characteristics of `String` Class provided [here](1)

###### What is happening behind the scene:

```java
public class StringExample {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = new String("Hello");

        System.out.println(s1 == s2); // true
        System.out.println(s1 == s3); // false
        System.out.println(s1.equals(s3)); // true
    }
}
```

What’s Happening Here?

1. **String s1 = "Hello";**
   - `"Hello"` is a string literal.
   - It goes into the **String Constant Pool**.
   - `s1` points to that literal.
2. **String s2 = "Hello";**
   - `"Hello"` already exists in the pool.
   - So `s2` also points to the **same object** as `s1`.
3. **String s3 = new String("Hello");**
   - The `new` keyword **forces the creation** of a new object in the **heap**, even if `"Hello"` is already in the pool.
   - So `s3` is a **different object**, though it contains the same value.



<h3 id="8">8. Difference between StringBuilder and StringBuffer?</h3>

StringBuilder and StringBuffer used for String based operations. StringBuffer is synchronous meanwhile StringBuilder is asynchronous.

`StringBuilder`:

- Designed for **single-threaded** environments.
- **Not synchronized**, so it doesn’t have the overhead of thread safety.
- Faster than `StringBuffer` in most cases.

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" World");
System.out.println(sb.toString());  // Output: Hello World
```

`StringBuffer`:

- Designed for **multi-threaded** environments.
- All methods are **synchronized**, making it **thread-safe**.
- Slower than `StringBuilder` due to synchronization overhead.

```java
StringBuffer sbf = new StringBuffer();
sbf.append("Hello");
sbf.append(" World");
System.out.println(sbf.toString());  // Output: Hello World
```

Both `StringBuilder` and `StringBuffer` use an **internal `char[]` array** (or `byte[]` in newer versions) to store character sequences. The main difference is in how they **synchronize** access:

```java
public synchronized StringBuffer append(String str) { ... }

public StringBuilder append(String str) { ... }
```



<h3 id="9">9. What is String.intern()? How does String.intern() work?</h3>

`String.intern()` is a **special method** in Java used to manage memory more efficiently when working with **duplicate String values**. It plays a key role in **String pooling**, which is a memory optimization feature in Java.

> [!IMPORTANT]
>
> If the pool already contains a string equal to this `String` object (based on `.equals()`), then the string from the **pool** is returned. Otherwise, this `String` object is **added to the pool** and returned.



##### String Pool (a.k.a. Intern Pool):

- It's a **special memory area inside the heap**.
- It stores **unique string literals** (like `"hello"`, `"java"`).
- It avoids creating multiple objects with the same value.

```java
public class InternExample {
    public static void main(String[] args) {
      // s1 is created using new, so it’s a heap object, not in the string pool.
        String s1 = new String("hello"); 
      // s2 is a string literal, so it goes to the string pool.
        String s2 = "hello";

        System.out.println(s1 == s2);              // false
      // s1.intern() looks for "hello" in the pool, finds it, and returns the same reference as s2.
        System.out.println(s1.intern() == s2);     // true
    }
}
```



> **Things to remember:**
>
> Memory leaks: *Excessive interning of unique strings can fill up the pool and cause memory issues.*
>
> Performance: *`intern()` involves a lookup in the pool — avoid using it excessively in performance-critical code.*
>
> Java Version: *From Java 7+, the string pool is moved to the **heap**, not the PermGen (which was limited).*
