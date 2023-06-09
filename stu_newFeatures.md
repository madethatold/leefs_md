# 流

Java 8 新增的 Stream 是为了解放程序员操作集合（Collection）时的生产力，之所以能解放，很大一部分原因可以归功于同时出现的 Lambda 表达式——极大的提高了编程效率和程序可读性。

要想操作流，首先需要有一个数据源，可以是数组或者集合。每次操作都会返回一个新的流对象，方便进行链式操作，但原有的流对象会保持不变。

流的操作可以分为两种类型：

1）中间操作，可以有多个，每次返回一个新的流，可进行链式操作。

2）终端操作，只能有一个，每次执行完，这个流也就用光光了，无法执行下一个操作，因此只能放在最后。

## 创建流

如果是数组的话，可以使用 `Arrays.stream()` 或者 `Stream.of()` 创建流；如果是集合的话，可以直接使用 `.stream()` 方法创建流，因为该方法已经添加到 Collection 接口中。其实`Stream.of()`本质上也是调用的`.stream()`

```java
		String[] arr = new String[]{"武汉加油", "中国加油", "世界加油"};
        Stream<String> stream = Arrays.stream(arr);

        stream = Stream.of("武汉加油", "中国加油", "世界加油");

        List<String> list = new ArrayList<>();
        list.add("武汉加油");
        list.add("中国加油");
        list.add("世界加油");
        stream = list.stream();
```

## 操作流

### 过滤

通过 `filter()` 方法可以从流中筛选出我们想要的元素。

```
        List<String> list = new ArrayList<>();
        list.add("周杰伦");
        list.add("王力宏");
        list.add("陶喆");
        list.add("林俊杰");
        Stream<String> stream = list.stream().filter(element -> element.contains("王"));
        stream.forEach(System.out::println);
```

`filter()` 方法接收的是一个 Predicate（Java 8 新增的一个函数式接口，接受一个输入参数返回一个布尔值结果）类型的参数，因此，我们可以直接将一个 Lambda 表达式传递给该方法，比如说 `element -> element.contains("王")` 就是筛选出带有“王”的字符串。

`forEach()` 方法接收的是一个 Consumer（Java 8 新增的一个函数式接口，接受一个输入参数并且无返回的操作）类型的参数，`类名 :: 方法名`是 Java 8 引入的新语法，`System.out` 返回 PrintStream 类，println 方法你应该知道是打印的。

### 映射

如果想通过某种操作把一个流中的元素转化成新的流中的元素，可以使用 `map()` 方法

```java
		List<String> list = new ArrayList<>();
        list.add("周杰伦");
        list.add("王力宏");
        list.add("陶喆");
        list.add("林俊杰");
        Stream<Integer> stream = list.stream().map(String::length);
        stream.forEach(System.out::println);
//
3 3 2 3
```

`map()` 方法接收的是一个 Function（Java 8 新增的一个函数式接口，接受一个输入参数 T，返回一个结果 R）类型的参数，此时参数 为 String 类的 length 方法，也就是把 `Stream<String>` 的流转成一个 `Stream<Integer>` 的流。

### 匹配

Stream 类提供了三个方法可供进行元素匹配，它们分别是：

- `anyMatch()`，只要有一个元素匹配传入的条件，就返回 true。
- `allMatch()`，只有有一个元素不匹配传入的条件，就返回 false；如果全部匹配，则返回 true。
- `noneMatch()`，只要有一个元素匹配传入的条件，就返回 false；如果全部不匹配，则返回 true。

```java
public class MatchStreamDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("周杰伦");
        list.add("王力宏");
        list.add("陶喆");
        list.add("林俊杰");

        boolean  anyMatchFlag = list.stream().anyMatch(element -> element.contains("王"));
        boolean  allMatchFlag = list.stream().allMatch(element -> element.length() > 1);
        boolean  noneMatchFlag = list.stream().noneMatch(element -> element.endsWith("沉"));
        System.out.println(anyMatchFlag);
        System.out.println(allMatchFlag);
        System.out.println(noneMatchFlag);
    }
}
```

### 组合

`reduce()` 方法的主要作用是把 Stream 中的元素组合起来，它有两种用法：

- `Optional<T> reduce(BinaryOperator<T> accumulator)`

没有起始值，只有一个参数，就是运算规则，此时返回 Optional

- `T reduce(T identity, BinaryOperator<T> accumulator)`

有起始值，有运算规则，两个参数，此时返回的类型和起始值类型一致。

## 转换流

```java
		List<String> list = new ArrayList<>();
        list.add("周杰伦");
        list.add("王力宏");
        list.add("陶喆");
        list.add("林俊杰");

        String[] strArray = list.stream().toArray(String[]::new);
        System.out.println(Arrays.toString(strArray));//[周杰伦, 王力宏, 陶喆, 林俊杰]

        List<Integer> list1 = list.stream().map(String::length).collect(Collectors.toList());
        List<String> list2 = list.stream().collect(Collectors.toCollection(ArrayList::new));
        System.out.println(list1);//[3, 3, 2, 3]
        System.out.println(list2);//[周杰伦, 王力宏, 陶喆, 林俊杰]

        String str = list.stream().collect(Collectors.joining(", ")).toString();
        System.out.println(str);//周杰伦, 王力宏, 陶喆, 林俊杰
```



当我们需要把一个集合按照某种规则转成另外一个集合的时候，就可以配套使用 `map()` 方法和 `collect()` 方法。

```java
List<Integer> list1 = list.stream().map(String::length).collect(Collectors.toList());
```

# Optional



Optional 之所以可以解决 NPE 的问题，是因为它明确的告诉我们，不需要对它进行判空。

## 创建Optional对象

```java
Optional<String> empty = Optional.empty();
System.out.println(empty); // 输出：Optional.empty

Optional<String> opt = Optional.of("沉默王二");
System.out.println(opt); // 输出：Optional[沉默王二]

String name = null;
Optional<String> optOrNull = Optional.ofNullable(name);
System.out.println(optOrNull); // 输出：Optional.empty
```

## 判断值是否存在

可以通过方法 `isPresent()` 判断一个 Optional 对象是否存在，如果存在，该方法返回 true，否则返回 false——取代了 `obj != null` 的判断。

```java
Optional<String> opt = Optional.of("沉默王二");
System.out.println(opt.isPresent()); // 输出：true

Optional<String> optOrNull = Optional.ofNullable(null);
System.out.println(opt.isPresent()); // 输出：false
```

## 非空表达式

```java
Optional<String> optOrNull = Optional.ofNullable(null);
if (optOrNull.isPresent()) {
    System.out.println(optOrNull.get().length());
}

Optional<String> opt = Optional.of("沉默王二");
opt.ifPresent(str -> System.out.println(str.length()));

Optional<String> opt = Optional.of("沉默王二");
opt.ifPresent(str -> System.out.println(str.length()));
```

## 设置（获取）默认值

`orElse()` 方法用于返回包裹在 Optional 对象中的值，如果该值不为 null，则返回；否则返回默认值。该方法的参数类型和值得类型一致。

```java
String nullName = null;
String name = Optional.ofNullable(nullName).orElse("沉默王二");
System.out.println(name); // 输出：沉默王二
```

`orElseGet()` 方法与 `orElse()` 方法类似，但参数类型不同。如果 Optional 对象中的值为 null，则执行参数中的函数。

```java
String nullName = null;
String name = Optional.ofNullable(nullName).orElseGet(()->"沉默王二");
System.out.println(name); // 输出：沉默王二
```

## 获取值

直观从语义上来看，`get()` 方法才是最正宗的获取 Optional 对象值的方法，但很遗憾，该方法是有缺陷的，因为假如 Optional 对象的值为 null，该方法会抛出 NoSuchElementException 异常。这完全与我们使用 Optional 类的初衷相悖。

建议`orElseGet()` 方法获取 Optional 对象的值。

## 过滤值

```java
String password = "12345";
Optional<String> opt = Optional.ofNullable(password);
System.out.println(opt.filter(pwd -> pwd.length() > 6).isPresent());

Predicate<String> len6 = pwd -> pwd.length() > 6;
Predicate<String> len10 = pwd -> pwd.length() < 10;

password = "1234567";
opt = Optional.ofNullable(password);
boolean result = opt.filter(len6.and(len10)).isPresent();
System.out.println(result);
```

`filter()` 方法的参数类型为 Predicate，将一个 Lambda 表达式传递给该方法作为条件，如果表达式的结果为 false，则返回一个 EMPTY 的 Optional 对象，否则返回过滤后的 Optional 对象。

## 转换值

```java
		String password = "password";
        Optional<String>  opt = Optional.ofNullable(password);

        Predicate<String> len6 = pwd -> pwd.length() > 6;
        Predicate<String> len10 = pwd -> pwd.length() < 10;
        Predicate<String> eq = pwd -> pwd.equals("password");

        boolean result = opt.map(String::toLowerCase).filter(len6.and(len10 ).and(eq)).isPresent();
        System.out.println(result);
```

# Lambda表达式

可以通过 Lambda 表达式干很多事情，比如说

1）为变量赋值，示例如下：

```java
Runnable r = () -> { System.out.println("沉默王二"); };
r.run();
```



2）作为 return 结果，示例如下：

```java
static FileFilter getFilter(String ext)
{
    return (pathname) -> pathname.toString().endsWith(ext);
}
```



3）作为数组元素，示例如下：

```java
final PathMatcher matchers[] =
{
        (path) -> path.toString().endsWith("txt"),
        (path) -> path.toString().endsWith("java")
};
```



4）作为普通方法或者构造方法的参数，示例如下：

```java
new Thread(() -> System.out.println("沉默王二")).start();
```