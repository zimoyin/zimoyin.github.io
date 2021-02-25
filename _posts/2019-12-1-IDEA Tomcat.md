# 一、创建Maven项目

1. 利用maven创建web项目，首先创建项目中选择maven，然后在maven中勾选`Create from archetype`选项，然后选择`org.apache.maven.archetypes:maven-archetype-webapp` 然后Next

[![yvY2Ed.png](https://s3.ax1x.com/2021/02/25/yvY2Ed.png)](https://imgtu.com/i/yvY2Ed)

2. 对项目命名，并且选择项目保存地址，然后next

[![yvJAeJ.png](https://s3.ax1x.com/2021/02/25/yvJAeJ.png)](https://imgtu.com/i/yvJAeJ)](https://imgtu.com/i/yvJAeJ)

3. 这三个地方必须注意，第一个选择你的maven，第二个选择你已经配置好的maven的**setting.xml**(==注意你的setting.xm要使用国内源否则你会崩溃)==，第三个是选择你的类保存的位置

[![yvJVoR.png](https://s3.ax1x.com/2021/02/25/yvJVoR.png)](https://imgtu.com/i/yvJVoR)

4. 单机finish后IDEA会自动配置你的环境，时间可能有点长耐心等待

二、配置Tomcat

1. 配置Tomcat，选择Add Configuration。也可以从Run菜单中选择 Edit Configurations... 他们打开后是一样的

[![yvJiyF.png](https://s3.ax1x.com/2021/02/25/yvJiyF.png)](https://imgtu.com/i/yvJiyF)

2. 选择`+` 后选择Tomcat Service 中的Local

[![yvJFL4.png](https://s3.ax1x.com/2021/02/25/yvJFL4.png)](https://imgtu.com/i/yvJFL4)



3. 配置Tomcat

[![yvJw6S.png](https://s3.ax1x.com/2021/02/25/yvJw6S.png)](https://imgtu.com/i/yvJw6S)

# 二、配置Tomcat的地址

[![yvJnW6.png](https://s3.ax1x.com/2021/02/25/yvJnW6.png)](https://imgtu.com/i/yvJnW6)

4. 配置项目:单击Fix选择 xxx:war exploded	如果这个没有这个选项则看下图2

[![yvJMQO.png](https://s3.ax1x.com/2021/02/25/yvJMQO.png)](https://imgtu.com/i/yvJMQO)

图2：

[![yvJYFI.png](https://s3.ax1x.com/2021/02/25/yvJYFI.png)](https://imgtu.com/i/yvJYFI)



5. 单击appl和ok后就可以了

6. 选择file菜单下的project structure.选择则下图的右侧那个小按钮效果一样

[![yvJlOe.png](https://s3.ax1x.com/2021/02/25/yvJlOe.png)](https://imgtu.com/i/yvJlOe)

7. 

[![yvJGTA.png](https://s3.ax1x.com/2021/02/25/yvJGTA.png)](https://imgtu.com/i/yvJGTA)



8. 添加Tomcat依赖:没有他就无法创建Servlet

[![yvJ3eH.png](https://s3.ax1x.com/2021/02/25/yvJ3eH.png)](https://imgtu.com/i/yvJ3eH)



9. 

[![yvJtYt.png](https://s3.ax1x.com/2021/02/25/yvJtYt.png)](https://imgtu.com/i/yvJtYt)

[![yvJdl8.png](https://s3.ax1x.com/2021/02/25/yvJdl8.png)](https://imgtu.com/i/yvJdl8)