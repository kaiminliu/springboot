# 一、什么是JPA

JPA仅仅是一种规范，也就是说JPA仅仅定义了一些接口，而接口是需要实现才能工作的。

JPA与JDBC区别
- JPA实现由全自动ORM框架
- JDBC实现 各数据库厂商

Eclipse基金会，Jakart Persistence API

JPA规范为我们提供了：

（1）ORM映射元数据
    如：@Entity、@Table、@Id、@Column等注解

（2）JPA的API
    如：entityManager.merge(T t);

（3）JPQL查询语言
    如：`from Student s where s.name = ?1`

# 二、Hibernate与JPA
Hibernate是一个ORM框架，是JPA的实现

Hibernate与Mybatis简单对比
- mybatis: 小巧，方便，高效，简单，直接，半自动
  - 小巧：mybatis就是jdbc封装
  - 国内流行
  - 应用场景：在业务比较复杂系统进行使用
- hibernate: 强大，方便，高效，复杂，绕弯，全自动
  - 强大：根据ORM映射生成不同的SQL
  - 国外流行
  - 应用场景：在业务相对简单的系统进行使用（现在学习，是因为，随着微服务的流行，系统会被拆分为一个个小服务，小服务往往不会太复杂）
# 三、