jpa学习分为三个模块
- spring：spring-data-jpa
- springboot：springboot-jpa
- jpa-hibernate
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

hibernate 示例

pom.xml

实体类
code first ：不需要创建表，只需要关心pojo类，当时需要创建数据库

配置文件

创建数据库

测试(API使用)
1、创建 SessionFactory
StandardServiceRegistryBuilder
MetadataSources

test1
sf.openSession
s.beginT
tx.commit

save
saveOrUpdate 有id更新，无id吃ARU
find
update
remove

测试(hql使用) hql 与 jpql 区别
session.createQuery.getResultList

jpa集成hibernate
添加 META—INF.persistence.xml

测试（JPA使用）
Persistence.createEnMF

persist
find（立即查询）
getRef（延迟查询）
merge （saveOrUpdate，只更新可以用jpql）
remove （直接删除，报删除游离状态异常，只能删除持久化状态（从数据库中查出的状态就是持久化状态））

JPQL
em.createQuery
    使用sql语句
        createNativeQuery

实现切换为openJpa
1.引入依赖
2.


jpa 4种状态
- 临时
- 持久，对持久状态的更改会对数据库进行同步
  - 只要修改，提交就会，被持久化，即使是find
  - 1. find -> 修改实体 -> commit
  - 2. find -> remove -> persist -> commit 不懂：p9 7:00
  - 
- 删除
- 游离

一级缓存 基于EntityManager 
二级缓存



spring-data-jpa 简化jpa的框架
1、引入依赖

2、配置
2.1、xml
EntityManagerFactory
TransationManager

2.2、测试类测试

repo.delete(customer); 底层会帮我们先查询一下（游离=>持久），再删除

2.2、javaConfig


3、repository api的使用
CrudRepository
PagingAndSortRepository
    PageRequest.
    Sort. 
        两种：字符串硬编码、type-safe

4、自定义持久化操作（复杂）
4.1、JPQL （@Query）
- 可以自由设置返回值，返回单挑记录使用pojo类，多条记录使用list
- 查询可以使用 
    - ?数字
    - :参数名
- 增删改，需要加声明式事务@Transaction（通常放在业务逻辑层） + @Modifying, 否则报错
- JPQL不支持新增，但他的实现Hibernate支持，伪新增(insert into ... select)，可以插入从别的地方查出的
    - 我认为直接使用 SQL不久得了
测试
-   提示插件 jpabuddy 好像已经过期了

4.2、SQL（@Query(nativeQuery=true)）


4.3、规定方法名
findByXxx



Like 需要自己拼上百分号


4.4、动态条件查询（多条件查询，有值就加入到查询条件，没有就不参与查询）
4.4.1、QueryByExample
- 字符串
withIgnorePaths 忽略某个条件
withIgnoreCase 会使用lower函数
withStringMatcher 对所有字符串property进行匹配
withMatcher（静态方法【支持链式写法】或lambda表达式） 对指定字符串property进行匹配
使用 withMatcher时 withIgnoreCase 会失效 ？？？ p17 26:00
  
4.4.2、Specifications（很复杂）
new Specifications(root, query, builder);

不支持分组，就是设置了，底层是固定的，这个分组也是无效的
不支持查询指定字段，底层会设置死 em， 但是存在线程安全问题 不能使用Autowired，要用@PersistenceContext，一个线程绑定一个em对象

@PersistenceContext 原理和作用，以及与Autowired Resource 区别
https://www.1024sky.cn/blog/article/539

还可以使用 em.getCriteBuilder，builder.createQuery query.form


4.4.3、Querydsl
可读性更好
1、集成接口
2、映入依赖
插件生成Q类
BooleanBuilder
Q类.类.xx
不可以可以分组、指定字段 需要用 em， 但是存在线程安全问题 不能使用Autowired，要用@PersistenceContext，一个线程绑定一个em对象

@PersistenceContext 原理和作用，以及与Autowired Resource 区别
https://www.1024sky.cn/blog/article/539

JPAQueryFactory方式

集成QuerydslPageSortSupport from()方式
fetchJoin


注解全
注解在上面各个阶段都生效吗
First1 JPA 在mysql下生效吗
单标查询ok
多表查询ok
单标动态查询ok
多表动态查询 可以
Querydsl https://blog.csdn.net/wjw465150/article/details/124879048
JPQL
HQL
SQL

看别人博客再行补充
JPA-Hibernate-JDBC 与 MyBatis—JDBC 对比


B站 JPA评论
赞
1.jpa如果微服务项目不创建表关联的话贼方便 也不需要 关联注解 如果单体表关联那种 直接注解控制关联 设置级联 随便查
    - 实际生产里关联关系一大堆，用起来是真滴烦人，在动态查询和关联关系以及sql 优化上来说，我个人的jpa 开发体验甚至不如jdbcTemplate
客观
1.DDD，CQRS读写分离，两者结合，jpa重在领域建模，读操作复杂查询用mybatis
2.说实话国内mybatis绝对不会过时，因为灵活。看着jpa很美，但是太完备的东西灵活度就差，稍微想动一动就很要命。我新起的项目基本已经完全放弃jpa了，mybatis真的香。不过学一下jpa也有点用，至少面试的时候有点用
3.两种orm技术，现在都能无缝切换，用哪个都可以，架构师想要的样子，我会

怼
1.这玩意就是把写个SQL解决的问题弄复杂，映射完了生成的SQL是不可控，会出现效率问题。99%以上情况不会换数据库，如果换数据库，改代码那点工作根本不算啥，整个运维体系，历史数据导入等等一系列问题，这就导致换数据库情况根本不存在。jpa可以不写SQL，呵呵，不会SQL根本找不到工作
2.为什么国外喜欢用全自动的ORM框架，而国内喜欢用MyBatis这种半自动呢？技术都是跟着大厂走，国内大厂面对的数据往往是上亿的，全自动ORM框架做SQL优化就非常困难了，而看起来相对繁琐的MyBatis自定义SQL反而更方便。
3.看完之后，觉得真的是难用，不明白有些公司为啥用这个，性能不高，开发效率也不高，学习成本还高.mybatis+mybatis-plus  不香嘛，不仅写起来简单，而且jpa里边的那些find啥的默认方法，mybatis-puls都有，还特好用，开发起来也简单。单表增删查改也有默认实现，多表的话jdbc也比jpa好用。所有来个大佬给解释一下为什么？
    - 可能唯一的好处就是换数据库方便吧
        - :就因为关联一大堆才舒服方便啊 级联跟注解关联简直爽死 一大堆关联查询数据 查一下本表关联表要的数据也出来了直接用json注解控制序列化 如果用sql要写一大堆 但是jpa注解控制 啥也不用写查也方便
        - 感觉多表联查不方便绝对是关联注解设置有问题 我以前也这么感觉 但是用久了就发现规律了 以前我也jpa里用jdbctemplate 后来根本不需要 要写原生sql肯定是设计不合理
4.mybatis灵活度更好吧
5.国外那用户量能和国内比吗。。。
人家也不需要秒杀什么的活动，人家是零元购。所以开发的框架一个比一个简单粗暴，但是效率不一定是最好的，重在好上手。
6.得考虑学习成本，mybatis几乎零入门门槛，jpa不学一段时间，真的不能碰生产环境代码