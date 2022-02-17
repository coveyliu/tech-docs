



# 基本用法

## 作用

`Stream API` 提供了强大的数据处理功能



在我看来，`Stream API` 的优点有：

1. 流式调用
2. 不但提供顺序流，还提供并行流，充分利用多线程优势





## 分类

顺序流 和 并行流





## 如何获取流



1. `Collection` 接口获取。（Collection 可以获取 `parallelStream`）
   - ==*关于并行流*==
     - 使用的是多线程，线程数默认是系统的 `CPU` 核心数。进一步说：内部使用的是 `fork/join` 框架，处理数据时，会将数据拆分成一块一块的，最后将处理结果汇总
2. `Arrays` 提供一些静态方法可以将数组转为流
3. `Stream` 接口提供了一些静态方法，获取流



==Collection==

```java
public interface Collection<E> extends Iterable<E> {
    default Stream<E> stream() {
        return StreamSupport.stream(this.spliterator(), false);
    }
    default Stream<E> parallelStream() {
        return StreamSupport.stream(this.spliterator(), true);
    }
}
```



==Arrays==，（这只是一个工具类）

```java
public class Arrays {
    
    public static DoubleStream stream(double[] var0);
    public static IntStream stream(int[] var0);
    public static LongStream stream(long[] var0);
    public static <T> Stream<T> stream(T[] var0);

}
```



==Stream==

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
    
    
    static <T> Stream<T> empty() {
        return StreamSupport.stream(Spliterators.emptySpliterator(), false);
    }
    static <T> Stream<T> of(T... var0) {
        return Arrays.stream(var0);
    }
    
    // 生成无限流
    static <T> Stream<T> generate(Supplier<T> s) {
        Objects.requireNonNull(var0);
        return StreamSupport.stream(new OfRef(9223372036854775807L, var0), false);
    }
    
    // 生成无线流。第一个元素为seed, 第二个为 f(seed), 第三个为 f(f(seed))
    static <T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) {
       // ...
    }
}
```



==Demo==

```java
public void main(String[] args){
    // 生成 10 个随机数
    Stream.generate(() -> Math.random()).limit(10).foreach(System.in::println);
    
    // 从 2 开始生成 10 个偶数
    Stream.iterate(2, t -> t+2).limit(10).foreach(System.in::println);
}
```



### 关于并行流











## 中间操作



例子：

```java
 Stream.generate(() -> Math.random()).
     limit(10).
     filter(t -> t>9).
     collect(Collectors.toList());
```



==中间操作只是在构建流程，向上述例子，只有在执行 collect 的时候，才会执行 limit, filter 中间操作==

==中间操作，返回的都是 Stream==

==中间操作：==

`filter`, `map`, `distinct`, `sorted`, `skip`, `limit`, `peek`, `mapToLong`, `mapToInt`, `mapToDouble`, `flatMap`

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
    
    Stream<T> filter(Predicate<? super T> predicate);
    <R> Stream<R> map(Function<? super T, ? extends R> mapper);
    IntStream mapToInt(ToIntFunction<? super T> mapper);

}
```



## 终端操作

中间操作，真正执行数据处理的工作



- `max/min`

- `count`

- `allMatch/anyMatch/noneMatch`

- `findFirst/findAny`

- `foreach`

- `toArray`

  - `Object[] toArray();`
  - `<A> A[] toArray(IntFunction<A[]> generator);` 能转换

- `reduce`

  - 比 `max/min/count` 更通用，==会将流中元素归约为一个值==

  

```java
public interface Stream<T> extends BaseStream<T, Stream<T>> {
    
    
    
    Optional<T> max(Comparator<? super T> comparator);
    Optional<T> min(Comparator<? super T> comparator);
    
    Optional<T> reduce(BinaryOperator<T> accumulator);
    T reduce(T identity, BinaryOperator<T> accumulator);
    <U> U reduce(U identity,
                 BiFunction<U, ? super T, U> accumulator,
                 BinaryOperator<U> combiner);

}
```









# 强大的收集器 collect 操作



将收集器分类后，能更了解 `JDK` 提供的收集器的作用



## 作用

`Stream#collect(Collector c)` 是流的终端操作，对流中元素的处理还是由收集器决定

比如：

- `Stream#collect(Collectors.toList())`就是往 `list` 中添加元素
- `Stream#collect(Collectors.toMap())` 会将流中的元素放进 `map` 中
- `Stream#collect(Collectors.groupingBy())` 会将流中的元素分组



## 分类

**两类：**

1. ==容器收集器==
   - 见名知意，流中元素最终放在容器里面
2. ==字符串收集器==
   - 流中的元素，最终会放在一个字符串里面



## 容器收集器



获取收集器 `Collector` 可以通过工具类 `Collectors`



==注意==：

1. `toMap` 是假定 key 是不重复的，如果重复会报错

2. 可以使用带有 `mergeFunction` 的方法处理

   比如：

   ```java
   // 这里使用新值
   Map<String,Integer> strLenMap = Stream.of("abc","hello","abc").
       collect(
       Collectors.toMap(Fuction.identity(), 
                        t-> t.length(), 
                        (oldValue, value) -> value))
   ```



`Collectors` 工具类

```java
public final class Collectors {
    
    // 1. toList()
    public static <T> Collector<T, ?, List<T>> toList() {
        return new CollectorImpl<>((Supplier<List<T>>) ArrayList::new, List::add,
                                   (left, right) -> { left.addAll(right); return left; },
                                   CH_ID);
    }
    
    // 2. toSet()
     public static <T> Collector<T, ?, Set<T>> toSet() {
        // ...
    }
    
    // 3. toCollection(collectionFactory)
    // 更通用， 可以指定使用的集合
    // Collectors.toCollection(LinkedHashSet::new)
    // Collectors.toCollection(LinkedList::new)
    public static <T, C extends Collection<T>> Collector<T, ?, C> toCollection(Supplier<C> collectionFactory) {
        return new CollectorImpl<>(collectionFactory, Collection<T>::add,
                                   (r1, r2) -> { r1.addAll(r2); return r1; },
                                   CH_ID);
    }
    
    
    // 4.1. toMap() 
    public static <T, K, U> Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper){
        // ...
    }
    
    // 4.2. toMap() 2, 这个方法接受一个 mergeFunction
    public static <T, K, U>
    Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                    Function<? super T, ? extends U> valueMapper,
                                    BinaryOperator<U> mergeFunction){
        
        // ...
    }
    
    
    // 4.3. toMap() 3 这个方法接受的参数更多
    public static <T, K, U, M extends Map<K, U>>
    Collector<T, ?, M> toMap(Function<? super T, ? extends K> keyMapper,
                                Function<? super T, ? extends U> valueMapper,
                                BinaryOperator<U> mergeFunction,
                                Supplier<M> mapSupplier){
        // ...
    }
    
    // 5. toConcurrentHashMap()
    public static <T, K, U>
    Collector<T, ?, ConcurrentMap<K,U>> toConcurrentMap(Function<? super T, ? extends K> keyMapper,
                                                        Function<? super T, ? extends U> valueMapper)
    
}
```



## 分组收集器



==分组之后，一定会是一个 map==，`Map<String, &>` 至于 `value` 是什么，取决于下游收集器（分组的元素如何处理）

分组收集器有下游收集器，默认`Collections.toList()`， 将分组后的元素直接放进`list` 中



### 参数

```java
public final class Collectors {
    
    
    // 一个参数
    public static <T, K> Collector<T, ?, Map<K, List<T>>>
    groupingBy(Function<? super T, ? extends K> classifier) {
        return groupingBy(classifier, toList());
    }
    
    //  两个参数。（接受下游收集器）
    public static <T, K, A, D> Collector<T, ?, Map<K, D>> 
        groupingBy(Function<? super T, ? extends K> classifier,
                         Collector<? super T, A, D> downstream) {
        return groupingBy(classifier, HashMap::new, downstream);
    }
    
    // 三个参数, 观察上述两个方法，默认的 mapFactory 是 HashMap::new
     public static <T, K, D, A, M extends Map<K, D>>
    Collector<T, ?, M> groupingBy(Function<? super T, ? extends K> classifier,
                                  Supplier<M> mapFactory,
                                  Collector<? super T, A, D> downstream) {
         // ...
     }
}
```





### 下游收集器



==下游收集器有很多，最重要的是 `Collectors.collectingAndThen()`==



可以用作下游收集器的是：

```java
public final class Collectors {
    
    // 计数
     public static <T> Collector<T, ?, Long> counting() {
        return reducing(0L, e -> 1L, Long::sum);
    }
    
    // 最大值
     public static <T> Collector<T, ?, Optional<T>> maxBy(Comparator<? super T> comparator) {
        return reducing(BinaryOperator.maxBy(comparator));
    }
    
    // 最小值
    public static <T> Collector<T, ?, Optional<T>> minBy(Comparator<? super T> comparator) {
        return reducing(BinaryOperator.minBy(comparator));
    }
    
    // 平均值
     public static <T> Collector<T, ?, Double> averagingDouble(ToDoubleFunction<? super T> mapper) {
       // ...
    }
    // 平均值
    public static <T> Collector<T, ?, Double> averagingInt(ToIntFunction<? super T> mapper) {
        // ...
    }
    
    // 平均值
    public static <T> Collector<T, ?, Double> averagingLong(ToLongFunction<? super T> mapper) {
        // ...
    }
}
```



```java
// demo
public static void main(String[] args){
    // 求每个班级的平均分
    List<Student> lists = Stream.of(...);
    lists.stream().groupBy(Collectors.groupBy(Student::grade, Collectors.averagingDoble(Student::score)))
}
static class Student{
    // 分数
    private double score;
    // 班级
    private String grade;
    // 名字
    private String name;
}
```



#### `Collectors.collectingAndThen()`



==利用这个下游收集器，可以在分组之后，再将每组的元素进行处理。比如：过滤，转换，排序==



```java
public final class Collectors {
    
    // 就只有这一种形式，而且参数也很简单
    public static<T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream,
                                                                Function<R,RR> finisher) {
        // ...
    }
}
 
```



### true 或 false 分组



`Collectors.partitioningBy()` 也是一个分组收集器，只不过 `Map` 的 key 是 `Boolean`



```java
public final class Collectors {
   
    
    public static <T>
    Collector<T, ?, Map<Boolean, List<T>>> partitioningBy(Predicate<? super T> predicate) {
        return partitioningBy(predicate, toList());
    }
    
    // 这个接受一个下游收集器
    public static <T, D, A>
    Collector<T, ?, Map<Boolean, D>> partitioningBy(Predicate<? super T> predicate,
                                                    Collector<? super T, A, D> downstream) {
        // ...
    }
}
```



```java
// demo
public static void main(String[] args){
    // 分数是否大于 60，对学生分组
    Stream.of(...).collect(Collectors.groupingBy(Collectors.partitioningBy(s -> s.getScore >= 60)));
}
static class Student{
    // 分数
    private double score;
    // 班级
    private String grade;
    // 名字
    private String name;
}
```



### 多级分组

`Collectors.groupBy()`, `Collectos.partitioningBy()`

1. 上述两个方法都可以接受一个下游收集器，表示对分组结果处理

2. 上述两个方法都返回一个收集器，可以作为分组的下游收集器，达到多级分组的作用

   

```java
public class T01_分组 {

    // demo
    public static void main(String[] args){
        Student s1 = new Student(1.2, "1", "张三");
        Student s2 = new Student(2.2, "2", "李四");
        // 按照班级分组后，再按照分数是否大于 60 分组
        Map<String, Map<Boolean, List<Student>>> collect = Stream.of(s1, s2)
                .collect(Collectors.groupingBy(Student::getGrade, Collectors.partitioningBy(s -> s.score >= 60)));
    }
    @Data
    @AllArgsConstructor
    static class Student{
        // 分数
        private double score;
        // 班级
        private String grade;
        // 名字
        private String name;
    }
}
```





