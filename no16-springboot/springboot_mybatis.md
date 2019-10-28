# springboot 整合mybatis

1）创建两个测试实体

```
@Data
public class Department {
    private Integer id;
    private String dname;
}


@Data
public class Employee {
    private Integer id;
    private String lastName;
    private String email;
    private Integer gender;
    private Integer d_id;
}

```

2）创建对应的mapper
```
@Mapper
public interface DepartmentMapper {

    @Select("select * from department where id=#{did}")
    Department getDeptById(Integer did);

    @Delete("delete from department where id=#{did}")
    Integer delDeptById(Integer did);

    @Insert("insert department(dname) value(#{dname})")
    Integer insertDept(Department department);

    @Update("update department set dname=#{dname} where id=#{id}")
    Integer updateDept(Department department);

}

```
3）测试
```
@RestController
public class DeptController {
    @Autowired
    private DepartmentMapper dao;
    @RequestMapping("/dept/getOne")
    public void getDept(){
        System.out.println("---DeptController---getDept()---");
        Department dept = dao.getDeptById(1);
        System.out.println(dept.toString());
    }
}
```
4）说明  
可以去掉mapper上面的注解，在启动类上进行全局扫描
```
@MapperScan(value = "com.songjn.springboot_mybatis.mapper")
```

### 第三节 配置文件集成mybatis
1）创建mapper文件
```
//@Mapper或者@MapperScan将接口扫描装配到容器中
public interface EmployeeMapper {

    Employee getEmpById(Integer id);

    void insertEmp(Employee employee);
}

```
2）添加全局扫描
```
@MapperScan(value = "com.songjn.springboot_mybatis.mapper")
@SpringBootApplication
public class SpringbootMybatisApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringbootMybatisApplication.class, args);
	}
}
```
3）增加mybatis配置文件
```
-resource
    -mapper
        EmployeeMapper.xml
    mybatis-config.xml 
    
①mybatis-config.xml如下：

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>

②EmployeeMapper.xml如下：

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.songjn.springboot_mybatis.mapper.EmployeeMapper">
   <!--    public Employee getEmpById(Integer id);

    public void insertEmp(Employee employee);-->
    <select id="getEmpById" resultType="com.songjn.springboot_mybatis.entity.Employee">
        SELECT * FROM employee WHERE id=#{id}
    </select>

    <insert id="insertEmp">
        INSERT INTO employee(lastName,email,gender,d_id) VALUES (#{lastName},#{email},#{gender},#{dId})
    </insert>
</mapper>
```
4）yml中配置xml文件的位置信息
```
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  mapper‐locations: classpath:mybatis/mapper/*.xml

```
