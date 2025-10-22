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
| 10   | [What are the performance implications of using `+` to concatenate Strings in loops?](#10) |
| 11   | [How does substring() work internally?](#11)                 |
| 12   | [How is String hashing implemented? Why is it cached?](#12)  |
| 13   | [Can a String be modified using reflection?](#13)            |
| 14   | [How does Java optimize string concatenation at compile time?](#14) |
| 15   | [Explain String deduplication in Java 8+?](#15)              |
| 16   | [What is the difference between `isEmpty()` and `isBlank()` in Java?](#16) |
| 17   | [How to extract substring between two delimiters?](#17)      |
| 18   | [Remove duplicate characters from a string](#18)             |
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



<h3 id="10">10. What are the performance implications of using + to concatenate Strings in loops?</h3>

In Java, **`String` is immutable**, meaning once a `String` object is created, it cannot be changed. When you use `+` to concatenate strings, you're creating **new `String` objects** every time.

Example:

```java
String result = "";
for (int i = 0; i < 1000; i++) {
    result += "a";
}
```

###### **Performance Impact**:

- **Time complexity:** Becomes **O(n²)** due to repeated copying of the growing string.
- **Memory usage:** Lots of temporary `String` objects are created and discarded, increasing garbage collection pressure.
- **CPU cycles:** Wasted on repeated allocations and character copying.

> The Solution: Use `StringBuilder` or `StringBuffer`



<h3 id="11">11. How does substring() work internally?</h3>

In Java, `substring(beginIndex, endIndex)` returns a new string that is a **subsequence** of the original string. The parameters are `beginIndex`: **the starting index** (inclusive), `endIndex`: **the ending index** (exclusive).

```java
String str = "Hello, world!";
String sub = str.substring(7, 12); // returns "world"
```

###### Internal Mechanism in Java:

***Before Java 7 Update 6***: Java `String` is **backed by a `char[]` array**. Originally, the `substring()` method **did not create a new array**. Instead, it created a **new String object that shared the same char array**, but with a different **offset** and **count**.

```java
// str → points to char[] {'H','e','l','l','o',',',' ','w','o','r','l','d','!'}
String str = "Hello, world!";

// sub → shares the same char[] but has:
// offset = 7
// count = 5 (because 12 - 7 = 5)
String sub = str.substring(7, 12);
```

> ###### Benefits:
>
> - Fast and memory-efficient (no array copy)
>
> ###### Problems:
>
> - **Memory leak**: If you extract a small substring from a very large string, the entire original character array remains in memory because of shared reference.
> - `str` can be garbage collected, but its huge `char[]` remains because `sub` references it.



***In Java (Java 7 Update 6 and Later)***: Due to the memory leak issue, Java changed this behavior, `substring()` now creates a **new char array** and **copies** the relevant characters.

```java
public String substring(int beginIndex, int endIndex) {
    // Validation omitted
    int subLen = endIndex - beginIndex;
    char[] subChars = new char[subLen];
    System.arraycopy(value, beginIndex + offset, subChars, 0, subLen);
    return new String(subChars);
}
```

> ###### Benefits:
>
> - Avoids memory leaks
> - Substrings are independent of the original string
>
> ###### Downsides:
>
> - **Less efficient** (extra memory and time to copy)
> - Cannot "reuse" memory across substrings



***From Java 9 onward***: Java uses **compact strings**:

- `byte[]` instead of `char[]`
- Single-byte encoding (ISO-8859-1) if possible
- UTF-16 if needed
- This is part of **JEP 254: Compact Strings**



<h3 id="12">12. How is String hashing implemented? Why is it cached?</h3>

Hashing is the process of converting a **string of characters** into a **fixed-size integer** value called a **hash code**. This **hash code** is used to determine the **bucket/index** in hash-based data structures.

```java
String str = "hello";
int hash = str.hashCode(); // returns 99162322
```

***Why Hashing Matters***: In structures like `HashMap`, hash codes help in **quick lookup, insertion, and deletion**. Instead of comparing every string (which is `O(n)`), you hash it to an integer and only check for **collisions**. 

> *So hashing makes these operations near `O(1)` time.*

***Problem Without Caching:*** Strings are **immutable** — once created, they don’t change. But if you repeatedly call `hashCode()`, and it's recalculated every time, that would be **wasteful**, especially for long strings. If `s` is used as a key in a `HashMap`, and you perform thousands of lookups — this would mean repeating the same calculation over and over.

***Solution - Cache the Hash Code***: Java internally **caches** the hash code **after computing it once**. The cached value is reused in future calls.

> [!IMPORTANT]
>
> Only compute `hashCode()` once per string. Future calls return the cached value.
>
> Java (from Java 7+) uses **hash randomization** in **`HashMap` for non-String keys**, but not for Strings. String hash codes remain consistent across JVM runs.

```java
// Internally
String s = "abc";
s.hashCode(); // triggers computation
s.hashCode(); // uses cached value
```



<h3 id="13">13. Can a String be modified using reflection?</h3>

Yes, It is possible but not recommended.

***Bypassing with Reflection (Pre-Java 9)***: 

```java
import java.lang.reflect.Field;

public class HackString {
    public static void main(String[] args) throws Exception {
        String str = "Hello";
        System.out.println("Before: " + str);

        Field valueField = String.class.getDeclaredField("value");
        valueField.setAccessible(true);
        char[] value = (char[]) valueField.get(str); // Java 8, Previous
      	// byte[] val = (byte[]) value.get(str); // Java 9+
        value[0] = 'J';

        System.out.println("After: " + str); // Output: "Jello"
    }
}
```

Here, The `value` field of `String` was accessed using reflection. The backing `char[]` was **mutated**, even though the `String` is marked as **final** and **immutable**. Since `String` is used everywhere, this can cause **horrific, unpredictable behavior** system-wide.

> ***Imacts of this***: Breaks the **contract of immutability**, a fundamental assumption in Java. Can **corrupt** `HashMap` keys or other data structures that rely on the string not changing. The cached `hashCode` is **not updated**, so your string can have a new value with the **old hash**, leading to logic bugs and hard-to-trace errors. You can even affect other string literals (because of **string interning**).



<h3 id="14">14. How does Java optimize string concatenation at compile time?</h3>

Java applies **powerful compile-time and runtime optimizations** for **string concatenation**, aiming to make your code efficient **without you having to manage performance manually**.

1. ***Compile-Time Optimization: Constant Folding:***

If the Java compiler (`javac`) detects that you're concatenating **string literals** or **constants**, it will **evaluate them at compile time** and embed the result directly in the `.class` file.

```java
public class Test {
    public static void main(String[] args) {
        String a = "Hello, " + "world!";
        System.out.println(a);
    }
}
```

`javac` sees that both `"Hello, "` and `"world!"` are **compile-time constants**

It performs **constant folding**, replacing the code with:

```java
String a = "Hello, world!";
```

> No concatenation occurs at runtime
>
> This is **fully optimized** and **fast**



2. ***What Happens at Runtime (When Compile-Time Is Not Possible):***

When strings involve **variables**, Java **cannot concatenate at compile time**, so it uses **StringBuilder** under the hood to optimize performance.

```java
String name = "Alice";
String greeting = "Hello, " + name + "!";
// This will create lots of string objects unnecessarly


// hence Java, converts this to
String greeting = new StringBuilder()
                     .append("Hello, ")
                     .append(name)
                     .append("!")
                     .toString();
```

So the **concatenation is compiled into efficient bytecode** using `StringBuilder`.



<h3 id="15">15. Explain String deduplication in Java 8+?</h3>

**String deduplication** is a clever memory optimization introduced in **Java 8 (update 20 and later)** to reduce the memory footprint of `String` objects on the heap.

> [!IMPORTANT]
>
> **String deduplication** is a feature in the **G1 garbage collector** (G1GC) introduced in **Java 8**.
>  It automatically **identifies multiple distinct `String` objects that have the same content** and **reuses a single character backing array (`char[]`)** among them.

Without deduplication, Each `String` has its own `char[]`, even if their contents are identical. With deduplication, Multiple `String` objects can **share** the same `char[]` data if they have identical characters.

###### Internal Working:

1. G1GC runs a garbage collection cycle.
2. While examining live `String` objects in the heap:
   - It checks if two or more strings have **identical content** (i.e., their `char[]` arrays are equal).
3. If found:
   - The GC **modifies the internal `value[]` field** of one string to **point to the already existing `char[]`** used by another.
4. The GC frees the unused `char[]` arrays.



<h3 id="16">16. What is the difference between isEmpty() and isBlank() in Java?</h3>

| Feature                   | `isEmpty()`                           | `isBlank()`                                           |
| ------------------------- | ------------------------------------- | ----------------------------------------------------- |
| **Returns true if**       | String has **zero length**            | String is **empty \*or\* only contains whitespace**   |
| **Whitespace characters** | Not ignored                           | Considered as blank                                   |
| **Unicode whitespace**    | Not handled                           | Handles all Unicode whitespace (`\u2002`, `\t`, etc.) |
| **Null safe?**            | Throws `NullPointerException` if null | Also throws `NullPointerException` if null            |
| **Defined in**            | `java.lang.String` (since Java 6)     | `java.lang.String` (since Java 11)                    |



<h3 id="17">17. How to extract substring between two delimiters?</h3>

```java
class Main {
    public static void main(String[] args) {
        String word = "i am [Rahul] NR";
        String res = findValueBetween(word, "[", "]");
        
        System.out.println(res);
    }
    
    private static String findValueBetween(String val, String dl1, String dl2) {
        if(val == null || dl1 == null || dl2 == null) {
            return null;
        }
        
        int firstInd = val.indexOf(dl1) + dl1.length();
        int secInd = val.indexOf(dl2);
        
        if(firstInd == -1 || secInd == -1) {
            return null;
        }
        
        return val.substring(firstInd, secInd);
    }
}
```



<h3 id="18">18. Remove duplicate characters from a string</h3>

```java
private static String removeDuplicates(String val) {
    // Convert the input string to a character array for easy iteration
    char[] valArr = val.toCharArray();

    // Boolean array used to track if a character has already been seen
    // 256 size supports extended ASCII (0-255)
    boolean[] isPresent = new boolean[256];

    // StringBuilder to efficiently build the result string without duplicates
    StringBuilder sb = new StringBuilder();
    
    // Loop through each character in the array
    for (char ev : valArr) {
        // 'ev' is implicitly converted to its integer ASCII/Unicode value to index the array
        // If this character has not been seen before, include it in result
        if (!isPresent[ev]) {
            sb.append(ev);         // Add character to result
            isPresent[ev] = true;  // Mark this character as seen
        }
    }
    
    // Return the final string with duplicates removed
    return sb.toString();
}

```

