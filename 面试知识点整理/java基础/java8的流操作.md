# java8的流操作

​	https://www.cnblogs.com/gc65/p/10611533.html	

​	在对流进行处理时，不同的流操作以级联的方式形成处理流水线。一个流水线由一个源（source），0 到多个中间操作（intermediate operation）和一个终结操作（terminal operation）完成。

下面列出了常用的流中间操作：

- map：通过一个 Function 把一个元素类型为 T 的流转换成元素类型为 R 的流。
- flatMap：通过一个 Function 把一个元素类型为 T 的流中的每个元素转换成一个元素类型为 R 的流，再把这些转换之后的流合并。
- filter：过滤流中的元素，只保留满足由 Predicate 所指定的条件的元素。
- distinct：使用 equals 方法来删除流中的重复元素。
- limit：截断流使其最多只包含指定数量的元素。
- skip：返回一个新的流，并跳过原始流中的前 N 个元素。
- sorted：对流进行排序。
- peek：返回的流与原始流相同。当原始流中的元素被消费时，会首先调用 peek 方法中指定的 Consumer 实现对元素进行处理。
- dropWhile：从原始流起始位置开始删除满足指定 Predicate 的元素，直到遇到第一个不满足 Predicate 的元素。
- takeWhile：从原始流起始位置开始保留满足指定 Predicate 的元素，直到遇到第一个不满足 Predicate 的元素	



- forEach 和 forEachOrdered 对流中的每个元素执行由 Consumer 给定的实现。在使用 forEach 时，并没有确定的处理元素的顺序；forEachOrdered 则按照流的相遇顺序来处理元素，如果流有确定的相遇顺序的话。
- reduce进行递归计算
- collect生成新的数据结构



#### 创建stream的四种方式

- 通过Collection系列集合提供的stream（）或者parallelStream（）
- 通过Arrays中的静态方法 Arrays.stream()
- 通过stream类的静态方法of()

```
Stream<String> stream3 = Stream.of("aa","bb","cc");
```

- 创建无限流

  ```java
  Stream<Integer> stream4 = Stream.iterate(0, (x) -> x + 2);
  ```

#### 流的中间操作

- 筛选与切片

  - ```
    filter：接收流中的元素，从流中排除某些元素
    ```

  - ```
    limit：截断流，使元素不超过给定数量
    ```

  - ```
    skip(n): 跳过前n个元素，返回剩余的元素，若流中元素不足n个，则返回空流
    ```

  - ```
    distinct：筛选，通过流所生成元素的hashCode和equals去除重复元素
    ```

- 映射

  - ```
     map:接收lambda，将元素转换成其它形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
    ```

  - ```
    flatMap：接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连城一个流
    ```

- 排序

  - ```
    sorted():自然排序(Comparable)
    ```

  - ```
    sorted(Comparator com):定制排序(Comparator)
    ```

- 内部迭代

  - ```
    迭代操作部由Stream API完成
    ```