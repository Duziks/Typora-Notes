# Java Map 用法详解

java中map的用法

在Java中，`Map` 是一个非常核心的接口（位于 `java.util` 包），用于存储**键值对（Key-Value Pair）**。每一个键（Key）在 Map 中是唯一的，通过键可以快速找到对应的值（Value）。

以下是关于 Java `Map` 的详细用法指南，涵盖了常用实现类、基本操作、遍历方式以及 Java 8 的新特性。

------

### 1. 核心概念与常用实现类

`Map` 本身是一个接口，我们通常使用它的具体实现类。

| **实现类**          | **特点**                                                | **适用场景**                                  | **排序**        |
| ------------------- | ------------------------------------------------------- | --------------------------------------------- | --------------- |
| **`HashMap`**       | 最常用，基于哈希表。查找和插入性能极高（接近 $O(1)$）。 | 通用场景，不需要排序。                        | 无序            |
| **`LinkedHashMap`** | 继承自 HashMap，但在内部维护了一个双向链表。            | 需要保持**插入顺序**或访问顺序时。            | 按插入/访问顺序 |
| **`TreeMap`**       | 基于红黑树实现。                                        | 需要根据 Key 进行**自然排序**或自定义排序时。 | 按 Key 排序     |
| **`Hashtable`**     | 线程安全（旧版），性能较差。                            | **不推荐**。多线程请用 `ConcurrentHashMap`。  | 无序            |

------

### 2. 基本操作 (CRUD)

以下代码以 `HashMap` 为例演示基本增删改查：

Java

```
import java.util.HashMap;
import java.util.Map;

public class MapDemo {
    public static void main(String[] args) {
        // 创建 Map，指定 Key 和 Value 的类型
        Map<String, Integer> scores = new HashMap<>();

        // 1. 添加元素 (Put)
        scores.put("Alice", 90);
        scores.put("Bob", 85);
        scores.put("Charlie", 95);
        
        // 注意：如果 Key 已存在，put 会覆盖旧值，并返回旧值
        scores.put("Alice", 92); // Alice 现在是 92

        // 2. 获取元素 (Get)
        Integer aliceScore = scores.get("Alice");
        System.out.println("Alice's score: " + aliceScore);

        // 3. 判断是否存在 (Contains)
        boolean hasBob = scores.containsKey("Bob");   // true
        boolean has100 = scores.containsValue(100);   // false

        // 4. 删除元素 (Remove)
        scores.remove("Charlie"); // 删除 Key 为 Charlie 的键值对

        // 5. 获取大小 (Size)
        System.out.println("Size: " + scores.size()); // 输出 2
    }
}
```

------

### 3. Map 的四种遍历方式

遍历 Map 是面试和开发中最高频的操作。

#### 方式一：通过 `forEach` (Java 8+)

最简洁、最现代的写法。

Java

```
scores.forEach((key, value) -> {
    System.out.println("Key: " + key + ", Value: " + value);
});
```

#### 方式二：通过 `entrySet` 遍历 (传统方式)

如果是非 Java 8 环境，或者需要在循环中删除元素，这是效率最高的方式。

Java

```
for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    String key = entry.getKey();
    Integer value = entry.getValue();
    System.out.println(key + " : " + value);
}
```

#### 方式三：通过 `keySet` 遍历 (效率较低)

如果只需要 Key，可以用这个；如果需要 Value，通过 Key 再去 `get()` 会导致二次查找（性能不如 entrySet）。

Java

```
for (String key : scores.keySet()) {
    // scores.get(key) // 这里会产生额外的哈希查找
    System.out.println("Key: " + key);
}
```

#### 方式四：通过 `values` 遍历

当你只需要值，不关心键时使用。

Java

```
for (Integer value : scores.values()) {
    System.out.println("Value: " + value);
}
```

------

### 4. Java 8+ 的高级用法

Java 8 引入了许多简化代码的方法，极大提高了开发效率。

- getOrDefault(key, defaultValue)

  防止空指针，如果 Key 不存在，返回默认值。

  Java

  ```
  // 如果 David 不存在，返回 0，而不是 null
  int score = scores.getOrDefault("David", 0); 
  ```

- putIfAbsent(key, value)

  只有当 Key 不存在时才放入数据（不会覆盖旧值）。

  Java

  ```
  scores.putIfAbsent("Alice", 100); // Alice 已存在，不操作
  ```

- computeIfAbsent(key, function)

  非常适合构建缓存或一对多结构（Map<Key, List>）。 如果 Key 不存在，则计算一个值放入 Map 并返回。

  Java

  ```
  Map<String, List<String>> groups = new HashMap<>();
  // 如果 "ClassA" 不存在，创建新 ArrayList 放入，然后添加 "Student1"
  groups.computeIfAbsent("ClassA", k -> new ArrayList<>()).add("Student1");
  ```

------

### 5. 常见问题与注意事项

1. **Key 的 Null 值：**
   - `HashMap` 和 `LinkedHashMap` **允许** Key 为 `null`（只能有一个）和 Value 为 `null`。
   - `TreeMap` **不允许** Key 为 `null`。
   - `ConcurrentHashMap` **不允许** Key 或 Value 为 `null`。
2. **作为 Key 的对象：**
   - 如果你使用自定义对象作为 Key，**必须**重写该类的 `equals()` 和 `hashCode()` 方法。否则，即使两个对象内容相同，Map 也会认为它们是不同的 Key。
3. **线程安全：**
   - `HashMap` 不是线程安全的。
   - 如果在多线程环境下使用，请使用 `ConcurrentHashMap`。
   - *不要*使用 `Collections.synchronizedMap(new HashMap<>())`，除非并发度很低，因为它的性能通常不如 `ConcurrentHashMap`。