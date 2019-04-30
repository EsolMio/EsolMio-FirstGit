# Maven
maven是一个将项目视为对象的管理工具（Porject Object Model，POM）
在整个Maven项目中，最基本的文件为`pom.xml`，此文件用于指明`reprository`, `dependencies`, `plugins`

## 暂时未归类项目记录
1. 若需要将resources加入`jar`包中，需要将文件放置在`${basicdir}/src/main/resources`目录下，并在命令行中执行`mvn package`或者`mvn jar:jar`（在打包方式为`jar`时使用）
2. 在命令行输入`mvn ...`时，均是执行对应的插件plugin（对应的goal）。
3. 对于不同的打包方式有对应的dependency

## Lifecycle

### Introduce phase & use

1. maven的lifecycle（生命周期）由多个phase（阶段）组成，一下为简述，会因为打包方式的不同而改变：
	- validate 验证
	- compile 编译，对应`src/main/java`
	- test 测试，对应`src/test/java`
	- package 打包，对应`target/classes`
	- verify 校验
	- install 安装至repository
	- deploy 部署

   上述所说的几项即在命令行中需要输入以执行的语句，即对于一个新的项目，需要按照上述的顺序执行上述的语句。

2. 使用Build Lifecycle（构建项目时的生命周期）有两种方式：1. 在命令行中使用每个阶段phase的默认名称 2. 配置plugin（插件）的goals并在命令行中使用

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
   - 使用`<executions>`，`<goals>`tag以实现对plugin的配置:
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
## pom.xml - A basic file of all Maven project
pom.xml包含了整个项目POM（project object model），**POM(`pom.xml`)是整个Maven项目的基础**


### 1. 在`p`使用external dependencies（外部依赖项）
在`pom.xml`中定义，在define（定义）时至少使用4个tag：`groupid`, `artifacitid`, `version`, `scope`，于`<dependencies>`(`<project>`标签的下一层级标签)中使用`<dependency>`tag
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

#### i. 多个项目之间的依赖会产生传递性依赖
在产生传递依赖时，会产生依赖树（dependency tree），如`A->B->C->D`:A依赖于(dependen on)B，B依赖于C，C依赖于D，A为此依赖树的根(root)
- *依赖调节* (Dependency mediation)：用于调节版本，在依赖树中，若出现依赖树中不同的子树中依赖项的版本不同，则Maven会使用**距离根最近**的依赖项的版本，并不以版本号的高低做判断，如：两棵依赖子树`A->B->C->D ver2.0`和`A->E->D ver 1.0`，Maven将会使用`D ver1.0`，因距离最近。若需要强制使用`D ver2.0`则需在`A`的`<dependency></dependecy>`中直接指明

- *依赖关系管理* (Dependency management)：允许项目的使用者直接指定在直接依赖/间接依赖上的依赖项版本问题。如上列所述的强制使用`D ver2.0`，尽管`D ver2.0`不是由`A`直接依赖，但依旧可以申明，于`A`的`<dependecyManager>`tag中指定`<version>`
	- 尽管可以通过依赖传递来隐式实现依赖（如`A->B->C`，A隐式依赖C），但最好还是在将隐式依赖项在本体中声明，以防止出现B修改了对C的依赖而导致A对C的隐式依赖失效，导致最终的编译失效。

	  > Although transitive dependencies can implicitly include desired dependencies, it is a good practice to explicitly specify the dependencies you are directly using in your own source code. This best practice proves its value especially when the dependencies of your project changes their dependencies.

- *排除依赖项* (Excluded dependencies)（主动）：对于依赖树`A->B->C`，可在A中使用`<exclusion>`tag声明C以排除A对C的传递依赖（间接依赖）

- *可选择依赖* (optional dependencies)（被动）：用于取消传递/间接依赖，对于依赖关系`Y->Z`，可在Y中使用`<optional>`tag将Z标记为optional可选性。此时若有X依赖于Y，则X无法传递依赖/间接依赖于Z，但仍可以使用特别指明依赖项Z

- 依赖项范围（dependency scope）url:[Dependency Scope](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope)：
	- compile：默认范围，在没有指定范围时使用。
	- provided
	- runtime
	- test
	- system
	- import
- 

