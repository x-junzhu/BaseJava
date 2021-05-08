# Java8新特性(20210507)



### 1 Stream流是什么

是一系列的数据项, 并不是一种数据结构

### 2 怎么使用Stream流

> 2.1 使用对象

集合, 包括list、set、map

> 2.2 如何创建

+ 创建Stream方式一: 通过集合

```java
@Test
public void test1(){
    List<Employee> employeeList = EmployeeData.getEmployees();
    // 返回一个顺序流
    employeeList.stream();
    // 返回一个并行流
    employeeList.parallelStream();
}
```

+ 创建Stream方式二：通过数组

```java
@Test
public void test2(){
    int[] arr = new int[]{1, 2, 3, 4, 5};
    IntStream intStream = Arrays.stream(arr);
}
```

+ 创建Stream方式三：通过Stream的of()

```java
@Test
public void test3(){
    Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);
}
```

+ 创建Stream方式四：创建无限流

```java
@Test
public void test3(){
    // 遍历前10个偶数
    Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out:: println);
}
```

> 2.3 Stream使用流程

① Stream的实例化

② 一系列的中间操作（过滤、映射、...)

③ 终止操作

注：

* Stream 自己不会存储元素。
* Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。
* Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

>  常见用法1：筛选与切片

```java
@Test
public void test1(){
    List<Employee> employeeList = EmployeeData.getEmployees();

    employeeList.stream().filter(e -> e.getSalary() > 7000).forEach(System.out:: println);

    System.out.println();
    // limit(n)——截断流, 使其元素不超过给定数量
    employeeList.stream().limit(3).forEach(System.out:: println);

    System.out.println();
    // skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
    employeeList.stream().skip(3).forEach(System.out:: println);

    System.out.println();
    employeeList.add(new Employee(1010,"刘强东",40,8000));
    employeeList.add(new Employee(1010,"刘强东",40,8000));
    // distinct()-- 去重
    employeeList.stream().distinct().forEach(System.out:: println);
}
```

> 常见用法2：映射

```java
@Test
public void test2(){
    List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
    // map(Function f)——接收一个函数作为参数，将元素转换成其他形式或提取信息，
    // 该函数会被应用到每个元素上，并将其映射成一个新的元素
    list.stream().map(s -> s.toUpperCase()).forEach(System.out:: println);
    System.out.println();
    // 练习1：获取员工姓名长度大于3的员工的姓名
    List<Employee> employeeList = EmployeeData.getEmployees();
    employeeList.stream().map(e -> e.getName()).filter(name -> name.length() > 3).forEach(System.out:: println);

    System.out.println();
    employeeList.stream().filter(e -> e.getName().length() > 3).forEach(System.out:: println);
}
```

> 常见用法3：排序

```java
@Test
public void test3(){

    // java与排序有关的：①实现comparable接口, ②new Comparator<>()类
    List<Integer> list = Arrays.asList(12, 43, 65, 34, 87, 0, -98, 7);
    // 自然排序
    list.stream().sorted().forEach(System.out:: println);

    List<Employee> employeeList = EmployeeData.getEmployees();
    System.out.println();
    // 先按照年龄排序, 年龄相同按着工资排序
    employeeList.stream().sorted((e1, e2) -> {
        int sortByAge = Integer.compare(e1.getAge(), e2.getAge());

        if(sortByAge != 0){
            return sortByAge;
        }else{
            return Double.compare(e1.getSalary(), e2.getSalary());
        }

    }).forEach(System.out:: println);
}
```

> 常见用法4：匹配与查找

```java
@Test
public void test1(){
    List<Employee> employeeList = EmployeeData.getEmployees();

    // allMatch(Predicate p)——检查是否匹配所有元素
    boolean allMatch = employeeList.stream().allMatch(e -> e.getAge() > 18);
    System.out.println(allMatch);

    // anyMatch(Predicate p)——检查是否至少匹配一个元素
    boolean anyMatch = employeeList.stream().anyMatch(e -> e.getSalary() > 10000);
    System.out.println(anyMatch);

    // noneMatch(Predicate p)——检查是否没有匹配的元素
    boolean noneMatch = employeeList.stream().noneMatch(e -> e.getName().startsWith("雷"));
    System.out.println(noneMatch);

    // findFirst——返回第一个元素
    Optional<Employee> optionalEmployee = employeeList.stream().findFirst();
    System.out.println(optionalEmployee);

    // findAny——返回当前流中的任意元素
    Optional<Employee> any = employeeList.parallelStream().findAny();
    System.out.println(any);
    
    
    List<Employee> employeeList = EmployeeData.getEmployees();

    // count——返回流中元素的总个数
    long count = employeeList.stream().filter(e -> e.getSalary() > 7000).count();
    System.out.println(count);

    // max(Comparator c)——返回流中最大值
    // 练习：返回最高的工资
    Optional<Employee> employee = employeeList.stream().max((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
    System.out.println(employee);
}
```

> 常见用法5：归约

```java
@Test
public void test4(){
    // collect(Collector c)——将流转换为其他形式。接收一个 Collector接口的实现，
    // 用于给Stream中元素做汇总的方法
    // 练习1：查找工资大于6000的员工，结果返回为一个List或Set

    List<Employee> employeeList = EmployeeData.getEmployees();
    List<Employee> employees = employeeList.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toList());
    employees.forEach(System.out:: println);

    Set<Employee> employeeSet = employeeList.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toSet());
    employeeSet.forEach(System.out:: println);
}
```

