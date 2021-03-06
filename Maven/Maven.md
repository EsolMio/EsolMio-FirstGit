# Maven - [Strated Guide](http://maven.apache.org/guides/getting-started/index.html)
maven是一个将项目视为对象的管理工具（Porject Object Model，POM）

在整个Maven项目中，最基本的文件为`pom.xml`，此文件用于指明`reprository`, `dependencies`, `plugins`

Maven本质上以插件为核心的集成框架，插件为执行，实现的主体。插件用于创建文件、项目，编译，测试等等

## *暂时未归类项目记录
1. 若需要将resources加入`jar`包中，需要将文件放置在`${basicdir}/src/main/resources`目录下，并在命令行中执行`mvn package`或者`mvn jar:jar`（在打包方式为`jar`时使用）
2. 在命令行输入`mvn ...`时，均是执行对应的插件plugin（对应的goal）。
3. 对于不同的打包方式有对应的dependency
4. `mvn compile` `mvc test`/`mvc test-compile` `mvc package` `mvc install` - instead of phase

## *创建Maven项目
1. 在cmd中使用`mvn archetype:generate`
2. Maven本身提供了多种“原型”archetype

## *Lifecycle-生命周期

### Introduce phase & use

1. maven的lifecycle（生命周期）由多个phase（阶段）组成，以下为简述，实际的；Lifecycle会因为打包方式的不同而改变：
	- validate 验证
	- compile 编译，对应`src/main/java`
	- test 测试，对应`src/test/java`
	- package 打包，对应`target/classes`
	- verify 校验
	- install 安装至repository
	- deploy 部署

   上述所说的几项即在命令行中需要输入以执行的语句，即对于一个新的项目，需要按照上述的顺序执行上述的语句。

	1. 使用Build Lifecycle（构建项目时的生命周期）有两种方式：1. 在命令行中使用每个阶段phase的默认名称 2. 配置plugin（插件）的goals，并在命令行中使用（执行）插件

3. 执行声明周期中的特定阶段，在命令行输入`mvn [phase]`即可，如：`mvn compile`。
	1. Tips：在使用默认的phase名称时，其所绑定的goal由在`pom.xml`的`<packing>`tag所设定的打包方式确定

### Use `goal` to binding phase

- 使用不同的打包方式以使用不同的phase：不同的打包方式有不同的goal绑定在特定的phase上，以下示范`jar`：

|Phase( 固定 )|plugin:goal(可自定义)|
|-|-|
|`process-resources`|`resources:resources`|
|`compile`|`compiler:compile`|
|`process-test-resources`|`resources:testResources`|
|`test-compile`|`compiler:testCompile`|
|`test`|`surefire:test`|
|`package`|`jar:jar`|
|`install`|`install:install`|
|`deploy`|`deploy:deploy`|

   - 需按照以上顺序执行，顺序执行下来为一个生命周期（不确定），一般在执行下层phase阶段时，会一同将之前的phase一同执行一遍

- 配置plugin的goal以绑定至phase:
  > The goals that are configured will be added to the goals already bound to the lifecycle from the packaging selected. If more than one goal is bound to a particular phase, the order used is that those from the packaging are executed first, followed by those configured in the POM. 
  
   如果有多个goal绑定到同一个phase，会先执行指定打包方式所默认绑定的goals，followed（其次）再执行配置的goals
   - 使用`<executions>`，`<goals>`tag以实现对plugin的goal的声明:
     example:
     ```
     <plugin><!-- tips: modello only on generate-source phase -->
         <groupID>org.codehaus.modello</groupId>
         <artifactId>modello-maven-plugin</artifactId>
         <version>1.8.1</version>
         <executions>
             <exectuion>
                <configuration>
                   <models>
                   <model>src/main/mdo/maven.mdo</model>
                   </models>
                   <version>4.0.0</version>
                </configuration>
                 <goals>
                     <goal>java</goal>
                 </goals>
             </exectuion>
         </executions>
     </pulgin>
     ```
     1. 一个`<execution>`对应一个`<configuration>`，一个`<phase>`和多个`<goal>`(`<goals>`位于`<execution>`中)

     2. 为何在`<exectuions>`中定义`<goals>`？可以多次在不同的配置(configuration)中运行相同的goal

      > You might be wondering why that <executions> element is there. That is so that you can run the same goal multiple times with different configuration if needed.
   - 对于可以使用亦可使用`<phase>`tag声明`goal`运行的phase（阶段）（即将goal和phase绑定）
example:
     ```
     <plugin><!-- tips: modello only on generate-source phase -->
         <groupID>com.mycompany.example</groupId>
         <artifactId>display-maven-plugin</artifactId>
         <version>1.0</version>
         <executions>
             <exectuion>
                 <phase>process-test-resources</phase>
                 <goals>
                     <goal>java</goal>
                 </goals>
             </exectuion>
         </executions>
     </pulgin>
     ```
关于plugin的详细配置，见下"Configuring Plugins"

## *pom.xml - A basic file of all Maven project
pom.xml包含了整个项目POM（project object model），**POM(`pom.xml`)是整个Maven项目的基础**

## *Dependency-依赖项 - [Introduction to dependency mechanism](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)

### i. Transitive Dependencies-传递依赖
- 对于各个依赖项之间存在传递依赖——即通过项与项之间的依赖以实现传递依赖(Transivate dependencies)，不需要额外的声明即可使用存在于依赖树的依赖项。如：`A->B->C`，使用A的项目可以使用依赖项C。
- 可以理解为，在一个project项目中使用了依赖项A，若存在C为A的传间接依赖，则可以在项目中无声明C的情况下使用依赖项C


### ii. 在`pom.xml`中使用external dependencies（外部依赖项）
- 在`pom.xml`中定义，在define定义时**至少**使用4个tag：

	- `groupId`

	- `artifacitId`
	- `version`
	- `scope`（虽说至少，可不声明的，不声明则为默认值，详见下 iii）

	于`<dependencies>`(`<project>`标签的下一层级标签)中使用`<dependency>`tag
```
<project>
  ...
  <dependencies>
    <dependency>
      <groupId>group-c</groupId>
      <artifactId>artifact-b</artifactId>
      <version>1.0</version>
      <type>war</type>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-b</artifactId>
      <version>1.0</version>
      <type>bar</type>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project>
```

> For each external dependency, you'll need to define at least 4 things: groupId, artifactId, version, and scope. The groupId, artifactId, and version are the same as those given in the pom.xml for the project that built that dependency. The scope element indicates how your project uses that dependency, and can be values like compile, test, and runtime


### iii. 多个项目之间的依赖会产生传递性依赖
在产生传递依赖时，会产生依赖树（dependency tree），如`A->B->C->D`

- *依赖调节* (Dependency mediation)：用于调节版本，在依赖树中，若出现依赖树中不同的子树中依赖项的版本不同，则Maven会使用**距离根最近**的依赖项的版本，并不以版本号的高低做判断，如：两棵依赖子树`A->B->C->D ver2.0`和`A->E->D ver 1.0`，Maven将会使用`D ver1.0`，因距离最近。若需要强制使用`D ver2.0`则需在`A`的`<dependency></dependecy>`中直接指明

- *依赖关系管理* (Dependency management)：允许项目的使用者直接指定在直接依赖/间接依赖上的依赖项版本问题。如上列所述的强制使用`D ver2.0`，尽管`D ver2.0`不是由`A`直接依赖，但依旧可以申明，于`A`的`<dependecyManager>`tag中指定`<version>`
	- 尽管可以通过依赖传递来隐式实现依赖（如`A->B->C`，A隐式依赖C），但最好还是在将隐式依赖项在本体中声明，以防止出现B修改了对C的依赖而导致A对C的隐式依赖失效，导致最终的编译失效。

	  > Although transitive dependencies can implicitly include desired dependencies, it is a good practice to explicitly specify the dependencies you are directly using in your own source code. This best practice proves its value especially when the dependencies of your project changes their dependencies.

- *排除依赖项* (Excluded dependencies)（主动）：对于依赖树`A->B->C`，可在A中使用`<exclusion>`tag声明C以排除A对C的传递依赖（间接依赖）

- *可选择依赖* (optional dependencies)（被动）：用于取消传递/间接依赖，对于依赖关系`Y->Z`，可在Y中使用`<optional>`tag将Z标记为optional可选性。此时若有X依赖于Y，则X无法传递依赖/间接依赖于Z，但仍可以使用特别指明依赖项Z

- *依赖项范围*（dependency scope）url:[Dependency Scope](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope)：（使用`<scope>`标签声明范围，于`<dependency>`标签中）（作用域对应的传递性作用于指定的依赖项，即`<scope>`所在的`<dependency>`）Tips：作用域本义为：使得依赖项在作用域对应的阶段(phase)使用
	- *compile* 编译-compile (phase)：**默认范围，在没有指定范围时使用（声明）**。使用此范围的依赖项可传递，并且可在项目中的全部类路径(classpaths)中使用
	- *provided* 提供-compile (phase)：功能上与compile相似（但并不相同），在使用JDK或者容器(container)类依赖项时使用（声明），此范围仅允许在`compliation`编译和`test`测试的类路径(classpaths)中使用，声明此范围的依赖无法使用。
	- *runtime* 运行期：依赖项应用于执行期(execution)，仅可使用运行期(runtime)和测试(test)的类路径(classpaths)，不包含编译期(compile)类路径。
	- *test* 测试-test (phase)：使用此范围的依赖项仅适用于测试编译(test compilation)和执行(execution)阶段(phase)（此阶段为彼阶段），不具有传递性。
	- *system* ：此范围和`povided`相似，但需要此依赖项为`jar`文件且要在`pom.xml`中指定此依赖项所需的jar文件地址，于`<systemPath>`标签中（如：`<systemPath>${basedir}\src\lib\ldapjsk.jar</systemPath>`）。适用于依赖项于地址`lib`的文件（但一般直接将需要的jar包使用`mvn install`即可直接在`pom.xml`中声明）。
	- *import*
- 对于不同的依赖项范围，会以不同的方式影响依赖的传递性，如下表所示：
  上行为传递依赖项的作用域，左行为依赖项的作用域，中间为最终作用域（但作用域作用于谁？待解决）


||compile|provided|runtime|test|
|-|-|-|-|-|
|compile|compile(*)|-|runtime|-|
|provided|provided|-|provided|-|
|runtime|runtime|-|runtime|-|
|test|test|-|test|-|

***

### iv. Dependency Management
此节将介绍Dependency Management: `<dependencyManagement>`标签。

#### a. 此标签适用于简化子项目依赖项的声明，
- 若在父项目的pom中声明了`<dependencyManagement>`标签，其中包含了依赖项的基本信息：`<groupId>`, `<artificId>`, `<version>`,`<scope>`；以及附加信息：`<exclusion>`,`<type>`时。
- 子项目pom只需声明对应依赖项的`<groupId>`, `<artificId>`即获得父项目pom中对应依赖项所声明的项
	

  如下所示范（example）：
```
<!-- Father pom.xml file, 父项目pom.xml文件 -->
<project>
  ...
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>group-a</groupId>
        <artifactId>artifact-a</artifactId>
        <version>1.0</version>
        <scope>runtime</scope>
        <type>war</type> <!-- !!!Attention!!! Here we use a <type> tag! -->
 
        <exclusions>
          <exclusion>
            <groupId>group-c</groupId>
            <artifactId>excluded-artifact</artifactId>
          </exclusion>
        </exclusions>
      </dependency>
    </dependecies>
</project>
```
```
<!-- Son pom.xml file, 子项目pom.xml文件 -->
<project>
  <dependencies>
    <dependency>
      <groupId>group-a</groupId>
      <artifactId>artifact-a</artifactId>
    </dependency>
  <dependencies>
</project>
```
**注意**，父项目的`pom.xml`中用到了`<type>`标签

先说明对于`<dependencyManagement>`的标签最小声明应为: 

- `<groupId>`

- `<artifactId>`
- `<type>`
- `<classifier>`。

1. 其中`<type>`指代依赖项所对应的打包“制品”(artifacts)，在默认情况，即使用jar时无需声明；
2. `<classifier>`所指代的为分类器，在默认情况时（其值`null`）无需使用`<classifier>`。

由`<type>`的原因：依赖项将引用打包方式的“制品”(artifacts)，大多数引用jar的artifact，但仍然存在部分依赖项引用其他打包方式的artifacts，比如上述例子中的依赖引用`war`打包方式。

#### b. `<dependencyManagement>`最主要用于控制依赖版本，**主要功能**！
- **子项目依赖项的版本将由父项目pom管理，无需各子项目额外声明**。如上例子所示，在父pom的`<dependencyManagement>`中声明`<version>`后，子pom的依赖项版本对应父pom所声明的。

- 若在**子项目pom中声明了依赖项版本**，则Maven会**以子项目声明的为准**
- 若子项目pom中声明了新`<dependncyManagement>`并声明了对应依赖项的新特性（如版本），则只覆盖父项目`<dependencymanagement>`中对应的依赖项，其余不变，且继承子项目的项目将受到新`<dependencyManage>`的影响。

父pom：
```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>maven</maven>
  <artifactId>A</artifactId>
  <packing>pom</packing>
  <name>A</name>
  <version>1.0-SNAPSHOT</vresion>
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>test</groupId>
        <artifactId>a</artifactId>
        <version>1.1</version>
        
        <exclusions>
           <exclusion>
             <groupId>group-c</groupid>
             <artifactId>excluded-artfact</artifactId>
           </exclusion>
        </exclusions>
      </dependency>
    </dependencies>
  </dependecyManagement>
...
</project>
```

子pom：
```
<project>
  <parent>
    <artifactId>A</artifactId>
    <groupId>maven</groupId>
    <version>1.0-SNAPSHOt</version>
  </parent>
...
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>test</groupId>
        <artifactId>a</artifactId>
        <version>3.0</version><!-- change!!! -->
        
        <!-- change!!! -->

      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies><!-- 将会以本pom中声明的<dependencyManagement>为准 -->
    ...
  </dependencies>
</project>
```

## *POM中的tag（标签）
- project: main tag ==>[maven pom tag](http://maven.apache.org/ref/3.6.1/maven-model/maven.html#class_project)

## *Configuring Plugins - 配置插件

### 声明plugin

在maven中，分为`build`插件和`report`插件
- `build`：将会在项目构建期间执行（即完整的声明周期），需要在pom中使用`<build/>`标签声明
- `report`：将在站点生成期间执行，需要使用`<reporting/>`标签执行。

声明plugin至少需要，于pom中：
1. `<groupId/>`
2. `<artifactId/>`
3. `<version/>`

使用`<build>`/`<reproting>`, `<plugins>`, `<plugin>`标签包装
```
    <build>
        <plugins>
            <plugin>
                <artifactId>test-plugin</artifactId>
                <groupId>com.esolmio.plugin</groupId>
                <version>1.0-SNAPSHOT</version>
            </plugin>
        </plugins>
    </build>
```

### 自定义plugin -[Guide java plugin development](https://maven.apache.org/guides/plugin/guide-java-plugin-development.html)
1. *创建Mojo，Mojo全名为“**M**aven-**O**ld-**J**ava-**O**bject”*。
	1. 创建Mojo需要用到(`groupId:artifactId`)`org.apache.maven:maven-plugin-api`, `org.apache.maven.plugin-tools:maven-plugin-annotations`依赖项。
	
	2. 一个插件中只需要**一个Mojo**类（有些插件将Mojo定义为抽象类）：

		a. Mojo类需要继承`org.apache.maven:maven-plugin-api`下的包`org.apache.maven.plugin`中的抽象类`AbstractMojo`，故在Mojo类中需要实现`execute():void`方法。

		b. 必须声明`@Mojo(name="...")`，此注解控制Mojo的使用（亦可使用自带的注解并声明`@goal`等注释）。

		c. 可以使用`getLog()`方法于`execute()`中以实现在cmd中输出需要的log信息。

	3. 在pom中声明打包方式`<packaging>`为`maven-plugin`
	4. 将插件执行`maven-plugin`的声明周期(`generate-resources`, `process-resources`, `compile`) [Build Lifecycle](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
	
2. *执行插件*
	1. 为了顺利执行，一定要将其编译`compile`=>打包`package`=>安装`install`

	2. 随后在需要的项目pom中声明本插件（亦可在插件的pom中声明，方便直接测试？（可使用test））
	3. 在对应的项目根目录下使用cmd`mvn artifactId:groupId:version:goal`执行插件
	
**对于plugin的执行，Maven只会使用继承`AbstractMojo`或实现`Mojo`的类，且这些类只有一个**（再次强调）

3. *使用指定plugin的前缀Prefix*
	
	1. Maven会根据插件的artifactId猜测指定的前缀，将artifactId中的`maven`, `plugin`以及连字符"-"删除，如：
		1. `maven-${prefix}-plugin`,  Maven官方插件, 剩下`${prefix}`。
		2. `${prefix}-maven-plugin`, 第三方/用户插件, 剩下`${prefix}`。
	2. 倘若不遵行标准的`artifactId`命名方式，则需要在插件的pom中声明`maven-plugin-plugin`依赖项，并使用`<goalPrefix>`标签独立声明前缀。（注意，修改后需要重新package并install）
		1. 使用`<plugin>`-`<configuration>`-`<goalPrefix>`。
	3. 执行时使用`mvn selfPrefix:goal`。
	4. 此方法需要额外多声明一个插件: `maven-plugin-plugin`，若本地仓库无此插件可以先在依赖中声明。
	5. 成功后，在本地仓库，自定义插件的文件夹，`maven-metadata-local.xml`文件中将会多出自定义声明的前缀。
```
<!-- EXAMPLE, 例子, 于"<project>"-"<build>"-“plugins”之下 -->
<plugin>
  <artifactId>maven-plugin-plugin</artifactId>
  <version>2.3</version>
    <configuration>
      ...
      <goalPrefix>somePrefix</goalPrefix>
    </configuration>
</plugin>
```
4. *为Mojo配置参数*
	1. 所有的参数声明均位于pom的`<configuration>`标签中
	2. 在Mojo中的声明：
		1. 使用javadoc注释`@parameter`于每个field的上方
		   ```
		   @parameter
		   private List animals;
		   ``` 
	3. 参数在pom中的声明：
		1. 声明时的标签名与Mojo中声明field名称相同；
		2. 倘若时集合类，则需要在Mojo中的名称后加上"s"，在pom中声明时需要使用`<xxs><xx>vaule1</xx><xx>value2</xx></xxs>`的方式；
		3. 对于Map类的field，则声明方式为:
		   ```
		   <myMap>
		     <key1>value1</key1>
		     <key2>value2</key2>
		   </myMap>
		   ```

5. *配置plugin执行期的参数：*
	1. 关于plugin的执行期参数配置，均存在于`<plugin>`下的`<executions>`标签内，如：`<goals>`, `<phase>`, `<configuration>`（此标签比较特殊，亦可以不在`<exectuions>`的标签内声明而在`<plugin>`中直接声明）
	2. `<phase>`标签声明了plugin的执行阶段。
		- 在命令行输入指定的阶段名称后，会执行项目pom中指定的打包方式所指定的运行插件以及在指定阶段内的自定义插件

## *Resources
- Maven中的Resources位置可以放置指定的properties-Java属性文件，使用时需要在`pom.xml`文件中先设定`<resource>`标签并在`<directory>`标签中指定resources的位置。
- 位于指定的Resources文件夹中的文件将会在打包为jar时放置在jar的根目录中。
- 故若需要META-INF文件夹则需要事先指定的Resources文件夹中创建好。

## About `<Properties>` section & `<filters>` section

Example URL : [Filter](https://maven.apache.org/plugins/maven-resources-plugin/examples/filter.html)

- 在使用`<resources>`时，启用`<filter>`标签同时设置为`true`时，将会开启过滤，此时`/resources`文件夹中的文件**文本**中若带有`${...}`(...为指定的属性名)，则在使用maven命令`mvn resources:resources`时将会将其替换为`<properties>`中对应属性名的属性值。
- 也可以在maven命令中使用`-D...`指定属性名`...`的属性值：`maven resources:resources -D...="xxx"`。
  
  Example：Tips-resources的目录路径可以自定义。
  ```
  file text in "src/main/resources/hello.txt": Hello ${name}

  maven command: maven resources:resources -Dname="world"

  output in "target/classes/hello.txt" content: Hello World 

  ```

