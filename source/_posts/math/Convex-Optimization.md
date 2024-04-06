---
title: 最优化理论
tags: ['凸优化']
date: 2024-03-15 21:00:32
categories: Math
excerpt: 最优化理论课堂笔记
mathjax: true
---

# 凸集

## 仿射集和凸集

+ 仿射：
    1. 仿射集合
    
        如果集合C中两个不同的点的直线仍在集合C中, 则称集合C是仿射的

        等价于: 对$\forall x_1, x_2 \in C, $满足$\theta x_1 + (1-\theta) x_2 \in C$
        
    2. 仿射组合（点）
    
        假设$x_1,x_2,\dots,x_k \in C,\theta_1,\theta_2,\dots,\theta_k \in R, $如果$C$是一个仿射集合，并且$\theta_1+\theta_2+\dots+\theta_k = 1$，那么点$\theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k$仍在$C$中

    3. 仿射包（集合）
    
        $\pmb{aff}C = \{ \theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k |x_i \in C , \theta_1+\theta_2+\dots+\theta_k = 1\} $
    
+ 凸集
    1. 凸集
       
        如果集合C中两个不同的点间的线段仍在集合C中, 则称集合C是凸集
        
        等价于: 对$\forall x_1, x_2 \in C, $且$\theta \in [0, 1]$满足$\theta x_1 + (1-\theta) x_2 \in C$

    2. 凸组合 
    
        $\theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k$，其中$\theta_1+\theta_2+\dots+\theta_k = 1，\theta_i \geq 0$
    3. 凸包
    
        $ \pmb{conv}C = \{ \theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k |x_i \in C ,\theta_i \geq 0, \theta_1+\theta_2+\dots+\theta_k = 1\} $
+ 锥
    1. 凸锥
    
        对$\forall x_1, x_2 \in C, $且$\theta_1,\theta_2 \geq 0 $满足$\theta_1 x_1 + \theta_2 x_2 \in C$
        
    2. 锥组合
       
        $\theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k$，其中$\theta_i \geq 0$
        
    3. 锥包
    
        $\{ \theta_1 x_1+\theta_2 x_2+\dots+\theta_k x_k |x_i \in C ,\theta_i \geq 0, \}$

+ 示例

    1. 超平面（$hyperplane$）
       
        $\{ x|a^Tx=b\}, a\in R^n, a \neq 0, b\in R$ 
        
    2. 半空间（$halfspace$）
       
        $\{ x|a^Tx \leq b\},a \in R^n, a \neq 0, b\in R$ 
        
    3. 球（$Euclidean \ ball$）
       
        $B(x_c, r) = \{x|\parallel x - x_c\parallel_2 \leq r\} = \{x|(x - x_c)^T(x - x_c) \leq r^2\}$
        
        $B(x_c,r) = \{x_c + ru|\parallel u\parallel_2 \leq 1\}$
        
    4. 椭球（$ellipsoid$）
    
        $\varepsilon = \{x|(x - x_c)^TP^{-1}(x - x_c) \leq 1\}$
        
        $\varepsilon = \{ x_c + Au|\parallel u\parallel_2 \leq 1\}\ \ \ A = P^{\frac{1}{2}} $
        
        
## 保凸运算

+ 交集: 凸集的交集仍为凸集

    如果对于任意的$\alpha \in A, S_\alpha$都是凸的, 那么所有的集合的交集也是凸的

+ 仿射函数 (线性映射)
    + 定义
    
        如果$f$是一个线性函数和一个常数的和, 即具有 $f(x) = Ax + b$的形式,$A \in R^{m\times n}, b \in R^m$, 函数 $f:R^n \rightarrow R^n$

    + 结论1: 

        假设S是凸的, 则S在f的映射下的象 $f(S) = \{ f(x)|x \in S \}$ 也是凸的
            
        类似地, S在f下的原象 $f^{-1}(S) = \{ x| f(x) \in S \}$ 也是凸的
        
    + eg:
    
        伸缩 $\alpha S = \{\alpha x | x \in S\}$
        
        平移 $S + \alpha = \{ x+\alpha | x\in S\}$
        
        和 $S_1 + S_2 = \{ x + y|x\in S_1, y \in S_2 \}$
        
        直积$S_1 \times S_2 = \{ (x , y)|x\in S_1, y \in S_2 \}$
+ 透视函数

    + 定义
      
        $P:R^{n+1} \rightarrow R^n, P(z,t) = z/t $为透视函数, $z \in R^n, t\in R_{++}$
        
        1）通过透视函数，从$n+1$降维到$n$维
        
        2）最后一个元素，即t值，必须是正数
        
        $e.g. \ (x_1,x_2,x_3) \rightarrow (\frac{x_1}{x_3}, \frac{x_2}{x_3},1) \rightarrow (\frac{x_1}{x_3}, \frac{x_2}{x_3})$
    + 结论
      
        如果$\ C\ $为$\ \pmb{dom}P \ $内的凸集，那么它的象$\{P(C)|x \in C \}$也是凸的
        
        一个凸集在透视函数下的原象也是凸的$P^{-1}(C) = \{ (x,t) \in R^{n+1}|x/t \in C,t > 0 \}$

## 其他

+ 超平面分离定理

    假设$\ C\ $ 和$\ D\ $是两个不相交的凸集, 那么$\exists a \neq 0 ,b, \\ \ s.t. \ \ x \in C 则 a^Tx \leq b, x\in D 则 a^Tx \geq b$
    
    若没有等号，则成为严格分离
    
+ 支撑超平面

    假设$x_0$为集合$C$边界上一点，如果$a \neq 0 $并且对于$\forall x \in C $有$a^Tx \leq a^T x_0,$那么称超平面 $\{x|a^Tx = a^T x_0\}$为集合$C$在$x_0$处的超平面
    
# 凸函数

## 凸函数

+ 定义
  
    $C$是非空凸集，$f: R^n \rightarrow R$定义在C上的函数，如果对$\forall x,y \in C, \alpha \in [0,1],$满足<br><br><center>$f(\alpha x + (1-\alpha )y) \leq af(x) + (1-\alpha)f(y)$</center><br><br>则称$f$为$C$上的凸函数
    
+ 凹函数
  
    如果$-f$为凸函数，则$f$为凹函数
    
+ 结论

    所有的仿射函数（线性函数）是既凸又凹的$ \\ $ 反之若某个函数既凸又凹，则其为仿射函数
    
+ 性质

{% asset_img 1.png %}

## 一阶条件

{% asset_img 2.png %}

## 二阶条件

{% asset_img 3.png %}

## 例子

+ 二次函数

{% asset_img 4.png %}

+ 常见函数
{% asset_img 5.png %}

+ 有趣的函数

{% asset_img 6.png %}

{% asset_img 7.png %}

{% asset_img 8.png %}
