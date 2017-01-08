<documentProperties pageTitle =“学习FuseJS”/>

＃ 介绍

$（FuseJS）是一个用于编写跨平台移动应用程序业务逻辑的$（JavaScript）框架。

注意：FuseJS目前[不支持WebGL导出]（/ learn / guides / previewandexport / other）。

＃＃ 入门

FuseJS可以通过`<JavaScript>`标记在@（UX Markup）中使用，通过指向外部JavaScript文件，如下所示：

	<JavaScript File =“SomeCode.js”/>

或者通过在代码中内嵌JavaScript代码，如下所示：

	<JavaScript>
		console.log（“Hello，FuseJS！”）;
	</ JavaScript>

##模块

FuseJS实施<a href="http://www.commonjs.org/"> CommonJS </a>模块系统。每个代码文件或内联片段是一个_module_。

对于模块内部的内部可见的事物，我们使用`module.exports`-结构：

	<JavaScript>
		module.exports = {
			exportSymbol：“你好，世界其他地方！
		};
	</ JavaScript>

无法从模块导出将无法达到模块中定义的数据：

	<JavaScript>
		var data = [1，2，3];
		var invisible =“I'm invisible”;

		module.exports = {
			data：data
		};
	</ JavaScript>

这对于从其他调用JavaScript模块和UX代码隐藏实现细节很好。

> ##导入模块

每个代码文件（或内联片段）是一个模块。

要通过`$（require）`使一个模块可用于其他模块，使用`ux：Global`属性标记`<JavaScript>`标记。

	<JavaScript File =“someJavaScriptFile.js”ux：Global =“MyModule”/>

然后，您可以在同一项目中的任何其他模块中访问此模块，如下所示：

	var myModule = require（'MyModule'）;

>注意：目前不可能仅通过文件路径包含来自JS的模块。我们在这，谢谢你的耐心！

> ##按文件名导入模块

您还可以按文件名导入JavaScript模块。为此，请确保您的JavaScript文件作为“Bundle”文件包含在.unoproj文件中：

```
“包括”：[
	“yourJavaScriptFile.js：Bundle”
	..其他文件 ..
]]
```

或者如果要使所有JavaScript文件作为捆绑文件包含：

```
“包括”：[
	“**。js：Bundle”
]]
```

然后，您可以要求使用JavaScript文件名：

```
var myModule = require（'/ someJavaScriptFile.js'）;
```

注意，用“/”作为文件名前缀意味着我们正在寻找相对于项目根目录的文件。要为相对于当前文件的文件命名，使用“./”作为前缀。通过省略前缀，文件名称相对于项目根或其所在的全局模块。

```
var relativeToProjectRoot = require（'/ SomeComponent.js'）;
var relativeFile = require（'./ MainView.js'）;
var relativeToRootOrGlobalModule = require（'SomeOtherComponent.js'）;
```

*请注意，如果您愿意，您可以省略文件名中的.js文件扩展名


例子：

* <a href="https://www.fusetools.com/developers/examples/todoparseexample">带有Parse后端示例的TODO应用</a>

##设计和动机

FuseJS的关键设计目标是保持你的JS代码小，干净，只关心你的应用程序的实际功能。与此同时
所有面向UX的，例如布局，数据呈现，动画和姿态响应留给声明性$（UX标记）和本地UI组件。

Fuse将JavaScript业务逻辑与UX标记表示分开的方式有一些明显的好处：

*性能 - 所有性能关键位都在本机代码中基于本机UI组件进行处理。
*轻松 - 声明性代码即使在有限的编程知识的情况下也易于阅读，编写和理解
*更少的错误倾向 - 更少的状态意味着更少的事情可能出错
  *可视化工具 -  UX标记可以通过保险丝工具编辑，如检查员，时间线和一般很酷的拖放工具。

注意，Fuse有大量声明性API（设计的UX标记），它们替代了从JavaScript控制动画的需要（即强制）。

许多其他JavaScript框架将命令式UI代码，动画和性能关键任务组合到JavaScript中，因此许多FUSEJS新手都倾向于尝试
一开始就这样做。虽然大多数这些东西在技术上是可能的在FuseJS，它是不鼓励。我们建议服用一些
时间来研究保险丝的例子来感受新的做事方式。

通过将视图和逻辑分离为UX标记和JavaScript来净化代码可以显着缩减代码库，使其更易于维护，并允许
UX设计师和开发人员之间更有效的协作。

如果你需要编写性能关键的业务逻辑，我们建议在本地代码或者替代@（Uno）代码，而不是JS。
