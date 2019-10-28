#  alibaba 《java开发手册》心得

### 一、命名

**类命名：**正例： MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion

**方法名、参数名、成员变量、局部变量都统一使用 lowerCamelCase 风格，必须遵从
驼峰形式:**
正例： localValue / getHttpMessage() / inputUserId

**常量命名全部大写，单词间用下划线隔开:**
正例： MAX_STOCK_COUNT

### 二、各层命名规约：
```
A) Service/DAO 层方法命名规约
1） 获取单个对象的方法用 get 做前缀。
2） 获取多个对象的方法用 list 做前缀。
3） 获取统计值的方法用 count 做前缀。
4） 插入的方法用 save/insert 做前缀。
5） 删除的方法用 remove/delete 做前缀。
6） 修改的方法用 update 做前缀。
B) 领域模型命名规约
1） 数据对象： xxxDO， xxx 即为数据表名。
2） 数据传输对象： xxxDTO， xxx 为业务领域相关的名称。
3） 展示对象： xxxVO， xxx 一般为网页名称。
4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO。
```

### oop规约
【强制】所有的相同类型的包装类对象之间值的比较，全部使用 equals 方法比较。  

说明： 对于 Integer var = ? 在-128 至 127 范围内的赋值， Integer 对象是在
IntegerCache.cache 产生，会复用已有对象，这个区间内的 Integer 值可以直接使用==进行
判断，但是这个区间之外的所有数据，都会在堆上产生，并不会复用已有对象，这是一个大坑，
推荐使用 equals 方法进行判断。


 【强制】构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中。


【推荐】慎用 Object 的 clone 方法来拷贝对象。
说明： 对象的 clone 方法默认是浅拷贝，若想实现深拷贝需要重写 clone 方法实现属性对象
的拷贝。