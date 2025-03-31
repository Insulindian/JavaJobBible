# SpringBoot

在 Spring 中，“Bean”指的是由 Spring 容器管理的对象。Spring 容器负责创建、配置、管理这些 Bean 的生命周期和依赖关系。

## Bean 扫描

```java
@SpringBootApplication  //SpringBootApplication注解包含了@ComponentScan,因此会自动扫描对应包及子包下的所有 Spring Bean。
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

## Bean 注册

@Component
用于标记通用组件，其他注解如 @Controller、@Service、@Repository 都是基于它的派生注解

如果要注册来自第三方的 bean,不能使用@Component,可以使用@Import @Bean
通过配置类手动创建并注册第三方组件为 Spring Bean

```java
@Configuration
public class ThirdPartyConfig {
    @Bean
    public Component thirdPartyComponent() {
        return new Component();
    }
}
```

## Bean 生命周期

![1741781091553](image/八股/1741781091553.png)Insulindian

1. **加载 Bean 定义**

   - 读取并解析 `applicationContext.xml`（或扫描 `@Component` 注解的 Bean），将配置信息转换为 `BeanDefinition`。

2. **BeanFactoryPostProcessor**

   - 实现 `BeanFactoryPostProcessor` 接口，在 `postProcessBeanFactory` 方法中得到完整的 `BeanDefinition`。

3. **Bean 实例化**

   - 实例化前置
   - 实例化对象（分配内存，Spring 选择合适的构造方法，使用 **反射调用构造函数** 创建对象，但此时属性还未填充）
   - 实例化后置
   - 依赖注入：设置属性,将依赖的 Bean (@Autowired 的) 注入和装配。

4. **初始化**

   - 执行各种通知（如 `Aware` 接口）
     BeanNameAware：获取当前 Bean 在容器中的名称.BeanFactoryAware：获取当前 Bean 所在的 BeanFactory。
     ApplicationContextAware：获取所在的 ApplicationContext。
   - 执行初始化的前置方法 BeanPostProcessor#before
   - 执行初始化方法（若实现 InitializingBean 接口,实现重写的方法,或执行自定义的 init）
   - 执行初始化的后置方法 BeanPostProcessor#after ( **AbstractAutoProxyCreator（实现了 BeanPostProcessor 接口）会检查每个 Bean 是否需要被代理（是否有切面应用于它）如果需要代理，Spring 会根据情况选择代理技术** )

5. **使用 Bean**

   - 在程序中使用 Bean。

6. **销毁 Bean**

   - 将 Bean 对象进行销毁操作（如 `@PreDestroy` 或 `DisposableBean.destroy`）。