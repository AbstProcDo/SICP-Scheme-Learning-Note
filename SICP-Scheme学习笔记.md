# 1.1 程序构成的七个基本元素

本文为MIT-SICP的学习笔记第一课。SICP有麻省理工提供的免费在线阅读教材和免费的公开课（英文版），请自行搜索关键词MIT-SICP查阅官方的免费教材和课程。

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/72b6ccb05e25464b9107a9ec281f3557)



# 0 引言

首先翻一下全书的题眼。

> The acts of the mind, wherein it exerts its power over simple ideas, are chiefly these three:
> 1. Combining several simple ideas into one compound one, *and thus* all complex ideas are made.(合)
>
> 2. The second is bringing two ideas, whether simple or complex, together, and setting them by one another so as to take a view of them ~at once~, without uniting them into one, by which it gets all its ideas of relations.(联)
>
> 3. The third is separating them from all other ideas that accompany them in their real existence:(离)
>
>    This is called abstraction, and thus all its general ideas are made.

心智的行为，当施展其能力于简单的思想上，主要表现在以下三个方面：

1. 组合简单的观念，由此形成复合的观念
2. 将两个想法，不管是简单或者复杂的，汇集到一起，并排摆放以便一眼就能看到他们，不把他们聚合成一，由此，我们就能得到他们之间的相互关联
3. 将这些观念与现实中的所有其他观点区分开。

这就是抽象，所有的思想都是这样形成的。



# 1 表达式 Expression

表达式（Expression）是scheme最基础的元素。一上来就定义出最基本的元素，这是SICP这本书的神奇之处，拨云见日，醍醐灌顶。

Expression由operator（操作符）和operands（操作数）两部分组成，并且使用prefix-notion（前序符号）

```
(+ 21 35 12 7)
(* 25 4 12)
#+RESULTS:
: 1200
```

上图可知，前序表达式的优点之一是单一的operator可以处理多个operands。

前序表达式的第二个优点是可以任意拓展嵌套的深度，而不必担心运算顺序的问题。

```
(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))
```

上面的表达式，我们用正常的中序表达式转换一下，会晕头转向。

```
(3+5+7) * (2+(4*6))         
# 390
```

对比来看，前序表达式可以嵌套任意深度，不会出现计算顺序错误的问题。

# 2 命名与环境 Naming and Environment

变量是程序第二个基础元素。

```
(define pi 3.14159)
(define radius 10)
;;(* pi (* radius radius))
;314.159
(define circumference (* 2 pi radius))
circumference
; 62.8318
```

define 是最简单的抽象形式。虽然简单，意义重大。给复杂的计算对象命名，就不必浪费脑细胞记住他们，每次重复其中的细节，而只需要记住一个名字即可。

在Environment中，这被称之为name-object-pair。

千里之行始于硅步，无论多么复杂的程序都是有最基本的模块define循序渐进地搭建而成。

Define是我们抽象思考之旅的起点站。

# 3 求组合表达式的值 Evaluate Combination

本章的目标那是要拆解思考的过程，将复杂的问题分离 isolate 成一个一个的单独元素。

我们考虑求组合表达式的具体过程，也就是intepreter的解析过程：

1. 求子表达式的值（我们可以想象子表达式是一个核桃）
2. 将左边的操作符（或者是程序，我们可以想象程序是一把锤子），应用到刚才的表达式所求得的值上。换言之用锤子敲碎核桃。

求值的过程是树形结构，也被称之为树形累计tree accumulation。

以下组合表达式的具体执行过程为：

```
(* (+ 2 (* 4 6))
   (+ 3 5 7))
```

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/f80232bfc50c483cacfcb8accbd15cb1)



我们可能在此处恍然大悟，原来加减乘除这样的操作符operator就是最基本的程序（procedure)啊！真是深刻的洞见，O(∩_∩)O哈哈~。

此处我们留下一个问题，如下面的define表达式是否适用以上的算法呢？

```
(define x 3)
```

答案是否定的，因为define是Macro而不是combination。

我们此处获得的另外一个洞见是，define的过程与evalation的过程是分离的，这也是后文中，高阶函数得以实现的原因。

# 4 复合程序 Compound Procedure

我们首先总结以上三节的内容。一个powerful的编程语言，必定要包含下面三点：

1. 数字和算术运算符，他们分别是最原始的数据和最原始的程序；（记得当初第一次得到这句话的时候，真是手舞足蹈，真爱死SICP这本巫师书了）。
2. 嵌套（Nesting）将多个运算操作过程组合起来。比如我们在bash中可以这样组合

```
$ echo $(date)
Sun 28 Jun 2020 06:20:00 PM CST
```

1. 定义是最基本的抽象，将values与name绑定在一起，如此便解放了我们的大脑，不必去记忆冗长的手机号，而只需知道人名就可以了。

现在我们来看看如何定义一个程序，一种更为强大的抽象技术。先看看小学时候的求数字的平方。

```
(define square (x) (* x x))
(square 36)
```

于是，我们有了一个复杂程序，其基本形式为：

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/69763058c2b74807895d0909651bc127)



上面的变量x就是我们自然语言中的代词pronoun，抽象出来的基本形式为：

```
(define (<name> <formal parameters>) <body>)
```

应用第三节中的组合方法：

```
(+ (square x)  (square y))
```

将组合方法与定义再次组合应用：

```
    (define (sum-of-squares x y)
     #  (+ (square x) (square y)))

    (sum-of-squares 3 4)
   #  25
```

emm,下面来看一个复杂点儿的：

```
    (define (f a)
      (sum-of-squares (+ a 1) (* a 2)))

    (f 5)
   #  136
```

虽然表面上看上去咋咋呼呼的有点复杂，其本质与最原始的程序即操作符（+-×-）别无二致。表达式`(sum-of-squares (+ a 1) (* a 2)))`最左边的`sum-of-squares`就是一个操作符。

读到这里，我隐隐然感觉似乎完全掌握了计算机科学的精义了。（吹牛不上税）

# 5.0 应用程序的替换模型

求组合的复合程序值的过程与求组合表达式的过程一样的。解释器首先求组合中每个元素的值，然后再将程序应用到这些参数中。分步过程如下：

1. 将复合程序应用到参数中
2. 求程序体的值就将形参formal-argument替换为相应的实参real-argument

我们来逐步拆解第四节中求平方和程序的过程：

```
(f 5)
```

f 是在第四节中定义的程序，我们首先获取 f 的实体。

```
(sum-of-squares (+ a 1) (* a 2))
```

然后将形参a替换为实参5

```
(sum-of-squares (+ 5 1) (* 5 2))
```

于是问题就降维到求两个表达式组合的值，以及一个求平方和的操作，也就是三个子问题。我们首先对操作符operator求值以获取其对应的程序，接着对操作符operands求值以获得对应的实参。

现在 (+ 5 1) 得 6， (* 5 2) 得10，我们将 sum-of-squares程序应用到这两个数值。

```
(defun sum-of-squares (x y)
  (+ (square x) (square y)))
```

具体过程，就是分别用实参6，10替换形参x， y

```
(+ (square 6) (square 10))
```

应用square的定义：

```
(+ (* 6 6) (* 10 10))
```

再次降维：

```
(+ 36 100)
```

以上便是程序应用的替换模型 the substitution model for procedure application。

但是此处有两点需要强调说明：

1. ‘替换模型’是帮助我们理解程序的应用，而非真实的描述‘解释器’interpretr  如何工作。典型的解释器并非是将一段程序的形参替换为实参。事实上，替换模型的实现，是用局部环境替换形参。这些内容将会在SICP这本书的第三章和第四章中涉及，届时，我们将考察‘解释器’的工作原理和细节。
2. 贯穿全书，我们应用一系列渐进的模型阐述解释器如何工作，所有这些将会在第五章中达到高潮，解释器与编译器的原理将会得到完备的说明。替换模型只是诸多模型中的第一个，是思考求值过程的起点。一般而言，当对自然现象构建科学与工程上的模型，我们往往从简单的不完备的模型着手。随着更多细节的考察，简单模型显出不足，于是被更优化的模型所取代。‘替换模型’也不例外。具体而言，当我们在第三章中讨论处理‘mutable data' 的程序时，替换模型就掉链子了，必须被更复杂的程序应用模型所取代。

# 5.1 应用顺序与正常顺序

根据第三节对求值过程的描述，解释器首先对操作符operator和操作数operand求值，然后将得到的程序应用到得到的实参中（也就是将锤子敲在核桃上）。这并非求值的唯一途径。存在另外一种模型，不对操作数求值。这种模型首先将操作数表达式operand-expression中的形参替换到只剩下最原始的操作符operator，之后才执行求值过程。

```
: (f 5)
: would proceed according to the sequence of expansions
: (sum-of-squares (+ 5 1) (* 5 2))
: (+    (square (+ 5 1))      (square (* 5 2))  )
: (+    (* (+ 5 1) (+ 5 1))   (* (* 5 2) (* 5 2)))
: followed by the reductions
: (+         (* 6 6)             (* 10 10))
: (+           36                   100)
:                     136
```

与替换模型的区别一目了然，这种模型只做替换Procedures（或称为operator）这一步，不对arguments求值；或者可以理解为‘正常序列’只做evaluate这一步，而不做apply。
另外，我们也发现，这种方法颇为养眼，一路拆解到最底层。

仔细观察，我们会发现这种‘正常顺序’的不足之处。就是对`(+ 5 1)`和`(* 5 2)`的求值分别执行了两次，也就是对

```
(* x x)
```

执行了两次。

这第二种全部展开然后再收敛的求值方法就是‘正序求值’normal-order evalation，与先前的‘先求值再应用’的方法相对应，也就是‘应用序列求值’。

Scheme默认使用application-order evaluation一方面是因为可以提高运算效率，避免对表达式的重复运算；更重要的另外一方面是，当我们拓展到不能应用替换模型的程序时，正序求值的方法将会变得异常复杂。

但是‘正序求值’也极有价值，我们将在第三章和第四章16节中，深入探索其应用。

# 6 Conditional Expressions and Predicate 条件表达式与断言

Predicate这个词用得太好了。Pre+dicate预先说出。

到目前为止，我们定义的程序表达力expressive-power极为有限。因为我们尚且没有途径做出种种测试，并根据测试的不同结果来决定下一步的操作。换言之，我们没有方法哪怕去完成求任意数字的绝对值这个样的简单任务。

对求绝对值这样的任务，需要引入‘案例分析’case-analysis。scheme中用一种特殊的形式来标记上述的‘案例分析’结构，这种形式的关键词 cond （即conditional）注意此处的conditional是个名词，即条件句的含义。

```
    (define (abs x)
      (cond ((> x 0) x)
                ((= x 0) 0)
                ((< x 0) (- x))))
```

通用形式为：

```
    (cond (⟨p₁⟩ ⟨e₁⟩)
                (⟨p₂⟩ ⟨e₂⟩)
          …
              (⟨pₙ⟩ ⟨eₙ⟩))
```

p 是predicate, e是expression，p与e一起组成一个conditional。

绝对值的另外一种写法：

```
    (define (abs x)
      (cond ((< x 0) (- x))
            (else x)))
```

我们还可以应用更加语义化的 if关键词：

```
    (define (abs x)
      (if (< x 0)
          (- x)
          x))
```

抽象其结构为：

```
 (if ⟨predicate⟩ ⟨consequent⟩ ⟨alternative⟩)
```

除了 < = > 这三个最原始的predicate之外（predicates都是具指向的），还有三个复合逻辑操作符，and/or/not，这给我们赋能构造出更加复杂的predicate。

```
(and ⟨e₁⟩ … ⟨eₙ⟩)
(or ⟨e₁⟩ … ⟨eₙ⟩)
(not ⟨e⟩)
```



注意，and 和or都是与 define 一样的special form，而非 procedures。因为他们的子表达式并不全被求值。not却是一个正常的 procedures。

# 7 案例分析：牛顿法求平方根

我们以上介绍的程序 Procedures就如普通的数学方程。都是根据一个或多个参数去确定一个数值。但是二者之间又有重大的不同，即procedure必须是有效的，能解决问题的，而非只是描述性的。

说到这一点，我们考察求平方根的问题：

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/d653ace139f849cebafea2cb41aec568)



上图的式子描述了一个完美的数学函数方程。我们可以用其判断一个数字是否为另一个数字的平方根，或者推导关于平方根的一般事实。另外一方面，这个定义并未阐述如何求解平方根的方法，将其用Scheme表达出来也是无济于事。

```
(define (sqrt x)
  (the y (and (>= y 0)
              (= (square y) x))))
```

这也是 declarative knowledge 与imperative（procedual) knowledge的区别。

牛顿求平方根法用的是‘连续逼近’的思路 "successive  approximations"。我们首先瞎猜一个y作为x的平方根，然后在这个瞎猜的基础上更好的瞎猜，不断地接近平方根的真实值。这个bette-guess为前面的瞎猜值与x/y的平均值，分布过程展示为：

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/069b9c031c2745908b14616accc4c8c3)



持续上述过程，我们就能得到不断优化的逼近值。

上述过程用实现出来就是：

```
    (define (sqrt-iter guess x)
      (if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x) x)))
```

瞎猜值通过取它与x/y之间的平均值不断的优化。此处的关键词是average，另外sicp中用了quotient（商）和radicand（开方数）这样的纯的定义表达方式。

根据第三节中的isolate原则，我们再分别将improve和average定义出来。

```
    # improve
    (define (improve guess x)
      (average guess (/ x guess)))
     # average 
     (define (average x y)
      (/ (+ x y) 2))
```

最后定义出来good-enough？因为我们采用的是持续逼近的方法，因此good-enough？要根据我们的需求酌情定义。

```
    (define (good-enough? guess x)
      (< (abs (- (square guess) x)) 0.001))
```

此处我们要引出函数式编程中最惊人的宣告。我们回头看一眼sqrt这个求平方根的程序，这个程序诠释了简单的过程式编程比如用C语言，Java等写纯数字程序的过程。但是，令人震惊的是，整个过程中我们并没有包含迭代Iterative（或者循环looping结构）。换言之，我们仅仅只是通过调用程序call procedure而实现了迭代循环的过程。

这是函数式编程最核心的特点之一，就是用recursiion实现iteration。

# 8 程序作为黑匣子抽象工具 Procedures as Black-Box Abstractions

**结构与黑匣子 Decompostion and Black-Box**

求平方根的sqrt程序可以解构decompostion为以下图例

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/5d05a19981c140afadcfee79db0945ec)



那么如何解构呢？解构的策略可以很简单，比如我们闭上眼睛，随意将程序分割成几个部分，前五行，后五行，再接着五行等，如此循环。

然而解构策略的关键点乃是每一个程序都完成一个明确的任务，如此这个程序便能在其他程序中调用。具体而言，当我们定义good-enough?程序后，就能将此作为一个黑匣子。我们无须操心他的内部实现，而只须知道他能计算平方square这个简单事实。也就是如何计算平方sqare的细节可以被suppressed，作为后面的考量。事实上，到good-enough？这一步，square并非一个程序，而是一个程序的抽象（真乃醍醐灌顶），这称之为‘过程抽象’procedural abstraction。

在这个层面上而言，任何能计算square的程序都是等效的。

换言之，如果只考虑返回值（不考虑计算效率等），下面的两种方法是等同的。

```
    (define (square x) (* x x))

    (define (square x)
      (exp (double (log x))))
    (define (double x) (+ x x))
```

综上所述，程序的定义必须能够覆盖细节。程序的用户无须亲自操心其内部的实现，而只需将其当作一个黑匣子。由此引出一个论断，一个程序不应该使得其用户只有详尽掌握起内部的实现时，才能得心应手的使用他。一个应该让用户不看其内部实现就能完全无障碍的应用。

**局部变量 Loca lnames**

程序内部实现的细节之一是，程序的形参的选择不应该影响到程序本身的使用。以下的两个程序是无差别的：

```
    (define (square x) (* x x))
    (define (square y) (* y y))
```

此处的基本原则是，程序的实际含义必须独立于其参数的名字，这看起来不证自明。然而却意义重大。

总结为一句话就是

> The simplest consequence is that the parameter names of a procedure must be local to the body of the procedure.

**内嵌定义和块状结构**

我们目前已经有了第一种参数分离策略（name isolation)：程序的形参局部于其程序体。（本章反复在论述isolation分离策略）。求平方根的程序诠释了另外一种控制命名使用的方式。当下的成簇包含几个分离的部分：

```
    (define (sqrt x)
      (sqrt-iter 1.0 x))

    (define (sqrt-iter guess x)
      (if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x) x)))

    (define (good-enough? guess x)
      (< (abs (- (square guess) x)) 0.001))

    (define (improve guess x)
      (average guess (/ x guess)))
```

此处的问题是，对用户而言，只有这sqrt这一个程序是重要的，其他的部分( sqrt-iter, good-enough?, and  improve只会让用户头脑混乱。当程序变得巨大的时候，此问题尤为严重。我们必须将这些散乱的子辅助程序隐藏在应用他们的程序体内部。

```
    (define (sqrt x)
      (define (good-enough? guess x)
        (< (abs (- (square guess) x)) 0.001))
      (define (improve guess x)
        (average guess (/ x guess)))
      (define (sqrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (sqrt-iter (improve guess x) x)))
      (sqrt-iter 1.0 x))
```

这种定义的嵌套称之语义作用域 lexical scoping。

我们将大量的应用这个块状结构，来帮助我们将大段的程序分解为易于处理的小块 break up large programs into tractable pieces。这种块状结构的思路始于编程语言Algol  60。当下已成为所有编程元组织构建大型程序的公钥工具。

# 收尾总结

基本结构总结如下：

![img](SICP-Scheme%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/608979694207413297e0079aac625fd9)



以上，第一课完结。