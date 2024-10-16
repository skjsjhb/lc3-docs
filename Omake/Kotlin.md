# Kotlin：集效率和优雅于一体的艺术品

> 从 Dev C++ 中的第一行 `hello, world` 开始，到字符串和数组，再到文件输入输出，你已经历许多。现在，是时候使用一门更现代化的语言续接你的探索了，从黑暗神秘的终端，到灵活多变的 Web 时代。

在这篇外传里，我们将以 C 为基础，向各位读者介绍 Kotlin 这样一门“艺术品”般的现代化编程语言。有关 Kotlin 的优点和缺陷我不打算在这里花费太多篇幅说明，[Reddit](https://www.reddit.com/r/Kotlin/comments/1b0bpru/is_kotlin_worth_to_learn_in_2024/) 以及其它你能通过 Google 或其它搜索引擎找到的网站上已经有了足够多的建议。Kotlin 简洁而优雅，却和 Java 同样健壮而强大。在 Google 将其列为 Android 的官方开发语言之后，Kotlin 的关注度与日俱增。

## 本文中的标记

我们会使用以下标记来增强文本的可读性：

?> 提示标记，进行一些短小提示的补充。

!> 警示标记，标注常见的错误或需要引起注意的其它理由。

> **到底怎么回事？**
> 
> 详细解释一些并不需要立即掌握的概念。

> **你是职业选手吗？**
> 
> 对具备专业水平的读者提供的进阶提示。

## 还就是那个经典的程序

以下是 C 语言中的经典 Hello World 程序（需要 C90 或更高版本）：

```c
#include <stdio.h>

int main(void) {
    puts("hello, world");
    return 0;
}
```

以下是 Kotlin 的同等版本：

```kotlin
fun main() {
    println("hello, world")
}
```

## Kotlin 开发环境

如同任何其它编程语言一样，要想开发 Kotlin 程序，你就需要搭设 Kotlin 的开发环境。或许你会有疑问，为什么在 Dev C++ 中没有做过类似的事情……

> **到底怎么回事？**
> 
> 任何一门编程语言，要生成最终可执行的程序，都绝不是仅仅靠语言本身，或是一个单一的编译器就能做到的。由源代码生成最终的产品（可能是可执行程序，也可能是支持库、可分发软件包等）被称为**构建（Build）**，你在 C 语言中已经接触到的编译和链接，就是 C 语言的构建流程（的一部分）。
> 
> 参与源代码的构建，需要使用数目众多的工具。例如，在我们的中央电脑上，用于构建 C 语言程序的工具和文件有：
> 
> - 编辑器：VSCodium 1.94.0，编辑器能让你更方便地创建和编辑源代码文件。
> 
> - 编译器：GCC 14.2.0，编译器将源代码转换为目标代码。
> 
> - 链接器：GNU LD 2.42，链接器将目标代码和库文件链接在一起，并加上相应的文件头，构成最终的可执行程序。
> 
> - 库文件：GCC Libs 14.2.0，库文件提供所需的头文件定义和用于链接的标准库。
> 
> - 构建工具：CMake 3.30.1 和 Ninja 1.12.1，构建工具能够管理各个文件编译的先后顺序，控制整个构建过程。
> 
> - 测试工具：Catch2 3.7.1，测试工具能运行自动化的单元测试，验证程序的功能和质量。
> 
> - 部署工具：Docker 2.29.6，部署工具将你的程序打包并以需要的形式交付给用户。
> 
> 这些工具和它们的配置统称为**软件开发工具包（Software Development Kit，SDK）**，也就是通常意义上的**环境**。
> 
> 在 Dev C++ 中，上述工具（或者功能相似的替代品）都已经内置了，你只需要按下运行按钮，软件就会自动使用这些工具来构建你的程序。这种集成了开发工具的软件，被称为~~轮椅~~**集成开发环境（IDE）**。

Kotlin 是一门基于 JVM 的语言（当然，后续我们会介绍 Kotlin 与其它平台的组合），后者是 Java 程序所用的运行环境，而 Java 的生态系统可谓是当今软件工程界最为强大的生态系统之一，每年都有各式各样的自动化工具被研发出来。这些工具是如此流行，以至于大多数 Java IDE 都内置了它们。如今，要开发 Kotlin（以及 Java、Scala 等）程序，除了 IDE 之外（那是理所当然！），就只需要额外安装一个 **Java 软件开发工具包（Java Development Kit，Java）**。JDK 中包含了一整套用于 Java 程序编译、运行、调试、性能分析、文档生成等用途的工具。

?> 前面已经提到，Kotlin 是一门基于 JVM 的语言，因此 Kotlin 的构建过程很大程度上利用着 Java 的工具。只有 Kotlin 编译器是个例外，需要另行获取 —— 这一点将由 IDE 为我们完成。

可用的 JDK 也有很多不同的分支，每个都有独特的功能，优缺点也不尽相同，但在业界上使用最广泛的，还是由 Eclipse 开发的 Adoptium OpenJDK。你可以从 [Adoptium 的官网](https://adoptium.net) 上下载到 JDK 的安装文件，也可以访问 [LUG 的镜像站](https://mirrors.ustc.edu.cn/adoptium/releases/)。在下载时，请确保你下载的是 JDK（开发工具）而不是 JRE（运行环境），并且版本号是 21。在安装时，请确保勾选了**“设置或重写 `JAVA_HOME` 变量”**选项。

?> 如果你同时还是一名 Minecraft 玩家，那么你的电脑上很可能已经有了 JDK 或 JRE，取决于设置，你可能需要配置你的启动器，以免它们自动选中新安装的开发工具用于游戏。

## 推荐使用？命令使用！

提到 Java（和 Kotlin） 的 IDE，这就不得不说到“轮椅级”的 IntelliJ IDEA 了。这款 IDE 由业界顶级的软件公司 JetBrains 开发（JetBrains 同时也针对其它编程语言开发很多同样优秀的 IDE），IDEA 非常强大，却也同样易用，在过去的许多 Java 教程中，作者通常都推荐使用 IDEA 来进行开发，而在近期的许多 Java 项目文档中，都把 IDEA 作为唯一的 IDE 选项。尽管 IDEA 的专业版价格不菲（每年大约 1200 人民币），但好在它同样有一个社区版，包含了绝大多数功能，而且是免费的。你可以从 [IntelliJ IDEA 下载界面](https://www.jetbrains.com/idea/download) 获取它，专业版在上，而社区版需要向下翻一下才能找到。~~当然，如果你足够有钱，你也可以直接签下专业版~~

?> 顺便一提，Kotlin 是由 JetBrains 设计的。

## 你的第一个程序

打开安装好的 IDEA（第一次使用会有一些设置，如果你购买了专业版，还需要登录），单击 New Project，在左侧点选 Kotlin，并在右侧的表单中填写项目的相关信息：

- Name：项目名称，只能包含字母、数字、下划线 `_`、连字符 `-` 和点号 `.`。

- Location：项目的存储位置，一个中等大小的 Kotlin 或 Java 项目可能最多要占据 2GiB 的磁盘空间。

- Build system：构建工具，选用 Gradle。Gradle 是在 Java 业界非常流行的构建工具之一，能非常灵活地配置多种任务，并且能使用插件来设置构建 流程。

- JDK：项目所使用的 JDK，IDEA 应当能自动定位到你安装的 JDK，如果没有，请点按 Add JDK，手动选定 JDK 的位置。

- Gradle DSL：Gradle 所使用的语言，选择 Kotlin。Gradle 支持使用 Kotlin 或 Groovy 来编写构建脚本，相比 Groovy，Kotlin 要更加严谨，在修改项目配置时不容易出错。

- Add sample code：添加样例代码，勾选此选项。

- Generate code with onboarding tips：在生成的代码中添加快速上手的提示，如果你愿意，可以勾选。 

单击 Create 按钮，创建项目。

在创建项目后，IDEA 会先下载 Gradle，然后再使用 Gradle 下载 Kotlin 的编译器，并配置整个项目。这个过程需要下载相当量的文件，可能需要几分钟。

?> 如果在下载时遇到问题，请尝试连接校园网（eduroam 选择教育网出口）。如果还是不行，或者如果你在其它地方开发，你可能会需要一点……小小的手段。这是有些麻烦，确实有些麻烦。

你可以单击左下角的 Build 面板（形状像一个锤子），并在其中查看整体进度。当面板显示 Finished 的时候，就代表项目设置完成。

> **到底怎么回事？**
> 
> 与 C 需要手动下载编译器和标准库不同，Kotlin（以及几乎整个 Java 生态系统）都是通过构建工具管理其依赖包的。Java 的生态系统非常庞大，可以使用的库也很多（截至编写时，至少有四千万个软件包），Kotlin 的标准库，甚至 Kotlin 编译器本身，也是这些库中的一部分。构建工具（我们使用的是 Gradle）会根据项目的需要，在创建项目时下载所需的包，这个步骤必须联网完成。

现在，在左侧的 Project 面板中，展开 `src/main/kotlin/Main.kt` 文件，并单击屏幕右上角的 Run 按钮（或者按下 `Shift + F10` 组合键）。如果没有出错，Run 面板将弹出，并且你将在其中看到 IDEA 的默认程序运行结果：

```
Hello, Kotlin!
i = 1
i = 2
i = 3
i = 4
i = 5
```

好，恭喜，欢迎来到 Kotlin 世界！请单击右下角的 NEXT 按钮，开始了解这门优雅却同样强大的语言。
