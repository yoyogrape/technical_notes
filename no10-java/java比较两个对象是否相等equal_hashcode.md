# java比较两个对象,可以通过重写equals()实现。

1.比较对象时， ==比较的是地址，而equals()比较的是对象内容

2.重写equals()方法必须重写hashCode()


```
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Person person = (Person) o;

        if (age != person.age) return false;
        return name.equals(person.name);
    }

    @Override
    public int hashCode() {
        int result = name.hashCode();
        result = 31 * result + age;
        return result;
    }
}
```
```
public class ObjectEquals {
    public static void main(String[] args) {
        Person person1=new Person("lin",25);
        Person person2=new Person("lin",25);
        System.out.println("通过==比较对象:");
        if(person1==person2) {
            System.out.println("person1和person2是同一个人");
        }else {
            System.out.println("person1和person2不是同一个人");
        }
        System.out.println("通过重写equals()和hashCode()比较对象:");
        if(person1.equals(person2)) {
            System.out.println("person1和person2是同一个人");
        }else {
            System.out.println("person1和person2不是同一个人");
        }
    }
}
```

## 首先先了解什么是hashcode，作用
　　hashcode就是散列码，使用高效率的哈希算法来定位查找对象。
比如 String str = "abcd"，那么计算机会先计算器散列码，再将其放入到对应的内存数组索引处。
在Object类中的 toString 方法：

```return getClass().getName() + “@” + Integer.toHexString(hashCode()); ```

在String类中的hashCode算法:
```
public int hashCode() {
    int h = hash;  //private int hash; // Default to 0
    if (h == 0 && value.length > 0) {
        char val[] = value;  //value是存储的字符数组

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```

## 为什么使用 31 作为散列码，而不是其他的数字?
计算机的乘法会涉及到移位计算，选择 31 的原因是一个素数。

`质数又称素数。指在一个大于1的自然数中，除了1和此整数自身外，没法被其他自然数整除的数`

>在存储数据，计算hash地址的时候，我们希望能尽量较少同样的 hash 地址，即“hash冲突”。
如果得到的相同的 hash 地址过多的话，那么这些数据组成的hash链（链表）就会更长，从而会降低查询的效率。
所以在选择系数的时候选择尽量长的（31=11111 [2] ）系数且乘法尽量不要溢出（大于31 的话，很容易溢出）的系数。
如果计算出来的Hash 地址越大，那么冲突就会越小，查找的效率也会提高。

>31 可以由 i*31 == (i<<5)-i 表示，使用31 是为了更好地分配hash （int类型） 地址。并且31 只占用了 5 bits。
Integer 类的最大值是  ： 2147483647
在java乘法中如果数字相乘过大，会导致移除的问题，导致数据的丢失。


## 为什么要有hashCode
在将对象加入到HashSet 的时候，在HashMap 中会先计算对象的 hashcode 值来判断对象加入的位置。
如果没有相等的 hashcode 则表示该对象没有添加过。进行添加。
如果存在星等的 hashcode（由于哈希碰撞），则使用 equals 方法检查两个对象是否相同的。
　　如果相同的话就不会进行加入。
　　如果不相同，就会重新散列到其他位置。

>博客地址：https://www.cnblogs.com/-zhuang/articles/10564024.html




## 拓展：重写equal方法的时候为什么要重写hashcode的方法

hashcode是用于散列数据的快速存取，如利用HashSet/HashMap/Hashtable类来存储数据时，都是根据存储对象的hashcode值来进行判断是否相同的。
如果我们将对象的equals方法重写而不重写hashcode，当我们再次new一个新的对象的时候，equals方法返回的是true，
但是hashCode方法返回的就不一样了，如果需要将这些对象存储到集合中（比如：Set，Map ...）的时候就违背了原有集合的原则，
下面让我们通过一段代码看下。

#### 在HashMap中的get(Object key)的执行流程
在HashMap中get(Object key)

计算key的hashcode()，等到一个整数n

然后用这个n%(length)【length代表当前map的最大的容量】=index

用index作为下标，得到hashmap内部的桶的结构中位于index处的链表list（Java1.8中有可能是红黑树，这里用链表代替。）

然后循环遍历列表，直到能够equals（key），输出当前值的value

通过上面可以知道，hashcode是为了确定在桶中的位置，而equals是了区别链表中的内容

不重写hashcode()使用默认的Object的hashcode()方法

```
public class Solution {
	public int a;
	public Solution(int val){
		this.a = val;
	}
	public static void main(String[] args){
		HashMap<Solution,Integer> test = new HashMap<Solution,Integer>();
		Solution s1 = new Solution(1);
		test.put(s1, 1);
		System.out.println(test.get(new Solution(1)));//输出null
	}
}
```


```
public class Solution {
	public int a;
	public Solution(int val){
		this.a = val;
	}
	@Override
	public boolean equals(Object k){
		if(this == k){
			return true;
		}
		if(k instanceof Solution){
			Solution s = (Solution)k;
			return ((Solution) k).a == s.a;
		}
		return false;
	}
	public static void main(String[] args){
		HashMap<Solution,Integer> test = new HashMap<Solution,Integer>();
		Solution s1 = new Solution(1);
		Solution s2 = new Solution(1);
		test.put(s1, 1);
		test.put(s2, 2);
		System.out.println(test.get(s1));//1
		System.out.println(test.get(s2));//2
	}
}
```
输出结果是 1,2 这样的情况下map里面的key应该是唯一的，这样却失去了唯一性。



## Java面试题之HashMap如何有效减少碰撞

碰撞：HashMap运用put方法存储多个元素时，计算得出相同的hashCode，在put时出现冲突。

>处理：利用“拉链法”处理HashCode的碰撞问题；当我们将键值对传递给put方法时，他调用键对象的hashCode()方法来计算hashCode，
然后找到bucket（哈希桶）位置来存储对象；当用get方法获取对象时，通过键对象的equals()方法找到正确的键值对，然后返回值对象。
HashMap使用链表来解决碰撞问题，当碰撞发生了，对象将会存储在链表的下一个节点中。hashMap在每个链表节点存储键值对对象。
当两个不同的键却有相同的hashCode时，他们会存储在同一个bucket位置的链表中。键对象的equals()来找到键值对。

### java8中对hash的调整
>(1)由 数组+链表 的结构改为 数组+链表+红黑树。
拉链过长会严重影响hashmap的性能，所以1.8的hashmap引入了红黑树。
在链表元素数量超过8时改为红黑树，少于6时改为链表，中间7不改是避免频繁转换降低性能。
相对于链表，改为红黑树后碰撞元素越多查询效率越高。链表O(n)，红黑树O(logn)。

>(2)优化了高位运算的hash算法：h^(h>>>16)
将hashcode无符号右移16位，让高16位和低16位进行异或。

>(3)扩容后，元素要么是在原位置，要么是在原位置再移动2次幂的位置，且链表顺序不变。
不需要重新计算hash，只需要根据原来hash值新增的bit是1还是0分别放进两个链表lo和hi（非红黑树的情况）里，0的话索引没变，1的话索引变为原索引加原来的数组长度。
因为用的尾插法所以新数组链表不会倒置，多线程下不会出现死循环。


