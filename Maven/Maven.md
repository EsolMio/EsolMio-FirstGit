# Maven
maven是一个将项目视为对象的管理工具（Porject Object Model，POM）
在整个Maven项目中，最基本的文件为`pom.xml`，此文件用于指明`reprository`, `dependencies`, `plugins`

## Lifecycle
1. maven的lifecycle（生命周期）由多个phase（阶段）组成，一下为简述，会因为打包方式的不同而改变：
	- validate 验证
	- compile 编译
	- test 测试
	- package 打包
	- verify 校验
	- install 安装至repository
	- deploy 部署

   上述所说的几项即在命令行中需要输入以执行的语句，即对于一个新的项目，需要按照上述的顺序执行上述的语句。




2. 对于不同的打包方式有不同的lifecycle，且会bind绑定一串`goals`在particular指定的phase上。以下示范`jar`：

|Phase|plugin:goal|
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
