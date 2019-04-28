# Maven
maven是一个将项目视为对象的管理工具（Porject Object Model，POM）
在整个Maven项目中，最基本的文件为`pom.xml`，此文件用于指明`reprository`, `dependencies`, `plugins`

## Lifecycle
1. maven的lifecycle（生命周期）由多个phase（阶段）组成：
	- validate 验证
	- compile 编译
	- test 测试
	- package 打包
	- verify 校验
	- install 安装至repository
	- deploy 部署

   上述所说的几项即在命令行中需要


2. 上述