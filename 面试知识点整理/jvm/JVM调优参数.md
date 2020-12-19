# JVM调优参数

- -Xmx：堆内存最大值

- -Xms：堆内存最小值

- -XX:MaxPermSize:永久代内存上限

- CMS采用标记清除算法，垃圾回收后会产生空间碎片，可以通过参数优化

  - ```objectivec
    -XX:UserCMSCompactAtFullCollection #开启碎片整理（默认是开的）
    ```

  - ```objectivec
    -XX:CMSFullGCsBeforeCompaction #执行多少次不压缩的Full GC之后，跟着来一次压缩的Full GC
    ```

- -XX:SurvivorRatio    Eden区与Survivor区的大小比值
- -XX:+DisableExplicitGC    关闭System.gc()
- 