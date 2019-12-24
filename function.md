# Function

**基于Stream的函数式编程**

```java
        //使用Stream.of(T... values)构建Stream 然后使用 filter forEach
		String[] array = new String[]{"1","2","3"};
        Stream.of(array).forEach(item -> System.out.println(item));
		
		// Stream IntStream LongStream 都是继承自 BaseStream 
```

