---
title: Gambler's Ruin Problem
---

*看[比特币白皮书](https://bitcoin.org/bitcoin.pdf "比特币白皮书")看到的一个比较有意思的概率问题，比特币白皮书上的问题和这个类似，讨论的是比特币的安全性，回滚攻击的概率。*

> 庄家有n个筹码，每次有概率p赢得一个筹码，或者概率q（q=1-p）输掉一个筹码。庄家输掉所有钱后，即终止游戏。假设各次赌博都是独立的，求庄家把所有筹码输光的概率。

设概率为$f(n)$,显然我们有$	f(0) = 1 $ 和 $f(+\infty) = 0$，又根据题意和全概率公式，我们有如下递推方程

$$
\begin{alignedat}{1}
f(n) &= pf(n+1) + qf(n-1)
\end{alignedat}
$$


所以，特征方程为

$$
x = px^2 + q
$$

解得

$$
\begin{alignedat}{1}
x &= \frac{1\pm\sqrt{1-4pq}}{2p}
\end{alignedat}
$$

注意到$q=1-p$

$$
\begin{alignedat}{1}
x &= \frac{1\pm\sqrt{(2p-1)^2}}{2p}
\end{alignedat}
$$

考虑$p>0.5$时$x_1=1,x_2=(1-p)/p$，此时
$$
\begin{alignedat}{1}
f(n) = A + B(\frac{1-p}{p})^n
\end{alignedat}
$$

注意到$p>0.5$
$$
\displaystyle\lim_{n \to +\infty}(\frac{1-p}{p})^n = 0
$$

代入$	f(0) = 1 $ 和 $f(+\infty) = 0$，我们立刻就能得到$	A=0,B=1 $

$$
\begin{alignedat}{1}
f(n) = (\frac{1-p}{p})^n
\end{alignedat}
$$

另一方面，$p \le 0.5$时，$x_1=1,x_2=(1-p)/p$(因为不影响结果，两个解交换了顺序)，我们惊奇的发现，两组情况下的解竟然如此一致
$$
\begin{alignedat}{1}
f(n) = A + B(\frac{1-p}{p})^n
\end{alignedat}
$$
此时我们只需注意
$$
\displaystyle\lim_{n \to +\infty}(\frac{1-p}{p})^n = +\infty
$$
代入$	f(0) = 1 $和 $f(+\infty) = 0$，就能得到$	A=1,B=0 $
$$
\begin{alignedat}{1}
f(n) = 1
\end{alignedat}
$$
因此
$$

\begin{alignedat}{2}
f(n) &= (q/p)^n ,\ \ &p>0.5\\
f(n) &= 1,\ &p \le 0.5
\end{alignedat}

$$

对此我又进一步的做了[概率分析](http://www.combination.net.cn/wp-content/uploads/2019/12/Gamblers-Ruin-Problem-Probability.html "概率分析")，输入的参数要在0.5~1之间，发现庄家基本不会输。

> You always said the cards would never do you wrong; The trick you said was never play the game too long.——Bob Seger


> 赌徒进去赌场后，在有钱的庄家面前，玩着不超过50%胜率的游戏，赢钱就只是一个美丽的泡沫。


关于这个问题也很多种解法，这里有一个[构造差分方程](https://www.jianshu.com/p/7df33ae5fb56 "构造差分方程")解的，当然还有一个更为详细的[分析](https://www.mathpages.com/home/kmath084/kmath084.htm "分析")。但是我觉得使用特征方程来解才是计算机专业学生的一般做法。



