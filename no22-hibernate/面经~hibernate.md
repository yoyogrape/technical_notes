## Question list：
* 项目中使用Hibernate的优缺点，以及如何优化
* Hibernate的时态，以及每种时态转换过程
* Hibernate的一对一，一对多，多对多是怎样配置的
* 乐观锁和悲观锁，还有没有用过其他的锁
* Hibernate的一级缓存以及二级缓存
* Hibernate中session有几种创建方式？都有那些区别？
* Hibernate的session是否多线程共享
* JPA条件分页怎样实现
* 事务的隔离级别
* 工作中有没有遇见过脏数据，怎样处理的
* 事务的传播性

## Hibernate工作原理及为什么要用？

读取并解析配置文件  
读取并解析映射信息，创建SessionFactory  
打开Sesssion  
创建事务Transation  
持久化操作  
提交事务  
关闭Session  
关闭SesstionFactory  


使用Hibernate框架就不用我们写很多繁琐的SQL语句。
Hibernate实现了ORM，能够将对象映射成数据库表，从而简化我们的开发！

## hibernate的三种状态之间如何转换？
hibernate的三种状态是瞬时状态、持久状态、托管状态
瞬时状态：session缓存中没有对象，数据库中也没有对应记录
持久状态：session缓存中有对象，数据库中有对应记录
托管状态：session缓存中没有对象，数据库中有对应记录
比如有一个User实体类和一张User表。当new了一个user对象，但没有开启事务。
此时user就处于瞬时状态，与数据库的数据没有任何联系，当开启事务后，执行了session.save()方法后，
session缓存中存放了该user对象，而数据库中也有相应的这条数据，此时就转换为持久状态。当事务提交后，
session被销毁。session缓存中就没有user对象，而数据库表中有相应记录，此时为托管状态。

## 比较hibernate的三种检索策略优缺点
* 立即检索：

优点： 对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便的从一个对象导航到与它关联的对象；
缺点： 1.select语句太多；2.可能会加载应用程序不需要访问的对象白白浪费许多内存空间；
立即检索:lazy=false；

* 延迟检索：

优点： 由应用程序决定需要加载哪些对象，可以避免可执行多余的select语句，以及避免加载应用程序不需要访问的对象。因此能提高检索性能，并且能节省内存空间；
缺点： 应用程序如果希望访问游离状态代理类实例，必须保证他在持久化状态时已经被初始化；
延迟加载：lazy=true；

* 迫切左外连接检索：

优点： 1对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便地冲一个对象导航到与它关联的对象。2使用了外连接，select语句数目少；
缺点： 1 可能会加载应用程序不需要访问的对象，白白浪费许多内存空间；2复杂的数据库表连接也会影响检索性能；
预先抓取： fetch=“join”；

## 说下Hibernate的缓存机制
* 一级缓存：

Hibenate中一级缓存，也叫做session的缓存，它可以在session范围内减少数据库的访问次数！ 只在session范围有效！ Session关闭，一级缓存失效！
只要是持久化对象状态的，都受Session管理，也就是说，都会在Session缓存中！
Session的缓存由hibernate维护，用户不能操作缓存内容； 如果想操作缓存内容，必须通过hibernate提供的evit/clear方法操作。

* 二级缓存：

二级缓存是基于应用程序的缓存，所有的Session都可以使用
Hibernate提供的二级缓存有默认的实现，且是一种可插配的缓存框架！如果用户想用二级缓存，只需要在hibernate.cfg.xml中配置即可； 不想用，直接移除，不影响代码。
如果用户觉得hibernate提供的框架框架不好用，自己可以换其他的缓存框架或自己实现缓存框架都可以。
Hibernate二级缓存：存储的是常用的类

## Hibernate中有哪些缓存？都是如何配置的?(三个缓存都要说)
hibernate中有一级缓存（session缓存），二级缓存（sessionfactory缓存）、查询缓存（三级缓存）
1级缓存：创建session，每次获取数据，hibernate会优先从session中找，如果没有，再去查询。查询后放到缓存中，当session关闭，一级缓存销毁。session缓存中的数据不能被别的session共享
2级缓存：sessionfactory级别缓存，sessionfactory中的session共享一个二级缓存。
sessionfactory缓存分为两部分：内置缓存和外置缓存，内置缓存是hibernate自己使用，外界只能读取。而外置缓存是提供个用户使用，hibernate只提供了外置缓存的接口，需要第三方提供商实现。常用的有ehcache等。
二级缓存的使用：1.先导入ehcache的jar包和ehcache的配置文件（二级缓存的默认属性）
        2.在hibernate.cfg.xml中开启二级缓存（默认是关闭）
        3.确定二级缓存提供商
        4.配置缓存对象（有两种模式，一种只读，一种读写）
3级缓存（查询缓存）：由于session.creatquery()在二级缓存中不起作用，便提供了三级缓存
三级缓存的配置：1.开启二级缓存，确定提供商，确定缓存对象
        2.查询后，调用setcacheable(true)放到缓存中
        3.读取时，调用setcacheable(true)去缓存中取


## JDBC hibernate 和 ibatis 的区别
对比上面两个，一目了然，hibernate相比mabatis居然连sql编写也封装了。
这点本质的不同，是mybatis与hibernate的最大区别

区别一：扩展性方面。使用mybatis要自己根据相应的数据库写不同的sql实现，例如用mysql数据库写了一个sql的分页语句，
如果换成了oracle数据库就用不了了。然而如果是hibernate的hql语句则是对实体类(User)和属性的操作，
也拿分页查询来说，string hql=“from User”; Query q1=session.creatQuery(hql); q1.setFirstResult(m);q1.setMaxResult(n); 
List list=q1.list();会根据具体的数据库生成各自相应不同的查询的语句，所以移植性很好。

区别二：sql效率，优化方面。Hibernate的查询，更新都是对所有字段的操作例如(根据id查询，sessin.get(User.class,"id")，
还有要session.update(对象)时，按照对象的id做条件，更新其他字段信息。如果想要更新部分字段，其他字段保持不变，
则对象的其他字段对应的属性也要有原值)，这一点会有性能消耗。Hibernate也可以自己写SQL来指定需要查询的字段，
但这样就破坏了Hibernate开发的简洁性。而Mybatis的SQL是手动编写的，所以可以按需求指定查询的字段。maybatis的优化，
就是对sql语句的优化，MyBatis在Session方面和Hibernate的Session生命周期是一致的，同样需要合理的Session管理机制

## 讲下什么是ORM?ORM组件有哪些？
orm是对象关系映射，是一种程序技术，通过将java对象映射到数据库表，通过操作java对象，就可以完成对数据表的操作。
常用的orm组件有JDBC、Hibernate、mybatis、springDate等


## Hibernate和JDBC相比的优点是什么?缺点是什么?
优点：简化了Dao层访问数据库的重复性代码
     hibernate是一个全自动的orm框架，它可以自动生成sql语句，自动执行。
     hibernate的映射灵活，（数据库方言）支持很多关系型数据库，从一对一到多对多的各种复杂关系
     hibernate提供了缓存机制（session缓存，二级缓存，查询缓存），提高了性能。
缺点：hibernate是个重量级框架，对jdbc的封装过于厉害，失去了对sql的控制，无法对sql进行优化。
在面对批量操作的时候，hibernate的性能就远远低于jdbc。


## Hibernate中session有几种创建方式？都有那些区别？
有两种创建方式：
第一种是：通过sessionfactory.getcurrentSession()创建session，它是从当前线程中去找，
看有没有session，如果有则返回session，如果没有则创建session。属于单例模式
第二种是：通过sessionfactory.opensession()创建session，每次都是新创建一个session。


## 说说连接池的原理
连接池：连接池就是提前创建好connection放在一个池中，状态都为空闲状态，当外界需要用到connection时，
分配connection给予使用，该connection状态为使用状态。连接池有自己的管理机制，但从conncetion不够时，
它会闯创建，但多于规定数时，进行销毁。工作完成的connetion会被回收，状态变更为空闲状态


## 说说悲观锁和乐观锁？
悲观锁：默认在使用数据时一定发生别人修改我使用的数据，所以对使用数据进行上锁，数据库提供实现（通过for update来上锁）
乐观锁：默认在使用数据时没有发生别人修改我使用的数据。通过版本号来实现。在实体类中加入version属性，表中添加version字段，每次事务提交之前都核对版本号，如果版本号不匹配，这事务提交失败，重新获取数据，每次事务提交后，版本号自增

## hibernate的优化
制定合理的缓存策略;

尽量使用延迟加载特性;

采用合理的Session管理机制;

使用批量抓取，设定合理的批处理参数(batch_size);

进行合理的O/R映射设计