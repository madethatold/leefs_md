# Scanner工具类

Java 的 Scanner 类是一个方便在控制台扫描用户输入的工具类，虽然它也可以扫描文件内容，但我们通常更喜欢它扮演前面的角色，因为扫描文件可以通过文件流来完成。

## 扫描控制台的输入

```java
		Scanner scanner = new Scanner(System.in); // 创建 Scanner 对象，从标准输入流中读取数据
        System.out.print("请输入一个整数：");
        int num = scanner.nextInt(); // 获取用户输入的整数
        System.out.println("您输入的整数是：" + num);123
        scanner.nextLine(); // 读取换行符，避免影响下一次读取
        System.out.print("请输入一个字符串：");
        String str = scanner.nextLine(); // 获取用户输入的字符串
        System.out.println("您输入的字符串是：" + str);
        scanner.close(); // 关闭 Scanner 对象
```

其中 System.in 返回的是一个字节输入流 InputStream，和 System.out 刚好对应。



### nextline

`nextLine()` 方法会扫描输入流中的字符，直到遇到行末尾的换行符 `\n`，然后将该行的内容作为字符串返回，同时，`nextLine()` 会将 Scanner 对象的位置移动到下一行的开头，以便下一次读取数据时从下一行的开头开始读取。



```java
Scanner scanner = new Scanner(System.in); // 创建 Scanner 对象，从标准输入流中读取数据
System.out.println("请输入多行文本，以空行结束：");
StringBuilder sb = new StringBuilder(); // 创建 StringBuilder 对象，用于保存读取的文本
String line = scanner.nextLine(); // 读取输入流中的第一行
while (!line.isEmpty()) { // 如果读取的行不为空，则继续读取下一行
    sb.append(line).append("\n"); // 将当前行的内容添加到 StringBuilder 对象中，并换行
    line = scanner.nextLine(); // 读取下一行
}
System.out.println("您输入的文本是：\n" + sb.toString()); // 打印读取的文本
scanner.close(); // 关闭 Scanner 对象
```

### nextInt

`nextInt()` 用于从输入流中读取下一个整数并返回，如果输入流中没有整数，或者不是整数，将抛出 InputMismatchException 异常。

### 其他

- `boolean hasNext()`：检查输入流是否还有下一个标记。
- `boolean hasNextLine()`：检查输入流是否还有下一行。
- `String next()`：读取输入流中的下一个标记（使用默认的分隔符，通常是空格或换行符）。
- `double nextDouble()`：读取输入流中的下一个双精度浮点数。

```java
Scanner scanner = new Scanner(System.in); // 创建 Scanner 对象，从标准输入流中读取数据
System.out.print("请输入一个整数：");
if (scanner.hasNextInt()) { // 判断输入流中是否有下一个整数
    int num = scanner.nextInt(); // 读取输入流中的下一个整数
    System.out.println("您输入的整数是：" + num);
} else {
    System.out.println("输入的不是整数！");
}
scanner.nextLine(); // 读取输入流中的换行符

System.out.print("请输入多个单词，以空格分隔：");
while (scanner.hasNext()) { // 判断输入流中是否还有下一个标记
    String word = scanner.next(); // 读取输入流中的下一个单词
    System.out.println("您输入的单词是：" + word);
}
scanner.nextLine(); // 读取输入流中的换行符

System.out.print("请输入一个实数：");
if (scanner.hasNextDouble()) { // 判断输入流中是否有下一个实数
    double num = scanner.nextDouble(); // 读取输入流中的下一个实数
    System.out.println("您输入的实数是：" + num);
} else {
    System.out.println("输入的不是实数！");
}
scanner.nextLine(); // 读取输入流中的换行符

System.out.print("请输入一个字符串：");
if (scanner.hasNextLine()) { // 判断输入流中是否有下一行
    String line = scanner.nextLine(); // 读取输入流中的下一行
    System.out.println("您输入的字符串是：" + line);
} else {
    System.out.println("输入的不是字符串！");
}
scanner.close(); // 关闭 Scanner 对象
```





## 扫描文件

```java
try {
    // 创建 File 对象，表示要扫描的文件
    File file = new File("docs/安装环境.md");
    Scanner scanner = new Scanner(file); // 创建 Scanner 对象，从文件中读取数据
    while (scanner.hasNextLine()) { // 判断文件中是否有下一行
        String line = scanner.nextLine(); // 读取文件中的下一行
        System.out.println(line); // 打印读取的行
    }
    scanner.close(); // 关闭 Scanner 对象
} catch (FileNotFoundException e) {
    System.out.println("文件不存在！");
}
```

以上是Scanner扫描文件的方式。

在上面的示例中，我们首先创建了一个 File 对象，表示要扫描的文件。然后，我们使用 Scanner 类的构造方法来创建 Scanner 对象，将文件作为参数传递给构造方法。在 while 循环中，我们使用 `hasNextLine()` 方法来判断文件中是否有下一行，如果有，则使用 `nextLine()` 方法读取该行字符串，并使用 `println()` 方法将其打印出来。最后，我们在程序结束前使用 `close()` 方法关闭 Scanner 对象。



除了使用循环+nextLine，我们还可以使用 useDelimiter 方法设置文件结束符 `\Z` 来读取整个文件。

```java
// 创建 File 对象，表示要扫描的文件
Scanner scanner = new Scanner(new File("docs/安装环境.md")); // 创建 Scanner 对象，从文件中读取数据
scanner.useDelimiter("\\Z"); // 设置分隔符为文件结尾
if (scanner.hasNext()) { // 判断文件中是否有下一行
    String content = scanner.next(); // 读取文件中的下一行
    System.out.println(content); // 打印读取的行
}
scanner.close(); // 关闭 Scanner 对象
```

正则表达式中的 `\Z` 表示输入的结尾，也就是文件结束符。在 Scanner 类中，我们可以使用 `\Z` 作为分隔符，以便读取整个文件内容。



## 查找匹配项

```java
String input = "good good study, day day up.";
Scanner scanner = new Scanner(input);
String result;

// 使用 findInLine() 方法查找字符串中的单词
result = scanner.findInLine("study");
System.out.println("findInLine(): " + result); // 输出 "study"

// 使用 findWithinHorizon() 方法查找字符串中的单词
scanner = new Scanner(input);
result = scanner.findWithinHorizon("study", 20);
System.out.println("findWithinHorizon(): " + result); // 输出 "study"

scanner.close(); // 关闭 Scanner 对象
```



在上面的示例中，我们首先创建了一个字符串 input，表示要查找的文本。然后，我们使用 Scanner 类的构造方法创建 Scanner 对象，并将 input 作为输入流传递给该对象。接着，我们使用 `findInLine()` 方法和 `findWithinHorizon()` 方法分别查找字符串中的单词 "study"。其中，`findInLine()` 方法在当前行中查找匹配项，而 `findWithinHorizon()` 方法在指定的限制范围内查找匹配项。在本例中，我们将查找的范围限制为前 20 个字符。

需要注意的是，`findInLine()` 方法和 `findWithinHorizon()` 方法都返回找到的匹配项。如果没有找到匹配项，则返回 null。此外，`findInLine()` 方法和 `findWithinHorizon()` 方法都会忽略默认的分隔符，因此需要使用正则表达式来指定查找的模式。在本例中，我们使用了字符串 "study" 作为查找的模式。

当然我们也可以使用正则表达式，比如说我们要在下面的文件中查找 openjdk 这个关键字。

```java
// 创建 File 对象，表示要扫描的文件
Scanner scanner = new Scanner(new File("docs/安装环境.md")); // 创建 Scanner 对象，从文件中读取数据
Pattern pattern = Pattern.compile("op..jdk");
String result;
while ((result = scanner.findWithinHorizon(pattern, 0)) != null) {
    System.out.println("findWithinHorizon(): " + result);
}
```

我们用正则表达式 pattern 来表示 `openjdk` 这个关键字，`op..jdk` 中的 `.` 表示任意字符，可以通过查找正则表达式去了解。

然后我们使用 while 循环来查找文件中所有的 `openjdk`，其中 findWithinHorizon 方法的第二个参数如果为 0 则表示忽略边界，如果没找到，会返回 null。







# Arrays工具类

数组专用工具类指的是 `java.util.Arrays` 类，基本上常见的数组操作，这个类都提供了静态方法可供直接调用。

## 创建数组

使用 Arrays 类创建数组可以通过以下三个方法：

- copyOf，复制指定的数组，截取或用 null 填充
- copyOfRange，复制指定范围内的数组到一个新的数组
- fill，对数组进行填充

```java
String[] intro = new String[] { "fu", "aa", "hi", "tp" };
String[] revised = Arrays.copyOf(intro, 3);
String[] expanded = Arrays.copyOf(intro, 5);
System.out.println(Arrays.toString(revised));
System.out.println(Arrays.toString(expanded));
//[fu, aa, hi]
//[fu, aa, hi, tp, null]
//revised 截取了最后一位，因为长度是 3 嘛；expanded 用 null 填充了一位，因为长度是 5。
String[] abridgement = Arrays.copyOfRange(intro, 0, 3);
System.out.println(Arrays.toString(abridgement));
String[] abridgementExpanded = Arrays.copyOfRange(intro, 0, 6);
System.out.println(Arrays.toString(abridgementExpanded));
//[fu, aa, hi]
//[fu, aa, hi, tp, null, null]
String[] stutter = new String[4];
Arrays.fill(stutter, "ok");
System.out.println(Arrays.toString(stutter));
//[ok, ok, ok, ok]
```

## 比较数组

Arrays 类的 `equals()` 方法用来判断两个数组是否相等

```java
String[] intro = new String[]{"aka", "jk", "lo", "pp"};
boolean result = Arrays.equals(new String[]{"aka", "jk", "lo", "pp"}, intro);
System.out.println(result);//true
boolean result1 = Arrays.equals(new String[]{"aka", "jk", "lo", "pl"}, intro);
System.out.println(result1);//false
```



简单看一下 `equals()` 方法的源码：

```java
public static boolean equals(Object[] a, Object[] a2) {
    if (a==a2)
        return true;
    if (a==null || a2==null)
        return false;

    int length = a.length;
    if (a2.length != length)
        return false;

    for (int i=0; i<length; i++) {
        if (!Objects.equals(a[i], a2[i]))
            return false;
    }

    return true;
}
```

因为数组是一个对象，所以先使用“==”操作符进行判断，如果不相等，再判断是否为 null，其中一个为 null，返回 false；紧接着判断 length，不等的话，返回 false；否则的话，依次调用 `Objects.equals()` 比较相同位置上的元素是否相等。



除了 `equals()` 方法，还有另外一个诀窍可以判断两个数组是否相等，尽管可能会出现误差。那就是 `Arrays.hashCode()` 方法，先来看一下该方法的源码：

```java
public static int hashCode(Object a[]) {
    if (a == null)
        return 0;

    int result = 1;

    for (Object element : a)
        result = 31 * result + (element == null ? 0 : element.hashCode());

    return result;
}
```

哈希算法本身是非常严谨的，所以如果两个数组的哈希值相等，那几乎可以判断两个数组是相等的。但这样确实不够严谨，优先使用 `Objects.equals()` 方法，当我们想快速确认两个数组是否相等时，可以通过比较 hashCode 来确认。



## 数组排序



```java
String[] intro1 = new String[] { "chen", "mo", "wang", "er" };
String[] sorted = Arrays.copyOf(intro1, 4);
Arrays.sort(sorted);
System.out.println(Arrays.toString(sorted));
```



## 数组检索



```java
		int []nums = new int[]{1,2,45,13,45,12,100,39,76,43};
        int []sorted = Arrays.copyOf(nums,nums.length);
        Arrays.sort(sorted);
        int index = Arrays.binarySearch(sorted,100);
        System.out.println(index);
        String[] intro1 = new String[] { "kj", "vrt", "vse", "no" };
        int index_0 = Arrays.binarySearch(intro1,"kj",String::compareToIgnoreCase);//忽略大小写查找
        System.out.println(index_0);
```





## 数组转流

Arrays 类的 `stream()` 方法可以将数组转换成流：

```java
String[] intro = new String[] { "沉", "默", "王", "二" };
System.out.println(Arrays.stream(intro).count());
```

还可以为 `stream()` 方法指定起始下标和结束下标：

```java
System.out.println(Arrays.stream(intro, 1, 2).count());
```

如果下标的范围有误的时候，比如说从 2 到 1 结束，则程序会抛出 ArrayIndexOutOfBoundsException 异常：

```java
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: origin(2) > fence(1)
	at java.base/java.util.Spliterators.checkFromToBounds(Spliterators.java:387)
```

如果要打印二维数组，或者多维数组可以采用以下方式：

```java
int [][]a = new int[][]{{1,3},{12,34}};
System.out.println(Arrays.deepToString(a));
```



## 打印数组

因为数组是一个对象，直接 `System.out.println` 的话，结果是这样的：

```
[Ljava.lang.String;@3d075dc0
```

使用 `Arrays.toString()`打印数组，该方法的源码如下：

```java
public static String toString(Object[] a) {
    if (a == null)
        return "null";

    int iMax = a.length - 1;
    if (iMax == -1)
        return "[]";

    StringBuilder b = new StringBuilder();
    b.append('[');
    for (int i = 0; ; i++) {
        b.append(String.valueOf(a[i]));
        if (i == iMax)
            return b.append(']').toString();
        b.append(", ");
    }
}
```

- 先判断 null，是的话，直接返回“null”字符串；
- 获取数组的长度，如果数组的长度为 0（ 等价于 length - 1 为 -1），返回中括号“[]”，表示数组为空的；
- 如果数组既不是 null，长度也不为 0，就声明 StringBuilder 对象，然后添加一个数组的开始标记“[”，之后再遍历数组，把每个元素添加进去；其中一个小技巧就是，当遇到末尾元素的时候（i == iMax），不再添加逗号和空格“, ”，而是添加数组的闭合标记“]”。

## 数组转List

尽管数组非常强大，但它自身可以操作的工具方法很少，比如说判断数组中是否包含某个值。如果能转成 List 的话，就简便多了，因为 Java 的集合框架 List中封装了很多常用的方法。

```java
String[] intro = new String[] { "asdfa", "fgddfg", "try", "cvbd" };
List<String> rets = Arrays.asList(intro);
System.out.println(rets.contains("try"));
```

需要注意的是，`Arrays.asList()` 返回的是 `java.util.Arrays.ArrayList`，并不是 [`java.util.ArrayList`]，它的长度是固定的，无法进行元素的删除或者添加。如果执行`add`或`remove`操作会抛出异常，要想操作元素的话，需要多一步转化，转成真正的 `java.util.ArrayList`

```java
List<String> rets1 = new ArrayList<>(Arrays.asList(intro));
```

## setAll

Java 8 新增了 `setAll()` 方法，它提供了一个函数式编程的入口，可以对数组的元素进行填充：

```java
int[] array = new int[10];
Arrays.setAll(array, i -> i * 10);
System.out.println(Arrays.toString(array));
```

可以用来为新数组填充基于原来数组的新元素。

## parallelPrefix

`parallelPrefix()` 方法和 `setAll()` 方法一样，也是 Java 8 之后提供的，提供了一个函数式编程的入口，通过遍历数组中的元素，将当前下标位置上的元素与它之前下标的元素进行操作，然后将操作后的结果覆盖当前下标位置上的元素。

```java
int[] a = new int[]{1, 2, 3, 4, 5};
Arrays.parallelPrefix(a, Integer::sum);
System.out.println(Arrays.toString(a));//[1, 3, 6, 10, 15]
```





# StringUtils工具类

StringUtils 提供了非常多实用的方法，大概有下图的四页到五页，我只截了两页，实在是太多了。

## 字符串判空

StringUtils 给我们提供了多个判空的静态方法，例如：

```java
String str1 = null;
String str2 = "";
String str3 = " ";
String str4 = "abc";
System.out.println(StringUtils.isEmpty(str1));
System.out.println(StringUtils.isEmpty(str2));
System.out.println(StringUtils.isEmpty(str3));
System.out.println(StringUtils.isEmpty(str4));
System.out.println("=====");
System.out.println(StringUtils.isNotEmpty(str1));
System.out.println(StringUtils.isNotEmpty(str2));
System.out.println(StringUtils.isNotEmpty(str3));
System.out.println(StringUtils.isNotEmpty(str4));
System.out.println("=====");
System.out.println(StringUtils.isBlank(str1));
System.out.println(StringUtils.isBlank(str2));
System.out.println(StringUtils.isBlank(str3));
System.out.println(StringUtils.isBlank(str4));
System.out.println("=====");
System.out.println(StringUtils.isNotBlank(str1));
System.out.println(StringUtils.isNotBlank(str2));
System.out.println(StringUtils.isNotBlank(str3));
System.out.println(StringUtils.isNotBlank(str4));
```

执行结果：

```java
true
true
false
false
=====
false
false
true
true
=====
true
true
true
false
=====
false
false
false
true
```

示例中的：`isEmpty`、`isNotEmpty`、`isBlank`和`isNotBlank`，这 4 个判空方法你们可以根据实际情况使用。

优先推荐使用`isBlank`和`isNotBlank`方法，因为它会把`" "`也考虑进去。



## 分割字符串

```java
String str1 = null;
System.out.println(StringUtils.split(str1,","));
System.out.println(str1.split(","));
```

分隔字符串是常见需求，如果直接使用 String 类的 split 方法，就可能会出现空指针异常。

使用 StringUtils 的 split 方法会返回 null。



## 判断是否为纯数字

给定一个字符串，判断它是否为纯数字，可以使用`isNumeric`方法。例如：

```java
String str1 = "123";
String str2 = "123q";
String str3 = "0.33";
System.out.println(StringUtils.isNumeric(str1));
System.out.println(StringUtils.isNumeric(str2));
System.out.println(StringUtils.isNumeric(str3));
```

执行结果：

```
true
false
false
```

## 将集合拼接成字符串

有时候，我们需要将某个集合的内容，拼接成一个字符串，然后输出，这时可以使用`join`方法。例如：

```java
List<String> list = Lists.newArrayList("a", "b", "c");
List<Integer> list2 = Lists.newArrayList(1, 2, 3);
System.out.println(StringUtils.join(list, ","));
System.out.println(StringUtils.join(list2, " "));
```

执行结果：

```
a,b,c
1 2 3
```

## 其他方法

- `trim(String str)`：去除字符串首尾的空白字符。
- `trimToEmpty(String str)`：去除字符串首尾的空白字符，如果字符串为 null，则返回空字符串。
- `trimToNull(String str)`：去除字符串首尾的空白字符，如果结果为空字符串，则返回 null。
- `equals(String str1, String str2)`：比较两个字符串是否相等。
- `equalsIgnoreCase(String str1, String str2)`：比较两个字符串是否相等，忽略大小写。
- `startsWith(String str, String prefix)`：检查字符串是否以指定的前缀开头。
- `endsWith(String str, String suffix)`：检查字符串是否以指定的后缀结尾。
- `contains(String str, CharSequence seq)`：检查字符串是否包含指定的字符序列。
- `indexOf(String str, CharSequence seq)`：返回指定字符序列在字符串中首次出现的索引，如果没有找到，则返回 -1。
- `lastIndexOf(String str, CharSequence seq)`：返回指定字符序列在字符串中最后一次出现的索引，如果没有找到，则返回 -1。
- `substring(String str, int start, int end)`：截取字符串中指定范围的子串。
- `replace(String str, String searchString, String replacement)`：替换字符串中所有出现的搜索字符串为指定的替换字符串。
- `replaceAll(String str, String regex, String replacement)`：使用正则表达式替换字符串中所有匹配的部分。
- `join(Iterable<?> iterable, String separator)`：使用指定的分隔符将可迭代对象中的元素连接为一个字符串。
- `split(String str, String separator)`：使用指定的分隔符将字符串分割为一个字符串数组。
- `capitalize(String str)`：将字符串的第一个字符转换为大写。
- `uncapitalize(String str)`：将字符串的第一个字符转换为小写。

# Objects工具类

Java 的 Objects 类是一个实用工具类，包含了一系列静态方法，用于处理对象。

## 对象判空

Objects 的 `isNull` 方法用于判断对象是否为空，而 `nonNull` 方法判断对象是否不为空。例如：

```java
Integer integer = new Integer(1);

if (Objects.isNull(integer)) {
    System.out.println("对象为空");
}

if (Objects.nonNull(integer)) {
    System.out.println("对象不为空");
}
```

## 判断两个对象是否相等

我们经常需要判断两个对象是否相等，Objects 给我们提供了 `equals` 方法，能非常方便的实现：

```java
Integer integer1 = new Integer(1);
Integer integer2 = new Integer(1);
System.out.println(Objects.equals(integer1, integer2));//true

Integer integer1 = new Integer(1);
Long integer2 = new Long(1);
System.out.println(Objects.equals(integer1, integer2));//false
```



虽然 `Objects.equals()` 方法本身是用来避免坑的，因为它可以处理 null 值的比较，而不会抛出空指针异常。然而，这并不意味着它没有任何潜在问题。实际上，`Objects.equals()` 方法的一个潜在问题是依赖于被比较对象的 `equals()` 方法实现。

当两个对象的类没有正确实现 `equals()` 方法时，`Objects.equals()` 方法可能会产生不符合预期的结果。

```java
public class ObjectsDemo1 {
    public static void main(String[] args) {
        Person person1 = new Person("沉默王二", 18);
        Person person2 = new Person("沉默王二", 18);

        System.out.println(Objects.equals(person1, person2)); // 输出：false
    }
}
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```



因此，为了更好的利用`Objects.equals`方法，我们需要重写自定义对象的`equals`

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) {
        return true;
    }
    if (obj == null || getClass() != obj.getClass()) {
        return false;
    }
    Person person = (Person) obj;
    return age == person.age && Objects.equals(name, person.name);
}
```

## 获取对象的hashCode

```java
String str = new String("213");
System.out.println(Objects.hashCode(str));
```



## 比较两个对象

`compare()` 方法用于比较两个对象，通常用于自定义排序。它需要一个[比较器 (Comparator) ](https://tobebetterjavaer.com/basic-extra-meal/comparable-omparator.html)作为参数。如果比较器为 null，则使用自然顺序。以下是一个 `compare()` 方法的示例：

```java
class ObjectsCompareDemo {
    public static void main(String[] args) {
        PersonCompare p1 = new PersonCompare("itwanger", 16);
        PersonCompare p2 = new PersonCompare("chenqingyang", 13);
        int res = Objects.compare(p1, p2, new Comparator<Person>() {
            @Override
            public int compare(Person p1, Person p2) {
                return p1.age-p2.age;
            }
        });
        System.out.println(res);//3
        
        Comparator<Person> comparator = Comparator.comparingInt(person -> person.age);
        int res1 = Objects.compare(p1,p2,comparator);
        System.out.println(res1);//1
    }
}

class PersonCompare {
    String name;
    int age;

    PersonCompare(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

## 比较两个数组

`deepEquals()` 用于比较两个数组类型的对象，当对象是非数组的话，行为和 `equals()` 一致。

```
int[] array1 = {1, 2, 3};
int[] array2 = {1, 2, 3};
int[] array3 = {1, 2, 4};

System.out.println(Objects.deepEquals(array1, array2)); // 输出：true（因为 array1 和 array2 的内容相同）
System.out.println(Objects.deepEquals(array1, array3)); // 输出：false（因为 array1 和 array3 的内容不同）

// 对于非数组对象，deepEquals() 的行为与 equals() 相同
String string1 = "hello";
String string2 = "hello";
String string3 = "world";

System.out.println(Objects.deepEquals(string1, string2)); // 输出：true（因为 string1 和 string2 相同）
System.out.println(Objects.deepEquals(string1, string3)); // 输出：false（因为 string1 和 string3 不同）
```

再来个二维数组的：

```
String[][] nestedArray1 = {{"A", "B"}, {"C", "D"}};
String[][] nestedArray2 = {{"A", "B"}, {"C", "D"}};
String[][] nestedArray3 = {{"A", "B"}, {"C", "E"}};

System.out.println(Objects.deepEquals(nestedArray1, nestedArray2)); // 输出：true (因为嵌套数组元素相同)
System.out.println(Objects.deepEquals(nestedArray1, nestedArray3)); // 输出：false (因为嵌套数组元素不同)
```

# Collections工具类

## 排序操作

- `reverse(List list)`：反转顺序
- `shuffle(List list)`：洗牌，将顺序打乱
- `sort(List list)`：自然升序
- `sort(List list, Comparator c)`：按照自定义的比较器排序
- `swap(List list, int i, int j)`：将 i 和 j 位置的元素交换位置

## 查找操作

- `binarySearch(List list, Object key)`：二分查找法，前提是 List 已经排序过了
- `max(Collection coll)`：返回最大元素
- `max(Collection coll, Comparator comp)`：根据自定义比较器，返回最大元素
- `min(Collection coll)`：返回最小元素
- `min(Collection coll, Comparator comp)`：根据自定义比较器，返回最小元素
- `fill(List list, Object obj)`：使用指定对象填充
- `frequency(Collection c, Object o)`：返回指定对象出现的次数

## CollectionUtils：Spring 和 Apache 都有提供的集合工具类

对集合操作，除了前面说的 JDK 原生 `Collections` 工具类，`CollectionUtils`工具类也很常用。

目前比较主流的是`Spring`的`org.springframework.util`包下的 CollectionUtils 工具类和`Apache`的`org.apache.commons.collections`包下的 CollectionUtils 工具类。

### 集合判空

通过 CollectionUtils 工具类的`isEmpty`方法可以轻松判断集合是否为空，`isNotEmpty`方法判断集合不为空。

```java
List<Integer> list = new ArrayList<>();
list.add(2);
list.add(1);
list.add(3);

if (CollectionUtils.isEmpty(list)) {
    System.out.println("集合为空");
}

if (CollectionUtils.isNotEmpty(list)) {
    System.out.println("集合不为空");
}
```

### 两个集合之间的操作

有时候我们需要对已有的两个集合进行操作，比如取交集或者并集等。

```java
List<Integer> list = new ArrayList<>();
list.add(2);
list.add(1);
list.add(3);

List<Integer> list2 = new ArrayList<>();
list2.add(2);
list2.add(4);

//获取并集
Collection<Integer> unionList = CollectionUtils.union(list, list2);
System.out.println(unionList);

//获取交集
Collection<Integer> intersectionList = CollectionUtils.intersection(list, list2);
System.out.println(intersectionList);

//获取交集的补集
Collection<Integer> disjunctionList = CollectionUtils.disjunction(list, list2);
System.out.println(disjunctionList);

//获取差集
Collection<Integer> subtractList = CollectionUtils.subtract(list, list2);
System.out.println(subtractList);
```







# Hutool工具类

Hutool 的设计思想是尽量减少重复的定义，让项目中的 util 包尽量少。一个好的轮子可以在很大程度上避免“复制粘贴”，从而节省我们开发人员对项目中公用类库和公用工具方法的封装时间。同时呢，成熟的开源库也可以最大限度的避免封装不完善带来的 bug。

官方文档[Hutool — 🍬A set of tools that keep Java sweet.](https://www.hutool.cn/)



## 类型转换

类型转换在 Java 开发中很常见，尤其是从 HttpRequest 中获取参数的时候，前端传递的是整型，但后端只能先获取到字符串，然后再调用 `parseXXX()` 方法进行转换，还要加上判空，很繁琐。

Hutool 的 Convert 类可以简化这个操作，可以将任意可能的类型转换为指定类型，同时第二个参数 defaultValue 可用于在转换失败时返回一个默认值。

```java
		String param = "10";
        int param_int = Convert.toInt(param,10);
        //第二个参数 defaultValue 可用于在转换失败时返回一个默

        String param1 = "2001.9.12";
        Date date = Convert.toDate(param1);

        String param2 = "你好";
        String unicode = Convert.strToUnicode(param2);
```

## 日期时间

```java
		Date date = DateUtil.date();
        //DateUtil.date() 返回的其实是 DateTime，它继承自 Date 对象，重写了 toString() 方法，返回 yyyy-MM-dd HH:mm:ss 格式的字符串

        //字符串转Date，这里的转换可以识别很多格式，包括中文
        String dateStr = "2020-09-29";
        Date date1 = DateUtil.parse(dateStr);

        //格式化时间差
        String dateStr1 = "2020-09-29 22:33:23";
        Date date2 = DateUtil.parse(dateStr1);
        String dateStr2 = "2020-10-01 23:34:27";
        Date date3 = DateUtil.parse(dateStr2);
        long betweenDay = DateUtil.between(date2, date3, DateUnit.MS);
        String formatBetween = DateUtil.formatBetween(betweenDay, BetweenFormater.Level.SECOND);// 输出：2天1小时1分4秒

        //星座&属相
        String zodiac = DateUtil.getZodiac(Month.MAY.getValue(), 21);//双子座
        String chineseZondiac = DateUtil.getChineseZodiac(2000);//龙
        System.out.println(chineseZondiac);
```



## IO流相关

Hutool 封装了流操作工具类 IoUtil、文件读写操作工具类 FileUtil、文件类型判断工具类 FileTypeUtil 等等。



在 IO 操作中，文件的操作相对来说是比较复杂的，但使用频率也很高，几乎所有的项目中都躺着一个叫 FileUtil 或者 FileUtils 的工具类。Hutool 的 FileUtil 类包含以下几类操作：

- 文件操作：包括文件目录的新建、删除、复制、移动、改名等
- 文件判断：判断文件或目录是否非空，是否为目录，是否为文件等等
- 绝对路径：针对 ClassPath 中的文件转换为绝对路径文件
- 文件名：主文件名，扩展名的获取
- 读操作：包括 getReader、readXXX 操作
- 写操作：包括 getWriter、writeXXX 操作

## 字符串工具

Hutool 封装的字符串工具类 StrUtil 和 Apache Commons Lang 包中的 StringUtils 类似

```java
String s = "{} is {}";
String s_full = StrUtil.format(s,"apple","nice");//apple is nice
```

## 压缩工具

在 Java 中，对文件、文件夹打包压缩是一件很繁琐的事情，Hutool 封装的 ZipUtil 针对 java.util.zip 包做了优化，可以使用一个方法搞定压缩和解压，并且自动处理文件和目录的问题，不再需要用户判断，大大简化的压缩解压的复杂度。

```java
ZipUtil.zip("hutool", "hutool.zip");
File unzip = ZipUtil.unzip("hutool.zip", "hutoolzip");
```

## 身份证工具

Hutool 封装的 IdcardUtil 可以用来对身份证进行验证，支持大陆 15 位、18 位身份证，港澳台 10 位身份证。

```java
String ID_18 = "321083197812162119";
String ID_15 = "150102880730303";

boolean valid = IdcardUtil.isValidCard(ID_18);
boolean valid15 = IdcardUtil.isValidCard(ID_15);
```

## 扩展HashMap

Java 中的 HashMap 是强类型的，而 Hutool 封装的 Dict 对键的类型要求没那么严格。

```java
Dict dict = Dict.create()
        .set("age", 18)
        .set("name", "沉默王二")
        .set("birthday", DateTime.now());

int age = dict.getInt("age");
String name = dict.getStr("name");
```

## 控制台的打印

```java
		String s = "{} is {}";
        String s_full = StrUtil.format(s,"apple","nice");//apple is nice
        Console.log(s_full);
        Console.log("{} is nice", "banana");//banana is nice
        int []a = {1,2,43,5,0};
        Console.log(a);//[1, 2, 43, 5, 0]
```

## 字段验证

做 Web 开发的时候，后端通常需要对表单提交过来的数据进行验证。Hutool 封装的 Validator 可以进行很多有效的条件验证：

- 是不是邮箱
- 是不是 IP V4、V6
- 是不是电话号码
- 等等

```java
		String param = "sda@asd";
        Console.log(Validator.isEmail(param));
        String param1 = "20000102";
        Console.log(Validator.isBirthday(param1));
```



## 双向查找Map



`Guava` 中提供了一种特殊的 Map 结构，叫做 BiMap，实现了一种双向查找的功能，可以根据 key 查找 value，也可以根据 value 查找 key，Hutool 也提供这种 Map 结构。

```java
		BiMap<Integer,String> biMap = new BiMap<Integer, String>(new HashMap<Integer, String>());
        biMap.put(1,"a");
        biMap.put(2,"g");

        Console.log(biMap.get(1));//a
        Console.log(biMap.getKey("g"));//2
```



## 缓存工具



CacheUtil 是 Hutool 封装的创建缓存的快捷工具类，可以创建不同的缓存对象：

- FIFOCache：先入先出，元素不停的加入缓存直到缓存满为止，当缓存满时，清理过期缓存对象，清理后依旧满则删除先入的缓存。

```java
Cache<String, String> fifoCache = CacheUtil.newFIFOCache(3);
fifoCache.put("key1", "沉默王一");
fifoCache.put("key2", "沉默王二");
fifoCache.put("key3", "沉默王三");
fifoCache.put("key4", "沉默王四");

// 大小为 3，所以 key3 放入后 key1 被清除
String value1 = fifoCache.get("key1");
```



- LFUCache，最少使用，根据使用次数来判定对象是否被持续缓存，当缓存满时清理过期对象，清理后依旧满的情况下清除最少访问的对象并将其他对象的访问数减去这个最少访问数，以便新对象进入后可以公平计数。

```java
Cache<String, String> lfuCache = CacheUtil.newLFUCache(3);

lfuCache.put("key1", "沉默王一");
// 使用次数+1
lfuCache.get("key1");
lfuCache.put("key2", "沉默王二");
lfuCache.put("key3", "沉默王三");
lfuCache.put("key4", "沉默王四");

// 由于缓存容量只有 3，当加入第 4 个元素的时候，最少使用的将被移除（2,3被移除）
String value2 = lfuCache.get("key2");
String value3 = lfuCache.get("key3");
```



- LRUCache，最近最久未使用，根据使用时间来判定对象是否被持续缓存，当对象被访问时放入缓存，当缓存满了，最久未被使用的对象将被移除。

```java
Cache<String, String> lruCache = CacheUtil.newLRUCache(3);

lruCache.put("key1", "沉默王一");
lruCache.put("key2", "沉默王二");
lruCache.put("key3", "沉默王三");
// 使用时间近了
lruCache.get("key1");
lruCache.put("key4", "沉默王四");

// 由于缓存容量只有 3，当加入第 4 个元素的时候，最久使用的将被移除（2）
String value2 = lruCache.get("key2");
System.out.println(value2);
```



























