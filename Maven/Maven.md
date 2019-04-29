# Maven
maven是一个将项目视为对象的管理工具（Porject Object Model，POM）
在整个Maven项目中，最基本的文件为`pom.xml`，此文件用于指明`reprository`, `dependencies`, `plugins`

## 暂时未归类项目记录
1. 若需要将resources加入`jar`包中，需要将文件放置在`${basicdir}/src/main/resources`目录下，并在命令行中执行`mvn package`或者``

## Lifecycle
maven的lifecycle（生命周期）由多个phase（阶段）组成，一下为简述，会因为打包方式的不同而改变：
- validate 验证
- compile 编译
- test 测试
- package 打包
- verify 校验
- install 安装至repository
- deploy 部署

   上述所说的几项即在命令行中需要输入以执行的语句，即对于一个新的项目，需要按照上述的顺序执行上述的语句。

使用Build Lifecycle（构建项目时的生命周期）有两种方式：1. 使用打包方式的goal（其绑定了不同的阶段）2. 配置plugin（插件）

- 不同的打包方式有不同的goal绑定在特定的phase上。以下示范`jar`：

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

   需按照以上顺序执行，顺序执行下来为一个生命周期（不确定）

- 配置plugin:
  > The goals that are configured will be added to the goals already bound to the lifecycle from the packaging selected. If more than one goal is bound to a particular phase, the order used is that those from the packaging are executed first, followed by those configured in the POM. 
  
   如果有多个goal绑定到同一个phase，会先执行指定打包方式所默认绑定的goals，followed（其次）再执行配置的goals

## pom.xml - A basic file of all Maven project
pom.xml包含了整个项目POM（project object model），POM是整个Maven项目的基础

### 1. 使用external dependencies（外部依赖项）