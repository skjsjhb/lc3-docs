# Kotlin 面向过程

由语句这种最小单元组织成更长的程序，有好几种不同的范式。在 C 语言中，你已经学会用语句构成简单函数，再用函数构成更复杂的函数。这种基于**过程（Procedure）**的范式，被称为**过程式编程**。作为最简单直观的范式，面向过程常常是小型项目的第一考虑方案。

## 打包带走！

先前已经提到过块的概念了，用大括号 `{}` 包裹起来的一些语句被称为一个**块（Block）**：

```kotlin
{
    val b = 1
    val a = b + 1
}
```

这里要注意一点，与其它语言不同，在用块将一些语句打包成一条时，Kotlin **并不会立即执行它**，请看下面的代码：

```kotlin
var b = 1
{
    b = b + 1
}
val a = b // a 是 1
```

相同的事情如果发生在 C、C++、Java 或者 JavaScript 中，`a` 的结果都会是 `2`。这也说明一点，即 Kotlin 中的块与其它语言不一样，块本身只是一个形式上的表达，具体的含义会根据语境而有所不同。

## 你的名字？

你已经知道，不可以重复定义同名的变量：

```kotlin
val a = 1
val a = "String" // 编译错误
```

那么，是不是整个程序当中的变量都不能相同呢？那肯定不是，如果真是这样，那么 `a` 这种变量名，肯定会被所有人争着抢着用，这也太不方便了。

这里要牵涉到**命名空间（Namespace）**的概念，在同一个命名空间中，同名的变量只能有一个。

那么什么是命名空间呢？请看以下两条规则：

- 传递给编译器的每个编译单元是一个独立的命名空间。（通常，一个编译单元也就是一个 `.kt` 源文件）

- 在编译单元内部，每个块是一个独立的命名空间。

所以，假设有名为 `Unicorn.kt` 的文件，并且内容如下：

```kotlin
val a = 1

fun unicorn() {
    val a = 2
    if (a > 0) {
        val a = 3
        {
            val a = 4
        }
    }
}
```

?> 我们还没有介绍函数，读者可以从形式上进行分析。

这里实际上有 4 个命名空间，分别是编译单元（文件本身）、函数 `unicorn` 的块、`if` 表达式的块以及在 `val a = 3` 后的一个没有指定用途的块。我们通过使用四个名为 `a` 的变量标识出了它们。

那为什么我们可以这么做呢？因为虽然变量 `a` 重复出现了 4 次，但每次都是在不同的命名空间中，所以也就不会发生冲突。这是命名空间的第一条规则：

**在同一个命名空间内，每个变量的名称必须是独一无二的。**

?> 命名空间没有传递性，也就是说，即使一个块被包裹在其它块中，它自己也拥有独立的命名空间，外部的块是“看不到”其中定义的变量的。换句话说，变量名不得相同这条规则，仅在“同级”的块中有效。

---

现在看下面的代码：

```kotlin
val a = 1

{
    val a = 2
    val b = a
}

val c = a
```

问题来了，`b` 和 `c` 的值各是多少？这其实也就是在问，在 `b` 和 `c` 所在的位置，分别访问到的是哪个 `a`。这里，`b` 是 `2`，而 `c` 是 `1`。这是命名空间的第二条规则：

**在使用某个变量时，优先在本级命名空间中寻找。如果没有找到，则向上一级寻找，以此类推。绝不会向下寻找。**

`b` 所在的位置是内层的块，同级的名为 `a` 的变量由 `val a = 2` 定义，而 `c` 所在的位置是外层的块，它对于内层块中的 `val a = 2` 一无所知 —— 它也不在乎，因为 Kotlin 规定了**绝不会向下寻找**，它在自己的命名空间（本例中是文件）中找到了 `val a = 1`，于是将它取为自身的值。

?> 程序中的这种做法，会使得在内部块中，由于已经定义了 `a`，因而无法访问外部块定义的变量 `a`。这被称为**重影（Shadow）** —— 在内层命名空间中，外层的变量 `a` 被“遮住”了。在大多数项目中，这不是一种好的用法。即使命名空间规则允许你定义相同的名称，但除非有特殊理由，否则不建议这么做。

下面这个例子能验证这一点：

```kotlin
var a = 1
if (a == 1) {
    a++
}
val b = a
```

这里 `b` 就是 `2`，因为在 `if` 的块内要求对 `a` 自增 `1`，但 `if` 的块内并没有定义 `a`，于是它向上找，找到了在外层定义的 `var a = 1`，它将这个值增加 `1`，使得 `a` 变为 `2`。

---

说了这么多有关命名空间的内容，好像也没什么用啊？别心急，命名空间是在介绍函数前必须介绍的一个重要知识。下面我们就开始介绍你在 C 中已经熟悉的函数。

## 行文之韵

你已经熟练于编写函数了，对吧？即使是 Hello World 程序也有一个函数：

```c
#include <stdio.h>

int main(void) {
    puts("hello, world");
    return 0;
}
```

（当然，如果你是 Python 使用者，你可能不会这么觉得……）

**函数（Function）**将一系列语句打包起来，以供后续使用。在 Kotlin 中，函数以 `fun` 关键字定义：

```kotlin
fun main() {
    // ...
}
```

?> 你确实可以将 `fun` 按字面意思理解 —— Kotlin 的口号是 "Concise. Multiplatform. Fun."，正如其名，它确实是一门很快乐的语言！

---

**主函数** `main` 是一个很重要的函数，和 C 一样，`main` 是程序的入口点 —— **程序从这里开始执行**。

```kotlin
fun main() {
    // 程序从这里开始
}
```

?> Kotlin 的主函数没有返回值。

---

另外，你已经在 C 中知道，除了定义以外，语句都应该写在函数中：

```c
int a = 0
a = a + 1 // 错误！
int main() {
    // ...
}
```

在 Kotlin 中，这条规则也同样适用。先前我们写的一些代码片段，例如：

```kotlin
var a = 1
if (a == 1) {
    a++
}
val b = a
```

严谨来说，它们其实应该放在 `main` 或者其它函数中。先前这么写，只是因为我们还没有介绍函数的概念。往后，我们都会采取符合 Kotlin 标准的写法。

---

通过**调用（Call）**函数，你可以重复使用函数中的代码：

```kotlin
fun main() {
    sayCiallo()
}

fun sayCiallo() {
    println("ciallo, world")
}
```

?> 与 C 不同，Kotlin 没有函数原型，也不要求“先定义再使用”—— Kotlin 编译器会找到它们的。

这里也向你介绍第一个**库函数**。Kotlin 标准库提供了大量可重用的函数，此外你还可以从 Kotlin 直接调用 Java 标准库中的函数，“青出于蓝，而胜于蓝”，这也是我们说“Kotlin 在功能上至少与 Java 一样强大”的底气。

> **`println`**
> 
> ```kotlin
> fun println()
> fun println(message: Any?)
> ```
> 
> 输出 `message` 指定的内容到终端，在输出后添加一个换行符。
> 
> 如果 `message` 不是字符序列，则将其转换为字符序列再输出。
> 
> 如果没有任何参数，则输出一个换行符。

如果你在 IDEA 中运行上述代码，输出将会是：

```
ciallo, world
```

---

函数可以接受**参数（Parameter）**：

```kotlin
fun sayCialloFor(count: Int) {
    repeat(count) {
        println("ciallo, world")
    }
}

fun main() {
    sayCialloFor(10000)
}
```

在定义函数时写在小括号 `()` 内的东西就是**参数列表（Parameter List）**，它们的形式与变量很像，只不过没有 `val` 或 `var` 关键字。需要注意，和 C 一样，参数的类型是不能省略的，必须用冒号 `:` 显式地指定参数类型。

在调用函数时，必须按照参数列表提供相应的**参数（Argument）**，如果数量或者类型不匹配，Kotlin 就会拒绝编译。

?> 顺便一提，与 C 不同，在 Kotlin 中你**不能对参数重新赋值**（它们视作是用 `val` 定义的）。

---

函数可以有**返回值（Return Value）**：

```kotlin
fun createCiallo(): String {
    return "ciallo, world"
}

fun main() {
    println(createCiallo())
}
```

通过使用 **`return` 表达式（Return Expression）**，我们可以向函数的调用者返回**一个**值。返回值的类型以冒号 `:` 加注在函数的参数列表小括号 `()` 后，但在函数体大括号 `{}` 前。如果函数没有返回值，则可以省略类型标记。

---

顺便说一句，如果函数的返回值可以**简单地计算出**，例如：

```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}
```

那么你可以用赋值符号 `=` 来简化函数的定义：

```kotlin
fun add(a: Int, b: Int): Int = a + b
```

就这么简单，只要把**返回值的表达式**用 `=` 标记就可以了。这里我们显式地指定了返回值类型 `Int`，但实际上也可以不加，因为 Kotlin 能自动推断出来。这是 Kotlin 另一个便捷的地方，它省去了对于小型函数需要多写一个 `return` 的无用工作。

?> 如果不使用赋值来定义函数，那么就需要显式指定返回值类型。

---

等等，你刚才又说了**表达式**对吧？哈，这下被我捉虫了，`return` 总不会是一个表达式了吧？诶，你先别急，`return` 它还真就是一个表达式。下面的代码完全可以不出错地编译（尽管会有警告）：

```kotlin
fun a(): Int {
    return return 1
}
```

`return` 后只能接表达式，这总没问题了吧？那么，这也说明 `return 1` 自身是一个表达式。`return` 表达式的值固定为 `Nothing`（一个类似 `Unit` 的值），因为从原则上来讲，`return` 必定会导致函数返回，因此它的值是什么已经不重要了 —— 被返回的函数没有机会用到它。之所以还要把 `return` 作为表达式，是出于它结构上的特性，在某些地方可以很方便地“快速返回”。这属于高阶技巧，我们放到后面再讲。

---

Kotlin 可以在函数中嵌套定义函数，内部定义的函数**无需参数**即可访问**其所在命名空间**的变量：

```kotlin
fun foo() {
    val unicorn = 1
    fun addUnicorn(a: Int): Int {
        return unicorn + a
    }
    addUnicorn(2) // 3
}
```

在这里，我们在 `foo` 的函数体内定义了 `addUnicorn` 函数，作用是将同一命名空间中的变量 `unicorn` 与参数 `a` 相加，并返回加法的结果。由于 `addUnicorn` 是在 `foo` 的函数体内定义的，因此它也**只能在这个命名空间中使用**。

?> 能够嵌套函数是大多数现代语言的重要特性，它能减少不必要的参数传递，并让程序结构与逻辑更容易优化。至于 C 语言，很遗憾，如果不经过特殊的设置（例如使用 GNU C 扩展），这一点并不容易做到。

## 把这些结合在一起！

有了函数，我们终于可以开始编写完整的程序了：

```kotlin
fun main() {
    var i = 0
    while (i <= 5) {
        printStars(i)
        i++
    }
}

fun printStars(count: Int) {
    repeat(count) {
        print('*')
    }
    println()
}
```

哦对了，这里需要介绍一下陌生的 `print` 函数，它其实就是 `println` 的不换行版本：

> **`print`**
> 
> ```kotlin
> fun print(message: Any?)
> ```
> 
> 输出 `message` 指定的内容到终端。
> 
> 如果 `message` 不是字符序列，则将其转换为字符序列再输出。

你可以在 IDEA 中尝试运行这个程序，结果应当是：

```
*
**
***
****
*****
```

试试用你刚学的知识解释上述的程序，应该不算太困难。

## 在结束之前

函数是组成过程式编程的最后一块拼图。有了基本语法、流程控制和函数，只要再加上标准库，我们就可以编写许多有趣的程序了（事实上，是大部分你在 C 中编写过的程序）。过程式编程尽管简单，但能做的事情也相对有限。要适应更现代化的项目，我们还需要混合一些更高级的范式，而这就需要在下一节介绍了。

## 练习题

1. 用循环输出以下内容：
   
   ```
   12345
   23451
   34512
   45123
   51234
   ```
   
   如果你在实现后发现除了 `main` 函数外没有其它的函数，尝试定义一个，并使用它。这会增加几行代码，但会让你的程序各部分的功能变得清晰。

2. 运行下面的程序：
   
   ```kotlin
   val a = 3
   
   fun main() {
       println(addByTwo(a))
   }
   
   fun addByTwo(b: Int): Int {
       val a = 2
       return a + b
   }
   ```
   
   它输出什么？为什么是如此？这个程序本可以写得更短，尝试在不删除 `addByTwo` 函数的前提下，减少程序中不必要的代码。

> **解答**
> 
> 1. 下面是一个参考实现：
>    
>    ```kotlin
>    fun main() {
>        var i = 1
>        repeat(5) {
>            outputLine(i)
>            i++
>        }
>    }
>    
>    fun outputLine(start: Int) {
>        var i = start   
>        repeat(5) {
>            print(i)
>            i = if (i == 5) 1 else i + 1
>        }
>        println()
>    }
>    ```
>    
>    本程序有一些要点：
>    
>    - 没有使用 `outputLine(i++)` 这样的写法，这并没有什么性能优化，还会引起歧义（许多人常常记不清前缀与后缀的运算顺序）。选用一种效率高而且绝不会影响阅读的写法是最好的。
>    
>    - 程序的功能分为两部分：输出 5 行和输出每行的数字，这可以拆分成两个函数来实现。往后，如果哪个部分的功能需要修改，只需要改变对应的函数即可，这让代码更容易维护。
>    
>    - 使用 `if` 表达式赋值来简化 `i` 到达 `5` 时将其设置为 `1` 的逻辑。
>    
>    这个程序其实还有进一步优化的空间，我们会在介绍 `repeat` 的高级用法时提到。
> 
> 2. 程序输出 `5`，因为 `addByTwo` 函数内使用定义为 `2` 的 `a` 与参数 `b` 相加。在 `main` 中调用该函数时，传入的参数是 `3`，因此所得结果是 `5`。注意 `addByTwo` 能够在自己的命名空间内找到定义 `val a = 2`，而 `main` 函数内并没有定义 `a`，因此这里的 `a` 指的是上一层（也就是全局）定义的 `val a = 3`。
>    
>    采用返回值赋值语句就可以简化该程序的写法：
>    
>    ```kotlin
>    val a = 3
>    
>    fun main() {
>        println(addByTwo(a))
>    }
>    
>    fun addByTwo(b: Int) = b + 2
>    ```