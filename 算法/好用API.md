```java
// 使用stream API实现list to array
list.stream().mapToInt(Integer::valueOf).toArray()
// 使用stream API实现 array to list 
Arrays.stream(arr2).boxed().collect(Collectors.toList());
```

```java
Collections.reverse()// 翻转
```

