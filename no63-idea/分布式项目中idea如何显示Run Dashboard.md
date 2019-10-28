# 分布式项目中idea如何显示Run Dashboard

在workspace.xml文件的  ```<component name="RunDashboard">```节点下添加



    <option name="configurationTypes">
      <set>
        <option value="SpringBootApplicationConfigurationType" />
      </set>
    </option>