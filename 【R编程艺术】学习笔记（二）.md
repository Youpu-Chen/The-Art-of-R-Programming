

## 第二章：向量

本章重点：

- 循环补齐
- 筛选
- 向量化

### 【回顾】关于变量的形式

使用`mode()`查看变量的形式。变量总共有如下几种形式等：

- 整型
- 数值型（浮点数）
- 字符型
- 逻辑型
- 复数型



### 关于标量、向量、数组与矩阵

#### 一些注意事项

- R中，是不存在标量的，单个数值为向量的特殊形式。

与C的比较：

x为一个整型变量。

```C
int x;      
int y[3]
```

- R中，不需要声明变量

  ```R
  # 无效代码
  y[1] <- 5
  y[2] <- 12
  
  # 有效代码
  y <- vector(length=2)
  y[1] <- 5
  y[2] <- 12
  ```

  



#### 1、添加或删除向量元素

需要注意的是，R中的向量是连续存储的 —— <font color='yellow'>不能插入或删除元素</font>。

```python
x.insert(6)
```

因此，想要添加或删除向量元素，需重新赋值。

```R
x <- c(88, 5, 12, 13)
x <- c(x[1:3], 168, x[4]) # insert 168 before the 13
```

#### 2、获取向量长度

使用函数：`length()`

计算长度的一些注意事项：

- 当给定的向量为空向量时（`length(x)=0`），使用`1:length(x)`获取向量的索引编号时，会出现如下情况：

  ```R
  x <- c()
  1:length(x)
  # [1] 1 0
  ```

  上述代码的含义为，变量i先取值为1，后取值为0（这与我们的目的不符）



### R的循环补齐

直接用例子来说明，

```R
### 原代码
c(1,2,4) + c(6,0,9,20,22)
# [1]  7  2 13 21 24
# Warning message:
# In c(1, 2, 4) + c(6, 0, 9, 20, 22) :
#   longer object length is not a multiple of shorter object length

### 与原始代码意义相同的代码
c(1,2,4,1,2) + c(6,0,9,20,22)
```



### 向量运算

#### 1、向量运算 & 逻辑运算

此部分需有一定线性代数基础（我似乎没有）

```R
# 1.加法
x <- c(1,2,4)
x + c(5,0,-1)   # 对应位置相加

# 2.乘法
x * c(5,0,-1)   # 对应位置相乘

### 其他的也是类似，为对应位置的元素进行计算
```

#### 2、向量索引

【略】

#### 3、使用`:`创建向量

需要注意的点，

- `:`的优先级，高于其他符号

  ```r
  i <- 2
  1:i-1     # 代表(1:i) - 1
  ```

#### 4、使用`seq()`创建向量

`seq()`的作用是，用于生成等差数列。

```R
### 示例
# 在处理整数等差数列和浮点数型等差数列时，存在区别，如下：
seq(from=12, to=30, by=3)
seq(from=1.1, to=2, length=10)

# seq()处理空向量。seq()相较于length()，前者能够正确计算出空值（NULL）
x<- c()
seq(x)
# integer(0)
```



#### 5、使用`rep()`重复向量常数

`rep()`的作用是，生成由同一元素组成的向量

使用格式：`rep(x, times, each)`，

- `times=`代表设定的重复次数
- `each=`代表给定元素x，交替重复的次数

```R
### 示例
x <- rep(8, 4)
[1] 8 8 8 8

rep(c(5,12,13), 3)
[1]  5 12 13  5 12 13  5 12 13

rep(c(5,12,13), each=2)
[1]  5  5 12 12 13 13
```



### 关于向量化运算

#### 一些注意事项

-  <font color='yellow'>提高R代码执行速度的有效方法 —— 向量化（vectorize）</font>，即应用到向量上的函数直接作用到每一个元素上

- **奇葩写法**（我自己认为的）

  ```R
  y <- c(12,5,13)
  '+'(y,4)  # 代表对y向量中的每一个元素加4
  ```

- 在函数编写过程中，将参数设置为**标量**

  ```R
  f <- function(x,c){
      if (length(c) != 1) stop("vector c not allowed")
      return((x+c)^2)
  }
  ```



#### 1、向量输入 ，向量输出

【默认情况下，使用向量化函数处理向量，输出为向量类型】

关键信息：<font color='yellow'>如果一个自行编写的函数使用了向量化的运算符，那么该函数为向量化后的函数</font>。

```R
### 示例
# 1.
u <- c(5,2,8)
v <- c(1,3,9)
u > v
# [1]  TRUE FALSE FALSE

# 2.
w <- function(x) return(x+1)
w(u)
# [1] 6 3 9
```

#### 2、向量输入，矩阵输出

【使用函数：`sapply()`，结果输出为<font color='yellow'>矩阵类型</font>】

> `sapply()`，全称为simplify apply

```R
### 示例
z12 <- function(z) return(c(z,z^2))
sapply(1:8, z12)
#     [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8]
# [1,]    1    2    3    4    5    6    7    8
# [2,]    1    4    9   16   25   36   49   64
```



### 关于NA和NULL

一句话概括：NA是缺失值，NULL代表不存在的值。前者不能计算，后者可以计算。



### R中的筛选（filtering）

#### 1、生成筛选索引（布尔值索引） & 直接索引

```R
### 示例
z <- c(5,2,-3,8)
w <- z[z*z > 8]
# [1]  5 -3  8
```

#### 2、使用函数进行辅助

##### 1.`subset()`：直接判断并提取

```R
### 示例
x <- c(6, 1:3, NA)
subset(x, x>5)
```

##### 2.`which()`：获取索引位置

```R
### 示例
# 1.
z <- c(5,2,-3,8)
which(z*z > 8)

# 2.找到向量中的第一个1
# 写法1
first1 <- function(x) {
    for (i in 1:length(x)) {
        if (x[i] == 1) break
    }
    return(i)
}

# 写法2
first1a <- function(x) return(which(x == 1)[1])
```



### `ifelse()`的使用

```R
### 示例
x <- 1:10
y <- ifelse(x %% 2 == 0, 5, 12)
```



### 判断向量之间的关系

```R
### 1、 ==
# 1. 
x <- 1:3
y <- c(1,3,4)
x == y
# [1]  TRUE FALSE FALSE
# 2.
"=="(3,2)
# 3.
i <- 2
"=="(i,2)
# [1] TRUE

### 2、 all()
x <- 1:3
y <- c(1,3,4)
all(x == y)  # 询问所有的运算结果是否为真

### 3、 identical()
identical(x,y)
```

需要注意的是，

- `:`产生的是整数，`c()`产生的是浮点数



---

## 第二章：向量，实例部分

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

##### 示例代码1和示例代码2的区别在哪里？

示例代码1，在每次均需要给新的向量（`c(runs, i)`）分配内存 —— 导致运行速度变慢。

示例代码2的操作为预先分配内存，避免在后续运行过程中执行分配内存的操作。

> 这边就算是，编写程序需要去注意的一个点了：预先分配内存较避免后续执行过程中分配内存快。



### 实例：预测离散值时间序列

题目：根据最近k天的天气记录来预测明天的天气。

背景知识 —— 过半数规则（majority rule）：如果在最近k期里，1的数量大于k/2，那么预测下一个值为1，否则，下一个值为0.

#### 示例代码1

```R
preda <- function(x,k) {
    n <- length(x)
    k2 <- k/2
    # the vector pred will contain our predicted values
    pred <- vector(length=n-k)
    for (i in 1:(n-k)) {
        if (sum[x[i:(i+(k-1))]] >= k2) pred[i] <- 1 else pred[i] <- 0
    }
    return(mean(abs(pred-x[(k+1):n])))
}
```

编写这段代码的时候，遇到的一些问题：

- R中，用于表示函数的代码为`function(){}`
- R中，计算0-1数列错误率的技巧：`mean(abs(a-b))`。其中a代表预测得到的数列，b代表观测得到的数列（原始数列）



#### 示例代码2

示例代码2相较于示例代码1的优化在于，示例代码1在计算k期内的累加值时，存在重复计算，举例说明为：

```R
# 原始数列
og_seq <- c(1, 0, 1, 0, 1)

# k=3
# 从索引第一个位置的第一个k期
seq1 <- sum(x[1:1+3-1])  # 对应原始数列：c(1,0,1)
# 从索引第二个位置的第二个k期
seq2 <- sum(x[2:2+3-1])  # 对应原始数列：c(0,1,0)
```

从上述例子，就可以看出对索引为2和3的数字进行了<font color='yellow'>重复计算</font>。

示例代码2优化的地方为`sm`，通过减去最早的元素`x[i-1]`，再加上新的元素`(x[i+k-1])`，达到更新sm的目的。示例代码2如下，

```R
predb <- function(x,k) {
    n <- length(x)
    k2 <- k/2
    pred <- vector(length=n-k)
    sm <- sum(x[1:k])
    if (sm >= k2) pred[1] <- 1 else pred[1] <- 0
    if (n-k >= 2) {
        for (i in 2:(n-k)) {
            sm <- sm+ x[i+k-1] - x[i-1]
            if (sm >= k2) pred[i] <- 1 else pred[i] <- 0
        }
	}
    return(mean( abs (pred-x[(k+1):n])))
}
```

##### 【知识点】向量化循环

该部分对应书中2.6小节。

#### 示例代码3

示例代码3代码较为简洁 —— 使用`cusum()`函数来简化求和过程，并使用`i`和`i+k`之间的索引数值差，求算一期内的和（`csx[i+k] - csx[i]`），用于后续计算错误率。

```R
predc <- function(x,k) {
    n <- length(x)
    k2 <- k/2
    pred <- vector(length=n-k)
    csx <- c(0, cusum(x))  # 注意，此处在向量开始处添加了0
    for (i in 1:(n-k)) {
        if (csx[i+k] - csx[i] >= k2) pred[i] <- 1 else pred[i] <- 0
    }
    return(mean(abs(pred-x[(k+1):n])))
}
```

#### 【总结】预测离散型时间序列

一般计算区间内一定步长的和，不使用如下方式（因为慢，计算次数太多了）：

```R
sum(x[i:i+k])
```

使用如下方式：

```R
# 计算结束处和开头处累积和之差
csx[i+k] - csx[i]
```

最后，从计算次数来看，示例代码1的计算次数最多，示例代码2的窗口每次移动时需要进行两次计算，示例代码3只需要计算一次。



### 实例：度量相关性

代码如下，

```R
# findud() converts vector v to 1s, 0s, representing an element increasing or not, relative to the previous one; output length is 1 less than input
findud <- function(v) {
    vud <- v[-1] - v[-length(v)]    # 计算向量中，前与后的差，并根据差的正负来赋值；可以替换为diff(v)
    return(ifelse(vud > 0,1,-1))
}

udcorr <- function(x,y) {
    ud <- lapply(list(x,y), findud)
    return(mean(ud[[1]] == ud[[2]]))
}


x <- c(5,12,13,3,6,0,1,15,16,8,88)
y <- c(4,2,3,23,6,10,11,12,6,3,2)
udcorr(x,y)
```

#### 【知识点】`lapply`

- 上述函数的输入值，要求为列表
- 上述函数的返回值，是一个列表，需要使用`[[]]`，将其提取为向量

#### 【知识点】`diff()`

`diff()`的作用是，计算给定向量之前的时序之差。

```R
diff(x, lag = 1, differences = 1, ...)
# x: a numeric vector or matrix containing the values to be differenced，即输入数据类型为向量或矩阵
# lag: 设置数值为k，表示以索引为n+k - n的数，作为计算结果
# difference: 表示执行diff()函数的次数
```



#### 【知识点】`sign()`

该函数的作用，根据给定向量中的数值是正、负、0，分别转化为1、0、-1。





### 实例：对鲍鱼数据集重新编码

关键点：`ifelse()`的嵌套使用

示例代码如下，

```R
# 1.数据存储在向量中
g
ifelse(g == "M",1,ifelse(g == "F",2,3))

# 2.数据存储在矩阵中
ab[,1] <- ifelse(ab[,1] == "M", 1, ifelse(ab[,1] == "F",2,3))

# 3.将需要重编码的数据存入列表
for (gen in c("M", "F", "I")) grps[[gen]] <- which(g == gen)
```

代码实例如下，

> 在github中的仓库中，没有找到对应数据集。
>
> 下载链接如下：https://archive.ics.uci.edu/ml/machine-learning-databases/abalone/

```R
aba <- read.csv("R/abalone.data", header=F, as.is=T)
grps <- list()
for (gen in c("M","F")) grps[[gen]] <- which(aba[,1] == gen)
abam <- aba[grps$M,]
abaf <- aba[grps$F,]
plot(abam$Length, abam$Diameter)
plot(abaf$Length, abaf$Diameter , pch=" x" ,new=FALSE)
```

结果图如下，

<img src="photo/【R编程艺术】学习笔记（二）/image-20220626135614217.png" alt="image-20220626135614217" style="zoom: 33%;" />

#### 【知识点】查看函数的参数

```R
# e.g. 
args(ifelse)
```

