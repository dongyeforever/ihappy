###### proguard（开源、github上有源码，也可引用打包好的jar）

1. java 程序使用：https://github.com/wvengen/proguard-maven-plugin

   有详细的使用方法（包括配置文件）、源码

2. 第三方jar 和自己的bean不做 混淆

简单说一下使用：

1、java配置文件引用Jar依赖(也可用源码，根据需求)，简单测试，引用maven依赖，pom文件中

```
<build>
    <plugins>
        <!-- ProGuard混淆插件-->
        <plugin>
            <groupId>com.github.wvengen</groupId>
            <artifactId>proguard-maven-plugin</artifactId>
            <version>2.4.0</version>
            <executions>
                <execution>
                    <!-- 混淆时刻，这里是打包的时候混淆-->
                    <phase>package</phase>
                    <goals>
                        <!-- 指定使用插件的混淆功能 -->
                        <goal>proguard</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!-- 是否将生成的PG文件安装部署-->
                <attach>true</attach>
                <!-- 是否混淆-->
                <obfuscate>true</obfuscate>
                <!-- 指定生成文件分类 -->
                <attachArtifactClassifier>pg</attachArtifactClassifier>
                <proguardInclude>${basedir}/proguard.conf</proguardInclude>
                <libs>
                    <lib>${java.home}/lib/rt.jar</lib>
                    <lib>${java.home}/lib/jce.jar</lib>
                </libs>
                <!-- 对什么东西进行加载，这里仅有classes成功，不可能对配置文件及JSP混淆吧-->
                <injar>classes</injar>
                <outjar>${project.build.finalName}-pg.jar</outjar>
                <!-- 输出目录-->
                <outputDirectory>${project.build.directory}</outputDirectory>
            </configuration>
        </plugin>
    </plugins>
</build>
```

2、在根目录添加配置文件

![image-20210816204053463](C:\Users\Candy\AppData\Roaming\Typora\typora-user-images\image-20210816204053463.png)

3、正常运行，结果：类名、部分变量名变成了A、B

​      具体请参考正常源码：demo项目；

​       正常编译的项目jar包：demo-1.0-SNAPSHOT.jar

​       proguard编译的jar包：classes-pg.jar

​		具体参见target目录；

对比图：

![image-20210816204453270](C:\Users\Candy\AppData\Roaming\Typora\typora-user-images\image-20210816204453270.png)



![image-20210816204518504](C:\Users\Candy\AppData\Roaming\Typora\typora-user-images\image-20210816204518504.png)

其他参考：https://www.guardsquare.com/manual/quickstart

