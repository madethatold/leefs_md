# 字符串的比较

字符串比较是常见的操作，包括比较相等、比较大小、比较前缀和后缀串等。在java 中，比较字符串的常用方法有 3 个：equals() 方法、equalsIgnoreCase() 方法、 compareTo() 方法。下面详细介绍这 3 个方法的使用。

## equals

equals() 方法将逐个地比较两个字符串的每个字符是否相同。如果两个字符串具有相同的字符和长度，它返回 true，否则返回 false。对于字符的大小写，也在检查的范围之内。equals() 方法的语法格式如下：

```java
str1.equals(str2);

String str1 = "abc";
String str2 = new String("abc");
String str3 = "ABC";
System.out.println(str1.equals(str2)); // 输出 true
System.out.println(str1.equals(str3)); // 输出 false
```

str1和str2既可以是字符串变量，也可以是字符串字面量。

## equalsIgnoreCase() 

equalsIgnoreCase() 方法的作用和语法与 equals() 方法完全相同，唯一不同的是 equalsIgnoreCase() 比较时不区分大小写。当比较两个字符串时，它会认为 A-Z 和 a-z 是一样的。

```java
String str1 = "abc";
String str2 = "ABC";
System.out.println(str1.equalsIgnoreCase(str2));    // 输出 true
```

## compareTo()

compareTo() 方法用于按字典顺序比较两个字符串的大小，该比较是基于字符串各个字符的 Unicode 值。

```java
str.compareTo(String otherstr);
```

它会按字典顺序将 str 表示的字符序列与 otherstr 参数表示的字符序列进行比较。如果按字典顺序 str 位于 otherster 参数之前，比较结果为一个负整数；如果 str 位于 otherstr 之后，比较结果为一个正整数；如果两个字符串相等，则结果为 0。

```java
		String a = "ab";
        String b = "ad";
        System.out.println(a.compareTo(b));//-2
        String s1 = "A";
        String s2 = "a";
        System.out.println(s1.compareTo(s2));//-32

        String s3 = "14";
        String s4 = "24";
        System.out.println((s3+s4).compareTo(s4+s3));//-1
```



## Objects.equals

`Objects.equals()` 这个静态方法的优势在于不需要在调用之前判空。

```java
public static boolean equals(Object a, Object b) {
    return (a == b) || (a != null && a.equals(b));
}
```

如果直接使用 `a.equals(b)`，则需要在调用之前对 a 进行判空，否则可能会抛出空指针 `java.lang.NullPointerException`。`Objects.equals()` 用起来就完全没有这个担心。

```java
Objects.equals("小萝莉", new String("小" + "萝莉")) // --> true
Objects.equals(null, new String("小" + "萝莉")); // --> false
Objects.equals(null, null) // --> true

String a = null;
a.equals(new String("小" + "萝莉")); // throw exception
```

## String 类的 `.contentEquals()`

`.contentEquals()` 的优势在于可以将字符串与任何的字符序列（StringBuffer、StringBuilder、String、CharSequence）进行比较。



```java
public boolean contentEquals(CharSequence cs) {
    // Argument is a StringBuffer, StringBuilder
    if (cs instanceof AbstractStringBuilder) {
        if (cs instanceof StringBuffer) {
            synchronized(cs) {
                return nonSyncContentEquals((AbstractStringBuilder)cs);
            }
        } else {
            return nonSyncContentEquals((AbstractStringBuilder)cs);
        }
    }
    // Argument is a String
    if (cs instanceof String) {
        return equals(cs);
    }
    // Argument is a generic CharSequence
    int n = cs.length();
    if (n != length()) {
        return false;
    }
    byte[] val = this.value;
    if (isLatin1()) {
        for (int i = 0; i < n; i++) {
            if ((val[i] & 0xff) != cs.charAt(i)) {
                return false;
            }
        }
    } else {
        if (!StringUTF16.contentEquals(val, cs, n)) {
            return false;
        }
    }
    return true;
}
```



从源码上可以看得出，如果 cs 是 StringBuffer，该方法还会进行同步，非常的智能化；如果是 String 的话，其实调用的还是 `equals()` 方法。当然了，这也就意味着使用该方法进行比较的时候，多出来了很多步骤，性能上有些损失



# String类源码

## String类的声明

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
}
```

String类是final的，也就是不可以被子类继承。

实现了`Serializable`接口，也就是可以序列化。

实现了Comparable接口，也就是说比较两个字符串时，不要使用“==”来比较，而是使用`compareTo`方法。

因为 == 是用来比较两个对象的地址，这个在讲字符串比较的时候会详细讲。如果只是说比较字符串内容的话，可以使用 String 类的 equals 方法：

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String) anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

## String类的底层实现

```java
private final char value[];
```

Java 9 以前，String 是用 char 型数组实现的，之后改成了 byte 型数组实现，并增加了 coder 来表示编码。这样做的好处是在 Latin1 字符为主的程序里，可以把 String 占用的内存减少一半。当然，天下没有免费的午餐，这个改进在节省内存的同时引入了编码检测的开销。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    @Stable
    private final byte[] value;
    private final byte coder;
    private int hash;
}
```

从 `char[]` 到 `byte[]`，最主要的目的是**节省字符串占用的内存空间**。内存占用减少带来的另外一个好处，就是 GC 次数也会减少。

char 类型的数据在 JVM 中是占用两个字节的，并且使用的是 UTF-8编码，也就是说，使用 `char[]` 来表示 String 就会导致，即使 String 中的字符只用一个字节就能表示，也得占用两个字节。

当然了，仅仅将 `char[]` 优化为 `byte[]` 是不够的，还要配合 Latin-1 的编码方式，该编码方式是用单个字节来表示字符的，这样就比 UTF-8 编码节省了更多的空间。

换句话说，对于：

```
String name = "jack";
```

这样的，使用 Latin-1 编码，占用 4 个字节就够了。

但对于：

```
String name = "小二";
```

这种只能使用 UTF16 来编码。

针对 JDK 9 的 String 源码里，为了区别编码方式，追加了一个 coder 字段来区分。`private final byte coder;`Java 会根据字符串的内容自动设置为相应的编码，要么 Latin-1 要么 UTF16。



也就是说，从 `char[]` 到 `byte[]`，**中文是两个字节，纯英文是一个字节，在此之前呢，中文是两个字节，英文也是两个字节**。

UTF-8 是变长的，那对于 String 这种有随机访问方法的类来说，就很不方便。所谓的随机访问，就是charAt、subString这种方法，随便指定一个数字，String要能给出结果。如果字符串中的每个字符占用的内存是不定长的，那么进行随机访问的时候，就需要从头开始数每个字符的长度，才能找到你想要的字符。

那你可能会问，UTF-16也是变长的呢？一个字符还可能占用 4 个字节呢？

的确，UTF-16 使用 2 个或者 4 个字节来存储字符。

- 对于 Unicode 编号范围在 0 ~ FFFF 之间的字符，UTF-16 使用两个字节存储。
- 对于 Unicode 编号范围在 10000 ~ 10FFFF 之间的字符，UTF-16 使用四个字节存储，具体来说就是：将字符编号的所有比特位分成两部分，较高的一些比特位用一个值介于 D800~~DBFF 之间的双字节存储，较低的一些比特位（剩下的比特位）用一个值介于 DC00~~DFFF 之间的双字节存储。

但是在 Java 中，一个字符（char）就是 2 个字节，占 4 个字节的字符，在 Java 里也是用两个 char 来存储的，而String的各种操作，都是以Java的字符（char）为单位的，charAt是取得第几个char，subString取的也是第几个到第几个char组成的子串，甚至length返回的都是char的个数。

所以UTF-16在Java的世界里，就可以视为一个定长的编码。



## String的hashCode方法

每一个字符串都会有一个 hash 值，这个哈希值在很大概率是不会重复的，因此 String 很适合来作为 `HashMap` 的键值。

```java
private int hash; // Cache the hash code for the string

public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

hashCode 方法首先检查是否已经计算过哈希码，如果已经计算过，则直接返回缓存的哈希码。否则，方法将使用一个循环遍历字符串的所有字符，并使用一个乘法和加法的组合计算哈希码。这种计算方法被称为“31 倍哈希法”。计算完成后，将得到的哈希值存储在 hash 成员变量中，以便下次调用 hashCode 方法时直接返回该值，而不需要重新计算。这是一种缓存优化，称为“惰性计算”。

31倍哈希法（31-Hash）是一种简单有效的字符串哈希算法，常用于对字符串进行哈希处理。该算法的基本思想是将字符串中的每个字符乘以一个固定的质数31的幂次方，并将它们相加得到哈希值。31倍哈希法的优点在于简单易实现，计算速度快，同时也比较均匀地分布在哈希表中。

## String的substring方法

String 类中还有一个方法比较常用 substring，用来截取字符串的，来看源码。

```java
public String substring(int beginIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    int subLen = value.length - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
}
```

substring 方法首先检查参数的有效性，如果参数无效，则抛出 StringIndexOutOfBoundsException异常。接下来，方法根据参数计算子字符串的长度。如果子字符串长度小于零，抛出StringIndexOutOfBoundsException异常。

如果 beginIndex 为 0，且 endIndex 等于字符串的长度，说明子串与原字符串相同，因此直接返回原字符串。否则，使用 value 数组（原字符串的字符数组）的一部分创建一个新的 String 对象并返回。

```java
String str = "Hello, world!";
String subStr = str.substring(7, 12);  // 从第7个字符（包括）提取到第12个字符（不包括）
System.out.println(subStr);  // 输出 "world"

String str = "Hello, world!";
String prefix = str.substring(0, 5);  // 提取前5个字符，即 "Hello,"
String suffix = str.substring(7);     // 提取从第7个字符开始的所有字符，即 "world!"

String str = "   Hello,   world!  ";
String trimmed = str.trim();                  // 去除字符串开头和结尾的空格
String[] words = trimmed.split("\\s+");       // 将字符串按照空格分隔成单词数组
String firstWord = words[0].substring(0, 1);  // 提取第一个单词的首字母
System.out.println(firstWord);                // 输出 "H"

String str = "1234-5678-9012-3456";
String[] parts = str.split("-");             // 将字符串按照连字符分隔成四个部分
String last4Digits = parts[3].substring(1);  // 提取最后一个部分的后三位数字
System.out.println(last4Digits);             // 输出 "456"
```



## String类的indexOf

`indexOf` 方法用于查找一个子字符串在原字符串中第一次出现的位置，并返回该位置的索引

```java
/*
 * 查找字符数组 target 在字符数组 source 中第一次出现的位置。
 * sourceOffset 和 sourceCount 参数指定 source 数组中要搜索的范围，
 * targetOffset 和 targetCount 参数指定 target 数组中要搜索的范围，
 * fromIndex 参数指定开始搜索的位置。
 * 如果找到了 target 数组，则返回它在 source 数组中的位置索引（从0开始），
 * 否则返回-1。
 */
static int indexOf(char[] source, int sourceOffset, int sourceCount,
        char[] target, int targetOffset, int targetCount,
        int fromIndex) {
    // 如果开始搜索的位置已经超出 source 数组的范围，则直接返回-1（如果 target 数组为空，则返回 sourceCount）
    if (fromIndex >= sourceCount) {
        return (targetCount == 0 ? sourceCount : -1);
    }
    // 如果开始搜索的位置小于0，则从0开始搜索
    if (fromIndex < 0) {
        fromIndex = 0;
    }
    // 如果 target 数组为空，则直接返回开始搜索的位置
    if (targetCount == 0) {
        return fromIndex;
    }

    // 查找 target 数组的第一个字符在 source 数组中的位置
    char first = target[targetOffset];
    int max = sourceOffset + (sourceCount - targetCount);

    // 循环查找 target 数组在 source 数组中的位置
    for (int i = sourceOffset + fromIndex; i <= max; i++) {
        /* Look for first character. */
        // 如果 source 数组中当前位置的字符不是 target 数组的第一个字符，则在 source 数组中继续查找 target 数组的第一个字符
        if (source[i] != first) {
            while (++i <= max && source[i] != first);
        }

        /* Found first character, now look at the rest of v2 */
        // 如果在 source 数组中找到了 target 数组的第一个字符，则继续查找 target 数组的剩余部分是否匹配
        if (i <= max) {
            int j = i + 1;
            int end = j + targetCount - 1;
            for (int k = targetOffset + 1; j < end && source[j]
                    == target[k]; j++, k++);

            // 如果 target 数组全部匹配，则返回在 source 数组中的位置索引
            if (j == end) {
                /* Found whole string. */
                return i - sourceOffset;
            }
        }
    }
    // 没有找到 target 数组，则返回-1
    return -1;
}
```



```java
String str = "Hello, world!";
int index = str.indexOf(",");     // 查找逗号在 str 中第一次出现的位置
System.out.println(index);        // 输出 5

String str = "Hello, world!";
int index1 = str.indexOf("o");    // 查找 "o" 子字符串在 str 中第一次出现的位置
int index2 = str.indexOf("o", 5); // 从索引为5的位置开始查找 "o" 子字符串在 str 中第一次出现的位置
System.out.println(index1);       // 输出 4
System.out.println(index2);       // 输出 8
```



## 其他方法

比如说 `length()` 用于返回字符串长度。

比如说 `isEmpty()` 用于判断字符串是否为空。

比如说 `charAt()` 用于返回指定索引处的字符。

比如说 `getBytes()` 用于返回字符串的字节数组

指定string的编码方式：

```java
String s = "你好";
System.out.println(Arrays.toString(s.getBytes(StandardCharsets.UTF_8)));
```

# String的不可变

```
 		String s = "123";
        System.out.println(System.identityHashCode(s));
        s = "lkl";
        System.out.println(System.identityHashCode(s));
```

虽然String是不可变的，但是变量s是可变的。在Java中，变量是可以重新赋值的。当你将一个新的字符串赋值给变量s时，实际上是创建了一个新的String对象，并将变量s指向了这个新的对象。原来的"123"字符串对象仍然存在，但是没有任何变量指向它，所以它会被Java的垃圾回收机制清理掉。



- String 类被 final修饰，所以它不会有子类，这就意味着没有子类可以重写它的方法，改变它的行为。
- String 类的数据存储在 `char[]` 数组中，而这个数组也被 final 关键字修饰了，这就表示 String 对象是没法被修改的，只要初始化一次，值就确定了。

保证 String 对象的安全性，避免被篡改，毕竟像密码这种隐私信息一般就是用字符串存储的。

保证哈希值不会频繁变更。毕竟要经常作为哈希表的键值，经常变更的话，哈希表的性能就会很差劲。

在 String 类中，哈希值是在第一次计算时缓存的，后续对该哈希值的请求将直接使用缓存值。这有助于提高哈希表等数据结构的性能。以下是一个简单的示例，演示了字符串的哈希值缓存机制：

```java
		String a = "123";
        String b = "123";
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
```

创建了两个内容一模一样的字符串，首次计算 a 的哈希值时，会进行实际计算并缓存该值。当我们再次计算 a的哈希值或计算具有相同内容的 b的哈希值时，将直接返回缓存的哈希值，而不进行重新计算。

由于 String 对象是不可变的，其哈希值在创建后不会发生变化。这使得 String 类可以缓存哈希值，提高哈希表等数据结构的性能。如果 String 是可变的，那么在每次修改时都需要重新计算哈希值，这会降低性能。



**String 对象一旦被创建后就固定不变了，对 String 对象的任何修改都不会影响到原来的字符串对象，都会生成新的字符串对象。**

# 字符串常量池

```java
String s = new String("123");
```

使用 new 关键字创建一个字符串对象时，Java 虚拟机会先在字符串常量池中查找有没“123”这个字符串对象，如果有，就不会在字符串常量池中创建“123”这个对象了，直接在堆中创建一个“123”的字符串对象，然后将堆中这个”123“的对象地址返回赋值给变量 s。如果没有，现在字符串常量池中创建一个”123“的字符串对象，然后再在堆中创建一个对象，并将堆中这个对象的地址返回赋值给s。

在Java中，栈上存储的是基本数据类型的变量和对象的引用，而对象本身则存储在堆上。

对于这行代码 `String s = new String(123);`，它创建了两个对象：一个是字符串对象 "123"，它被添加到了字符串常量池中，另一个是通过 new String() 构造函数创建的字符串对象 "123"，它被分配在堆内存中，同时引用变量 s 存储在栈上，它指向堆内存中的字符串对象 "123"。



**由于字符串的使用频率实在是太高了，所以 Java 虚拟机为了提高性能和减少内存开销，在创建字符串对象的时候进行了一些优化，特意为字符串开辟了一块空间——也就是字符串常量池。**

```java
String s = new String("bc");
String s1 = new String("bc");
```

这两行代码会创建三个对象，字符串常量池中一个，堆上两个。

```java
String s = "三妹";
String s1 = "三妹";
```

这两行代码只会创建一个对象，就是字符串常量池中的那个。





# String.intern() 方法



第一，使用双引号声明的字符串对象会保存在字符串常量池中。

第二，使用 new 关键字创建的字符串对象会先从字符串常量池中找，如果没找到就创建一个，然后再在堆中创建字符串对象；如果找到了，就直接在堆中创建字符串对象。

第三，针对没有使用双引号声明的字符串对象来说，就像下面代码中的 s1 那样：

```java
String s1 = new String("二哥") + new String("三妹");
```

如果想把 s1 的内容也放入字符串常量池的话，可以调用 `intern()` 方法来完成。



Java 7 之前，执行 `String.intern()` 方法的时候，不管对象在堆中是否已经创建，字符串常量池中仍然会创建一个内容完全相同的新对象； Java 7 之后呢，由于字符串常量池放在了堆中，执行 `String.intern()` 方法的时候，如果对象在堆中已经创建了，字符串常量池中就不需要再创建新的对象了，而是直接保存堆中对象的引用，也就节省了一部分的内存空间。



```java
String s1 = new String("123");
String s2 = s1.intern();
System.out.println(s1 == s2);
```

首先在字符串常量池中创建了一个“123”的对象，然后在堆中创建了一个对象，s1引用的是堆中的对象。

对 s1 执行 `intern()` 方法，该方法会从字符串常量池中查找“123”这个字符串是否存在，此时是存在的，所以 s2 引用的是字符串常量池中的对象。

也就意味着 s1 和 s2 的引用地址是不同的，一个来自堆，一个来自字符串常量池，所以输出的结果为 false。

```java
String s1 = new String("二哥") + new String("三妹");
String s2 = s1.intern();
System.out.println(s1 == s2);
```

这段代码的输出是true

第一行代码，会在字符串常量池中创建两个对象，一个是“二哥”，一个是“三妹”，然后在堆中会创建两个匿名对象“二哥”和“三妹”，最后还有一个“二哥三妹”的对象（稍后会解释），s1 引用的是堆中“二哥三妹”这个对象。

第二行代码，对 s1 执行 `intern()` 方法，该方法会从字符串常量池中查找“二哥三妹”这个对象是否存在，此时不存在的，但堆中已经存在了，所以字符串常量池中保存的是堆中这个“二哥三妹”对象的引用，也就是说，s2 和 s1 的引用地址是相同的，所以输出的结果为 true。



再来解释一下 `String s1 = new String("二哥") + new String("三妹")` 这行代码。

1. 创建 "二哥" 字符串对象，存储在字符串常量池中。
2. 创建 "三妹" 字符串对象，存储在字符串常量池中。
3. 执行 `new String("二哥")`，在堆上创建一个字符串对象，内容为 "二哥"。
4. 执行 `new String("三妹")`，在堆上创建一个字符串对象，内容为 "三妹"。
5. 执行 `new String("二哥") + new String("三妹")`，会创建一个 StringBuilder 对象，并将 "二哥" 和 "三妹" 追加到其中，然后调用 StringBuilder 对象的 toString() 方法，将其转换为一个新的字符串对象，内容为 "二哥三妹"。这个新的字符串对象存储在堆上。

也就是说，当编译器遇到 `+` 号这个操作符的时候，会将 `new String("二哥") + new String("三妹")` 这行代码编译为以下代码：

```java
new StringBuilder().append("二哥").append("三妹").toString();
```

- 创建一个 StringBuilder 对象。
- 在 StringBuilder 对象上调用 append("二哥")，将 "二哥" 追加到 StringBuilder 中。
- 在 StringBuilder 对象上调用 append("三妹")，将 "三妹" 追加到 StringBuilder 中。
- 在 StringBuilder 对象上调用 toString() 方法，将 StringBuilder 转换为一个新的字符串对象，内容为 "二哥三妹"。



```java
new String("小萝莉").equals("小萝莉")//true

new String("小萝莉") == "小萝莉"//false
//==操作符左侧的是在堆中创建的对象，右侧是在字符串常量池中的对象，尽管内容相同，但内存地址不同

new String("小萝莉") == new String("小萝莉")//false
//两个不同的匿名对象
    
"小萝莉" == "小萝莉"//true
//字符串常量池中只会有一个相同内容的对象
    
"小萝莉" == "小" + "萝莉"//true
//由于‘小’和‘萝莉’都在字符串常量池，所以编译器在遇到‘+’操作符的时候将其自动优化为“小萝莉
    
new String("小萝莉").intern() == "小萝莉"//true
//new String("小萝莉") 在执行的时候，会先在字符串常量池中创建对象，然后再在堆中创建对象；
//执行 intern() 方法的时候发现字符串常量池中已经有了‘小萝莉’这个对象，所以就直接返回字符串常量池中的对象引用了，那再与字符串常量池中的‘小萝莉’比较
```







# String、StringBuilder、StringBuffer

String是不可变的，当遇到字符串拼接等问题的时候，需要考虑性能的问题,你不能毫无顾虑地生产太多 String 对象，对珍贵的内存造成不必要的压力。于是 Java 就设计了一个专门用来解决此问题的 StringBuffer 类。不过，由于 StringBuffer 操作字符串的方法加了`synchronized`进行了同步，主要是考虑到多线程环境下的安全问题，所以执行效率会比较低。在单线程环境下，使用StringBuilder提升效率。



 Java 编译器将字符串拼接操作（`+`）转换为了 StringBuilder 对象的 append 方法，然后再调用 StringBuilder 对象的 toString 方法返回拼接后的字符串。

来看一下 StringBuilder 的 toString 方法：

```java
public String toString() {
    return new String(value, 0, count);
}
```

value 是一个 char 类型的数组，在StringBuilder对象创建的时候，会为value创建一定的内存空间，用于存储字符串，随着字符串的拼接，value 数组的长度会不断增加，因此在 StringBuilder 对象的实现中，value 数组的长度是可以动态扩展的。

```java
public StringBuilder append(String str) {
    super.append(str);
    return this;
}
```





# String拼接

## +号操作符拼接字符串

\+ 号操作符其实被 Java 在编译的时候重新解释了，换一种说法就是，+ 号操作符是一种语法糖，让字符串的拼接变得更简便了

编译的时候把“+”号操作符替换成了 StringBuilder 的 `append()` 方法



## StringBuilder.append

循环体内，拼接字符串最好使用 StringBuilder 的 `append()` 方法，而不是 + 号操作符。原因就在于循环体内如果用 + 号操作符的话，就会产生大量的 StringBuilder 对象，不仅占用了更多的内存空间，还会让 Java 虚拟机不停的进行垃圾回收，从而降低了程序的性能。



### append方法解析

```java
public StringBuilder append(String str) {
    super.append(str);
    return this;
}
```

父类 AbstractStringBuilder 的 `append()` 方法：


```java
public AbstractStringBuilder append(String str) {
    if (str == null)
        return appendNull();
    int len = str.length();
    ensureCapacityInternal(count + len);
    str.getChars(0, len, value, count);
    count += len;
    return this;
}
```

判断拼接的字符串是不是 null，如果是，当做字符串“null”来处理。

获取字符串的长度。

由于字符串内部是用数组实现的，所以需要先判断拼接后的字符数组长度是否超过当前数组的长度，如果超过，先对数组进行扩容，然后把原有的值复制到新的数组中。

将拼接的字符串 str 复制到目标数组 value 中

更新数组的长度 count





## String.concat

tring 类的 `concat()` 方法，有点像 StringBuilder 类的 `append()` 方法。

```java
String chenmo = "沉默";
String wanger = "王二";
System.out.println(chenmo.concat(wanger));
```

1）如果拼接的字符串的长度为 0，那么返回拼接前的字符串。

2）将原字符串的字符数组 value 复制到变量 buf 数组中。

3）把拼接的字符串 str 复制到字符数组 buf 中，并返回新的字符串对象。

“和 `+` 号操作符相比，`concat()` 方法在遇到字符串为 null 的时候，会抛出 NullPointerException，而“+”号操作符会把 null 当做是“null”字符串来处理。

**如果拼接的字符串是一个空字符串（""），那么 concat 的效率要更高一点,毕竟不需要 `new StringBuilder` 对象。**

**如果拼接的字符串非常多，`concat()` 的效率就会下降，因为创建的字符串对象越来越多。**

## String.join

String 类有一个静态方法 `join()`，可以这样来使用。

```java
		String s1 = "1";
        String s2 = "2";
        String s3 = "3";
        System.out.println(String.join("-", s1,s2,s3));//1-2-3
```

来看一下 join 方法的源码：

```java
public static String join(CharSequence delimiter, CharSequence... elements) {
    Objects.requireNonNull(delimiter);
    Objects.requireNonNull(elements);
    // Number of elements not likely worth Arrays.stream overhead.
    StringJoiner joiner = new StringJoiner(delimiter);
    for (CharSequence cs: elements) {
        joiner.add(cs);
    }
    return joiner.toString();
}
```

里面新建了一个叫 StringJoiner 的对象，然后通过 for-each 循环把可变参数添加了进来，最后调用 `toString()` 方法返回 String。

## StringUtils.join

实际的工作中，`org.apache.commons.lang3.StringUtils` 的 `join()` 方法也经常用来进行字符串拼接。”

```java
String chenmo = "12";
String wanger = "34";
StringUtils.join(chenmo, wanger);
```

该方法不用担心 `NullPointerException`。

来看一下源码：

```java
public static String join(final Object[] array, String separator, final int startIndex, final int endIndex) {
    if (array == null) {
        return null;
    }
    if (separator == null) {
        separator = EMPTY;
    }

    final StringBuilder buf = new StringBuilder(noOfItems * 16);

    for (int i = startIndex; i < endIndex; i++) {
        if (i > startIndex) {
            buf.append(separator);
        }
        if (array[i] != null) {
            buf.append(array[i]);
        }
    }
    return buf.toString();
}
```

内部使用的仍然是 StringBuilder。



# String拆分

String 类的 `split()` 方法使用的时候，是建立在字符串是确定的情况下，最重要的是分隔符是确定的。

大约有 12 种英文特殊符号，如果直接拿这些特殊符号替换上面代码中的分隔符（中文逗号），这段程序在运行的时候就会出现以下提到的错误。

- 反斜杠 `\`（ArrayIndexOutOfBoundsException）
- 插入符号 `^`（同上）
- 美元符号 `$`（同上）
- 逗点 `.`（同上）
- 竖线 `|`（正常，没有出错）
- 问号 `?`（PatternSyntaxException）
- 星号 `*`（同上）
- 加号 `+`（同上）
- 左小括号或者右小括号 `()`（同上）
- 左方括号或者右方括号 `[]`（同上）
- 左大括号或者右大括号 `{}`（同上）

此时需要使用正则表达式。



除此之外，还可以使用Pattern类的`quote`方法包裹英文逗点，

```java
String [] parts = cmower.split(Pattern.quote("."));
```