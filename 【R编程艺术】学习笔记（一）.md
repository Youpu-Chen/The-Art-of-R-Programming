从作者的角度，对本书有一个比较好的理解：

- 不需要特别优秀的统计学知识 & 编程知识 —— 更精确介绍的是R的特性
- 主要面向：R内的数据结构 & 编程结构、R的面向对象编程、R的并行计算、R的debug、如何提升R的程序性能

本书的结构：

- 第0部分（chapter1）：数据结构 & 编程基础
- 第1部分（chapter2~6）：数据结构
- 第2部分（chapter7~13）：编程结构 & R面向对象编程的特性，R的输入输出，<font color='yellow'>R的字符串处理</font>，R绘图
- 第3部分（chapter14~16）：R的执行速度、R与Python/C++的混合编程



## 第一章：快速入门

有很多部分在《R语言实战》等部分，我已经得到了充分的训练，因此略过。

### 启动和关闭R

#### `.Rprofile`

`.Rprofile`的作用，每次开始R会话的时候执行保存在该文件中的R命令

##### 【做个标记】`.Rprofile`如何帮助我们更好使用R？ —— 这部分的笔记需要整理

### R中的帮助文档

```shell
# 1.
help()
?functionname

# 2.
help.search("multivariate normal")  # 不太清楚查什么的时候，所使用给函数
??"multivariate normal"
```



### 关于函数

#### 以计算给定向量内的奇数个数为例

```R
oddcount <- function(x) {
    k<-0  # assign 0 to k
    for(n in x){
        if (n %% 2 == 1)k <- k+1 # %%is the modulo operator
        }
    return(k)
}

# run oddcount()
oddcount(c(1,3,5))
oddcount(c(1,2,3,7,9))
```

需要注意的是，

- R函数内的变量为`局部变量`，以上述函数为例：`oddcount()`函数内建立了变量`n`，但是如果在当前环境中直接调用会出现如下报错：

  ```R
  r$>  n
  Error: object 'n' not found
  ```

- 函数可以直接访问全局变量，

  ```R
  f <- function(x) return(x+y)
  y <- 3
  f(5)
  # 8
  ```



### 关于列表

需要记住的是，R向量所包含的数据类型需要相同，列表包含的数据类型可以不同。

```R
# 例1
x <- list(u=2, v="abc")
x # 列表x内存在2个部分，可以通过x$u 和 x$v的方式进行索引

# 例2：以尼罗河数据集为例
hn <- hist(Nile)
print(hn)   # 查看创建的对象内包含元素，组成成分为多个列表
str(pn)     # 另一种打印列表的方式！！！
```



### 关于类

该部分主要关于<font color='yellow'>S3类</font>的使用。

#### 以直方图类为例

```R
hn <- hist(Nile)

print(hn)
$breaks
 [1]  400  500  600  700  800  900 1000 1100 1200 1300 1400

$counts
 [1]  1  0  5 20 25 19 12 11  6  1

$density
 [1] 0.0001 0.0000 0.0005 0.0020 0.0025 0.0019 0.0012 0.0011 0.0006 0.0001

$mids
 [1]  450  550  650  750  850  950 1050 1150 1250 1350

$xname
[1] "Nile"

$equidist
[1] TRUE

attr(,"class")
[1] "histogram"
```

上述对象和列表的区别在于：上述对象存在一个`attribute`，<u>即属性变量，用于指定列表的类，表示其为`histogram类`</u>。

#### 类的组成部分都是列表，那为什么还需要类？

答：

- 类，使用在泛型函数内。
- 泛型函数代表一个函数族，<font color='yellow'>只能适用于某个特定的类</font>。

##### 【做个标记】类，在第九章会细讲。

### 实例：考试成绩的回归分析

```R
examquiz <- read.table("R/ExamsQuiz.txt", header = T)

# 查看examquiz的对象类型
class(examquiz)

# 使用”期中“考考试成绩预测”期末“考试成绩
lma <- lm(examquiz[,2] ~ examquiz[,1])
# lma <- lm(examquiz$V2 ~ examquiz$V1)
# 使用”期中“成绩和测验成绩预测期末考试的成绩。使用符号：+
lmb <- lm(examquiz[,2] ~ examquiz[,1] + examquiz[,3])

# 查看lma的属性
attributes(lma)   # 查看线性回归对象所包含的属性/特征
lma$coef
lma # 查看线性回归对象的一些参数。使用函数：print.lm()
summary(lma) # 对线性回归对象进行统计。使用函数：summary.lm()
```



## 第二章：向量

### 实例：寻找连续出现1的游程

> 什么是游程？
>
> 在一个0和1组成的序列中，一个由连续的0或者1构成的串，称为一个游程（run）

#### 示例代码1

```R
findruns <- functions(x, k){
    n <- length(x)
    runs <- NULL
    for (i in 1:(n-k+1)){
        if (all(x[i:(i+k-1)] == 1)) runs <- c(runs, i)
    }
    return(runs)
}
```

##### 【知识点】`all()`函数的使用

`all()`的作用：输出是否全部为TRUE。例子如下：

```R
x <- c(1:10)
all(x > 88)
```

#### 示例代码2

```R
findruns <- function(x, k){
    n <- length(x)
    runs <- vector(length=n)
    count <- 0
    for (i in 1:(n-k+1)) {
        if (all(x[i:(i+k-1)] == 1)) {
            count <- count + 1
            runs[count] <- i
        }
    }
    if (count > 0) {
        runs <- runs[1:count]
    } else runs <- NULL
    return(runs)
}
```

