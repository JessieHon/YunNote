# 自定义AOP实现

- 定义实现注解@Permission
  - ![image-20201215101822810](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201215101822810.png)
  - 主要是两个注解
    - @Target（使用范围）
    - @Retentation（生命周期）
- 将注解根据@Target定义的使用范围（这里是方法上），放到相应的位置上
  - ![image-20201215102002537](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201215102002537.png)
- AOP配置
  - ![image-20201215104040704](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201215104040704.png)
  - 主要注解
    - @Aspect
    - @Around
  - 通过proceedingJoinPoint获取方法参数

自定义切点实现

- ![image-20201215113028493](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201215113028493.png)