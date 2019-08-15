## JVM 常用参数

### 设置永久代内存大小（方法区）
永久代内存太小，会报java.lang.OutOfMemoryError: PermGen space
```
# Set initial PermGen Size.
-XX:PermSize=64M
# Set the maximum PermGen Size.
-XX:MaxPermSize=256M
```



