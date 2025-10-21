# Core Java (String)



| No   | Content                                                      |
| ---- | ------------------------------------------------------------ |
| 1    | [What is a String?](#1)                                      |
| 2    | Why String is immutable?                                     |
| 3    | Java String concat() Method                                  |
| 4    | Substring, length, indexOf and toCharArray in java           |
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



<h3 id="1">1. What is a String</h2>

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
