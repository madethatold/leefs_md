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

