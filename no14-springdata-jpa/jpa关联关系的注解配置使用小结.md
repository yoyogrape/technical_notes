# jpa关联关系的注解配置使用小结

### 1、一对一关系的配置
>     @JoinColumn(name="component_id")
>     @OneToOne
>     private Component component;
>     **说明：**
>
>     *   注解放在关系维护方，component_id为本表中关联表的外键。
>     *   需要在component_id的注解中添加insertable和updatable属性为false，避免生成多余的列。如下：

>     @Column(name = "component_id",insertable = false,updatable = false)
>     private Long componentId;
### 2、一对多关系的配置

>     @JoinColumn(name="workflow_component_id")
>     @OneToMany(fetch = FetchType.LAZY,cascade=CascadeType.ALL)
>     private Set<RParameterValue> params=new HashSet<>();

**说明：**
* workflow_component_id是关联表中外键字段，默认关联本表中的id。
* 可以设置fetch为懒加载，cascade级联删除等操作。

### 3、多对一关系的配置


### 4、多对多关系的配置


### 5、注解及属性相关说明

**以@OneToMany为例说明：**
* fatch:可选择项包括：FetchType.EAGER 和FetchType.LAZY，设置是否懒加载。

* cascade:该属性定义类和类之间的级联关系。
```
①CascadeType.PERSIST
级联持久化（保存）操作（持久保存拥有方实体时，也会持久保存该实体的所有相关数据。）

②CascadeType.REMOVE
Cascade remove operation，级联删除操作。
删除当前实体时，与它有映射关系的实体也会跟着被删除。

③CascadeType.MERGE
Cascade merge operation，级联更新（合并）操作。
当Student中的数据改变，会相应地更新Course中的数据。

④CascadeType.DETACH
Cascade detach operation，级联脱管/游离操作。
如果你要删除一个实体，但是它有外键无法删除，你就需要这个级联权限了。它会撤销所有相关的外键关联。

⑤CascadeType.REFRESH
Cascade refresh operation，级联刷新操作。
假设场景 有一个订单,订单里面关联了许多商品,这个订单可以被很多人操作,那么这个时候A对此订单和关联的商品进行了修改,与此同时,B也进行了相同的操作,但是B先一步比A保存了数据,那么当A保存数据的时候,就需要先刷新订单信息及关联的商品信息后,再将订单及商品保存。

⑥CascadeType.ALL
Cascade all operations，清晰明确，拥有以上所有级联操作权限。
```
* mappedBy:定义类之间的双向关系。如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系，我们就需要使用这个属性进行定义， 否则可能引起数据一致性的问题。 

* optional:属性是定义该关联类对是否必须存在。 

>    值为true 时, 关系被维护端可以不存在，查询的结果仍然会返回关系维护端，在关系维护端中指向关系被维护端的属性为null。   
>    值为false时，关联类双方都必须存在，如果关系被维护端不存在，查询的结果为null。 

实际上，optional 属性指定了联接关系optional=false联接关系为inner join,  optional=true联接关系为left join。 

**@JoinColumn说明:**

>指明了被维护端（OrderItem）的外键字段为order_id，它和维护端的主键(orderid)连接,unique= true 指明order_id列的值不可重复。

<center>未完待续</center>
