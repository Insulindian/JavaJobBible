# JAVA SE

## 反射

- 反射得到类的 class 对象,代表这个类本身. 每个类在 jvm 都仅有一个 class 对象,通过它可以获得类的接口、方法、属性等信息,可以调用方法
  可以用于

1. 框架开发 spring
2. 动态代理 (通过反射才能知道代理对象的签名,方法)
3. 注解处理

### 核心 API

Class 类：所有反射操作的入口点

```java
// 获取Class对象的三种方式
Class<?> cls1 = Class.forName("java.lang.String");
Class<?> cls2 = String.class;
Class<?> cls3 = "Hello".getClass();
```

Constructor 类：用于创建对象

```java
Constructor<?> constructor = cls.getConstructor(String.class);
Object obj = constructor.newInstance("测试");
```

Method 类：用于调用方法

```java
Method method = cls.getMethod("substring", int.class);
Object result = method.invoke(obj, 2);
```

Field 类：用于访问字段

```java
Field field = cls.getDeclaredField("value");
field.setAccessible(true); // 访问私有字段
Object value = field.get(obj);
```

## 动态代理

允许在运行时创建实现了一组给定接口的代理类，将方法调用转发到实际目标对象，同时可以在方法执行前后添加额外逻辑。

### JDK 动态代理

**基于接口实现，只能代理实现了接口的类。**

相比于静态代理

- 避免代码重复
  静态代理每个接口都需要手动实现所有方法
  若有 100 个方法，静态代理需要手写 100 段重复代码
  动态代理只需要一个 InvocationHandler 处理所有方法
- 运行时动态创建
  动态代理在运行时生成代理类，编译时不需要存在
  可以根据运行时条件决定是否代理及如何代理

```java
// 接口
public interface UserService {
    void save(User user);
    User find(int id);
}

// 目标实现类
public class UserServiceImpl implements UserService {
    @Override
    public void save(User user) {
        System.out.println("保存用户: " + user.getName());
    }

    @Override
    public User find(int id) {
        System.out.println("查询用户: ID=" + id);
        return new User(id, "用户" + id);
    }
}

// 创建JDK动态代理
public class JdkProxyDemo {
    public static void main(String[] args) {
        // 创建目标对象
        UserService target = new UserServiceImpl();

        // 创建代理
      <!--
      参数说明:
         - 第一个参数：目标类的类加载器
         - 第二个参数：目标类实现的接口数组
         - 第三个参数：InvocationHandler接口的匿名实现类
      InvocationHandler实现:
      - invoke方法在代理对象的任何方法被调用时执行
      - 在目标方法执行前后添加了日志输出
      - 通过method.invoke调用原始目标对象的方法
      -->
        UserService proxy = (UserService) Proxy.newProxyInstance(
            target.getClass().getClassLoader(),
            target.getClass().getInterfaces(),
            new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    System.out.println("======方法执行前: " + method.getName() + "======");
                    Object result = method.invoke(target, args);
                    System.out.println("======方法执行后: " + method.getName() + "======");
                    return result;
                }
            }
        );

        // 调用代理方法
        proxy.save(new User(1, "张三"));
        User user = proxy.find(1);
    }
}
```

