# Mybatis

## MyBatis 的用途
MyBatis 是一个持久层框架，用于简化 Java 应用程序与关系型数据库之间的交互。它通过 XML 或注解的方式将 SQL 语句与 Java 对象映射起来，减少了手动编写 JDBC 代码和解析结果集的工作量。

主要用途包括：
- **简化数据库操作**：通过映射文件或注解直接执行 SQL。
- **支持动态 SQL**：根据条件动态生成 SQL 语句。
- **对象与数据库表的映射**：将数据库记录映射为 Java 对象。
- **事务管理**：与 Spring 等框架集成，支持事务管理。

## Mybatis优点举例
- sql与代码分离，sql在xml中，代码中只需要调用sql的id即可
- 支持动态sql，xml中可以使用if、choose、foreach等标签来实现动态sql的拼接

## Mybatis$和#的区别
- #：预编译，使用占位符，防止 SQL 注入，性能更好。
- $：直接拼接，可能导致 SQL 注入，性能较差。

