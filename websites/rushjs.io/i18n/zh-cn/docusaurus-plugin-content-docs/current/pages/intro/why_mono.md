---
title: 为什么用一个大仓库？！
---

_开源的 NPM 包看起来是在许多小的 GitHub 仓库中开发的。我不应该这么做吗？_

如果你正在构建毫无关系的组件，并且它们不适合整个在一起，当然可以。但是商业软件并不是这样的。它更像这样：

大多数人开始构建一个 web 应用，而不是一堆库，当你的应用发布后，它的体积会逐渐增大， 直到有一天你需要在一些不同的仓库内共享一些代码，此时你发现代码已经乱成一窝，又需要花费时间重构！

显然，你必须将这些分割为可管理的组件。在 JavaScript 代码中，NPM 包是一种解决问题的方案。但查阅后才发现，NPM 似乎要求**一个 GitHub 仓库对应一个 NPM 包。**于是在一周乃至数周内，你创建了 10 个 Git 仓库来分割你的代码，并试着使用它们...

...但是使用 10 个 Git 仓库来管理代码发现出一些让人头疼的问题：

- **一孔之见**：如果一个同事大部分时间在 5 号和 6 号仓库工作，他们看起来完全忽略了来自其他 8 个仓库的 pull requests。每天都会有新的仓库出现，而你甚至却不知道它们的存在。

- **层级式的发版**： 从 **lib3** 发布一个修复到你的应用项目需要更新/构建/发布许多 Git 仓库，顺序是：**lib3** --> **lib2** --> **lib1** --> **application**. 当 **lib3** 变动频繁时，上述过程会变的异常繁琐。人们如何记住正确的发版顺序？互联网上有地方可以询问这个问题，但是你人力有限，别人也很忙。

- **下游的受害者**：当 Bob 在 **lib3** 上做了一些改动并发布，所有的下游项目需要花费一定的时间才能升级并使用它。如果升级版本存在问题，那么可能是一周后 Alice 在 **lib1** 中尝试 "npm update" 时发现的。那时，Bob 可以能动身去欧洲旅行了。为什么 Alice 要修复其他人升级导致的问题？貌似每次升级都会存在破坏性变动！

- **疯狂的链接**：为了直接测试 **lib3** 的变动，解决方法看起来是使用 [npm link](https://docs.npmjs.com/cli/link) 将你的**应用**和 **lib3** 链接起来。但是 NPM 会创建一个全局范围内的符号连接，如果在同一台电脑上多个存在 **lib3** 多个分支，那么就会有问题。而且有 10+ 个库，很难记录哪两个库需要链接起来。

**一个库一个包**的方式对于陌生人之间维护孤立的项目是很有意义的（同样，这些库的更新频率也比较低，因此上述问题也更容易解决）。但是在我们的示例中，所有人都在同一个公司工作，这些库更多地扮演体系内的一个组件。代码会频繁的变动，某处的变动很可能破坏系统的另外某部分。将多个项目集合起来构建，可以让你在每个变动中运行所有的单元测试，这样可以将修复问题的责任转移到起初更改代码的人身上。

于是，**一个 Git 仓库一个团队**的方式开始出现了，更好的描述是**尽可能少的 Git 仓库，以达到工作完成的目的**。

<div>
  <img src="/images/home/mono-concept-h.svg" alt="monorepo block diagram" style={{ width: '50rem' }} />
  <p />
</div>

[Lots](https://danluu.com/monorepo/) [of](https://medium.com/@bebraw/the-case-for-monorepos-907c1361708a) [people](http://blog.shippable.com/our-journey-to-microservices-and-a-mono-repository) 许多开发大型业务软件的人，最终似乎都把所有代码都放在一个大的 "monorepo" 中。JavaScript 是最后一个加入该方式的语言。

monorepo 策略下最大的担忧是显著的**_构建耗时_**。JavaScript 工具链明显比编译性语言慢一些，如果一个工程花费一分钟去构建，那么假如你有 75 个工程，理论上将有 75 分钟被构建占据。这似乎令人生畏，但是使用工业级的工具链可以让你在将构建时间视为一个问题前走很多。Rush 和 **gulp-core-build** 在规划上对构建耗时非常看重，而且我们乐观的认为这里依然有很大的优化空间。使用子集构建或增量构建，在理论上可以避免重新构建所有工程，除非某处变动真的影响了所有内容 —— 对于这种变动，为了找到其中的破坏性改动而等待更长时间的构建，二者很难说哪个更值得些。