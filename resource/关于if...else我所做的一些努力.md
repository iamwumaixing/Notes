> 在任何语言中，都有if...else语句，它作为一个非常重要的分支判断语句，很多功能都需要依靠它作为基础来实现。最近在项目中，碰到了一个多分支的场景，如果纯粹使用if...else，就会使代码的可读性变得很差，一长段一长段的代码，看着都让人头大，因此，我上Google搜索了多分支if...else的优化方案，发现有许多人跟我有着一样的疑惑，在浏览了论坛的讨论和一些博客之后，总结出了几个关于if...else可行的做法

#### If...else

```java
if (condition1) {
	// todo
} else if (condition2) {
	// todo
} else if (condition3) {
	// todo
} ...
```

- if...else 可以更好地适应复杂的条件判断，但是如果前面的条件无法匹配，需要经过多次判断(!condition1 -> !condition2 -> condition3 -> todo)
- 在条件分支不是很多，每个分支代码量也不是很多的情况下比较适合使用

#### switch...case

```java
switch (type) {
	case condition1:
	// todo
	case condition2:
	// todo
	case condition3:
	// todo
}
```

- switch可以匹配的表达式：byte\short\char\int\枚举\String
- case可以匹配的表达式：常量表达式\枚举常量
- switch...case 底层的实现是通过跳转表(jump table)来实现的
- 在多分支的情况下，相较于if...else的类型，不需要多次判断，只匹配一次即可(虽然这点差距影响微乎其微)，同时在分支多的情况下，switch...case的代码可读性更强

#### 策略模式

[关于策略模式，可以看看这篇博客](https://juejin.im/post/5d12228de51d45775c73dd1b)

#### 责任链模式

```java
// 以下场景
if (condition1) {
	// todo
	if (condition2) {
		// todo
		if (condition3) {
			// todo
		}
	}
} else {
	// todo
}
```

- 符合条件 condition1 -> 判断 condition2 -> 符合条件 condition2 -> 判断 condition3，针对这种情况，如果不进行优化，会使代码的可读性变得很差（估计回过头来看自己写的代码，也会有点懵逼）

- 这种情况下，适合使用责任链模式(职责链模式)

- [关于责任链模式，可以看看这篇博客](https://blog.csdn.net/lovelion/article/details/7420891)

  这是《Java设计模式》的作者所写的博客，也是我目前看过最好的入门各种设计模式的博客

> 对于使用某种模式或者方法，并没有死板地规定，说到底做这些工作都是为了让自己写的代码给别人看起来能够更加易懂更快上手，所以符合当前项目实际的做法就是最好的做法，明白多一种手段就有多一种选择，而如何使用也是需要经验来判断的，以我目前的经验来说还不足以做到这样，所以记录下这些"手段"，通过实践让我慢慢朝着能够灵活地使用它们而努力。

参考博客：https://www.cnblogs.com/eric-shao/p/10115577.html