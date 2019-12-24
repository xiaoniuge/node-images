# ThreadLocal 线程缓存

```JAVA
    private final static ThreadLocal<String> userHolder = new ThreadLocal<>();

    public static String getCurrentUser(){
        return userHolder.get();
    }

    public static void add(String user){
        userHolder.set(user);
    }

    public static void remove(){
        userHolder.remove();
    }
```

