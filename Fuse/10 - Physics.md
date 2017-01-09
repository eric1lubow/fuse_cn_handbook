# $(Physics)

一些类型的动画([这样的一个](https://www.fusetools.com/developers/examples/swipeplaces))最好描述使用物理模拟。保险丝带有一组基于物理的触发和行为，可用于这些情况。

_Note:当使用物理，确保你的@(项目结构:项目)有一个引用Fuse.Physics包。

## $(物理规则)

物理模拟通过使用不同类型的物理行为来创建空间来实现，其中元素以各种方式动态地受到影响。每个物理行为知道如何模拟某种力量，以及如何将其应用于动画的每个帧。受影响的元素可以一次响应多个力。我们可以通过使用相关的@(触发器:触发器)基于某些物理事件对动画添加额外的自定义动画。物理行为被分类

### $(Draggable)

`Draggable'行为允许你使用指针通过拖动来移动一个@(Element)。

```
<面板>
	<Rectangle Width =“100”Height =“100”Fill =“Red”>
		<Draggable />
	</ Rectangle>
</ Panel>
```

#### $(WhileDragging)

`WhileDragging`触发器在@(元素)被拖动时是活动的，而当它被释放时被取消激活。

```
<面板>
	<Rectangle Background =“Blue”Width =“50”Height =“100”>
		<Draggable />
		<WhileDragging>
			<旋转角度=“90”持续时间=“0.8”
			        Easing =“BounceOut”EasingBack =“BounceIn”/>
		</ WhileDragging>
	</ Rectangle>
</ Panel>
```

## $(强制字段)
力场是物理行为，持续影响其定义的影响范围内的元素。

#### $(PointAttractor)

'PointAtractor'用于创建一个圆形的影响区域，将空闲@(Draggable:draggable)@(Element:elements)拉向其中心。

* 属性:*
-  Offset  - 设置PointAtractor相对于其容器的位置
- 半径 - 从吸引子将产生效果的点的半径。
- 力量 - 对受影响的元素施加多少吸引力。
- 独占 - 当被认为是最强的@(物理规则:规则)施加力时，这个吸引子是否将被独占应用。

多个PointAtractors可以重叠，创建潜在的有趣的行为。

[Swipe places](https://www.fusetools.com/examples/swipe-places)示例是使用中的“PointAttractor”的一个很好的示例。

<!--  ### $(Spring)
使用Spring @(Rule:rule)

- 目标
- 长度
- 刚度
作者:Buggy？
-->

### $(强制字段触发器)

强制字段触发器根据其父@(元素)是否受强制字段影响而激活。

* 常用属性:*
- `ForceField`  - 输入激活此@(触发器)所需的力字段。

#### $(EnteredForceField)

`EnteredForceField`是一个脉冲触发器，当@(元素)进入指定的`ForceField`时激活。
作为脉冲触发器，意味着触发器内的@(动画:动画)/ @(动作:动作)将在连续播放中向前播放，然后向后播放。您可以修改`Threshold`属性来指定@(Element)必须是多么接近激活触发器，与所讨论的力场的Radius属性一致。

#### $(ExitedForceField)

`ExitedForceField`工作原理像@(EnteredForceField)，但激活元素_leaving_指定的`ForceField`。

#### $(InForceFieldAnimation)

`InForceFieldAnimation`用于在进入力场时对@(Element:elements)进行动画处理。您可以修改“From”和“To”属性以控制沿着力场中心到其外边缘的距离的范围，动画应该出现在该外边缘。

- “From”和“To” -  0和1之间的数字，0是力场的边缘，1是中心的。

	<面板>
		<面板对齐=“顶部”>
			<Rectangle Background =“Blue”Width =“50”Height =“100”Margin =“0,100”>
				<Draggable />
				<InForceFieldAnimation ForceField =“attractor”From =“0.5”To =“1”>
					<Rotate Degrees =“360”/>
				</ InForceFieldAnimation>
				<InForceFieldAnimation ForceField =“attractor”From =“0”To =“1”>
					<Change circleColor.Color =“#f00”/>
				</ InForceFieldAnimation>
			</ Rectangle>
		</ Panel>
		<面板对齐=“BottomCenter”MaxHeight =“10000”MaxWidth =“10000”
		       Height =“800”Width =“800”Y =“50％”>
			<面板>
				<PointAtractor ux:Name =“attractor”Radius =“400”Strength =“200”/>
			</ Panel>
			<Circle>
				<SolidColor ux:Name =“circleColor”Color =“#ddd”/>
			</ Circle>
		</ Panel>
	</ Panel>
