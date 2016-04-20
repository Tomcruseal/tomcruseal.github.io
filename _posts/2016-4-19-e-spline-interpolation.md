---
layout: post
title: Interpolation by Spline Functions and Others
categories: [Numerical Method]
tags: []
fullview: true
comments: true
---
>**Firstly**,because of the arrangement of the textbook,I read the parts of Least-Square line priory to interpolation by spline function,so I note the introduction of Least-Square line firstly.
Linear function has the form:$y=f(x)=Ax+B$
So,how to find the best approximation of such form that goes near the points?Errors is a vital concept.The error is defined as $e_k=f(x_k)-y_k$

>**There** are 3 kinds of errors:
* Maximum error:$E_{\infty}(f)=max\{|f(x_k)-y_k|\}$
* Average error:$E_1(f)=\frac{1}{N}\sum_{k=1}^{N}|f(x_k)-y_k|$
* Root-Mean-Square error:$E_2(f)=[\frac{1}{N}\sum_{k=1}^{N}(f(x_k)-y_k)^2]^\frac{1}{2}$

>**Since** the 3rd norm is easier to be minimized computationally,it is the traditon choice.

>**Theorem 5.1 (Least-Square Line)**. Suppose that $$\{(x_{k},y_{k})\}_{k=1}^N$$ are N points,where the abscissas $$\{x_k\}^{N}_{k=1}$$ are distinct.The coefficients of the least-squares line:
$$
y=Ax+B
$$

>are the solution to the following linear system(**normal equation**)
$$
A\sum^{N}_{k=1}x_k^2+B\sum^{N}_{k=1}x_k=\sum^{N}_{k=1}x_ky_k. 
$$

>
$$
A\sum^{N}_{k=1}x_k+NB=\sum^{N}_{k=1}y_k
$$

>**Below** is the proof of it:
According to the definition of Root-Mean-Square error,we can write E as:

>
$$
E(A,B)=\sum^{N}_{k=1}(Ax_k+B-y_k)^2
$$
which mens that E is the function of variables A and B
Then partial derive $E(A,B)$:
$$
\frac{\partial E(A,B)}{\partial A}=\sum^{N}_{k=1}2(Ax_k+B-y_k)x_k=2\sum^{N}_{k=1}Ax_{k}^2+Bx_k-x_ky_k
$$
$$
\frac{\partial E(A,B)}{\partial B}=\sum^{N}_{k=1}2(Ax_k+B-y_k)\cdot1=2\sum^{N}_{k=1}Ax_k+B-y_k
$$

>**By** set them to 0,we can get the **normal equations**
Then goes **Piecewise Cubic Splines**
Suppose that $$\{x_k,y_k\}^{N}_{k=0}$$ are ${N+1}$ points,where $a=x_0< {x_1} < \cdots < x_N = b$.The function $S(x)$ is called a cubic spline if there exists $N$ cubic polynomials $S_k(x)$` with coeficients $s_{k,0}$,$s_{k,1}$,$s_{k,2}$,and $s_{k,3}$ that satisfy the following properties:
$$
S(x)=S_k(x)=S_{k,0}+S_{k,1}(x-x_k)+S_{x,2}(x-x_k)^2+S_{k,3}(x-x_k)^3 ,for\ x \in[x_k,x_{k+1}]
$$
$$
S(x_k)=y_k ,\ k=0,1,2,\cdots, N-1 
$$
$$
S_k(x_{k+1})=S_{k+1}(x_{k+1}), k=0.1.\cdots,N-2
$$
$$
S'_k(x_{k+1})=S'_{k+1}(x_{k+1}), k=0.1.\cdots,N-2
$$
$$
S''_k(x_{k+1})=S''_{k+1}(x_{k+1}), k=0.1.\cdots,N-2
$$

>**Totally**,there are $4N-2$ equations while $4N$ coeficients to be determined,so 2 more preconditions we have to know--2 endpoints,usually $S'(x)$and $S''(x)$ at $x_0$and $x_N$
Here belows are some inductions:
Since $S(x)$ is piecewise cubic,$S''(x)$ is piecewise linear in $[x_0,x_N]$According to Lagrange Interpolation formula:
$$
S''_k(x)=S''(x_k)\frac{x-x_{k+1}}{x_k-x_{k+1}}+S''(x_{k+1})\frac{x-x_k}{x_{k+1}-x_k}
$$
Assume that $m_k=S''(x_k)$,$h_k=x_{k+1}-x_k$,we can rewrite the above equation as:
$$
S''_k(x)=\frac{m_k}{h_k}(x_{k+1}-x)+\frac{m_{k+1}}{h_k}(x-x_k)
$$
According to the textbook,
$$
S_k(x)=\frac{m_k}{6h_k}(x_{k+1}-x)^3+\frac{m_{k+1}}{6h_k}(x-x_k)^3+p_k(x_{k+1}-x)+q_k(x-x_k)
$$

>**However I still could't understand why to handle the constant $C$ like that,I have tried another way and obtain another form of $S(x)$ whereas difference is between terms whose degree is smaller than 3.I am not sure if the difference does make sense**
Then substituting $x_k$ and $x_{k+1}$ into the equaiton and using the values that $y_k=S_k(x_k)$ and $y_{k+1}=S_k(x_{k+1})$above:
$$
y_k=\frac{m_k}{6}h_k^2+p_kh_k
$$
$$
y_{k+1}=\frac{m_{k+1}}{6}h_k^2+q_kh_k
$$
then it is easy:$p_k=\frac{y_k}{h_k}-\frac{m_kh_k}{6}$,$q_k=\frac{y_{k+1}}{h_k}-\frac{m_{k+1}h_k}{6}$
Then,
$$
S_k(x)=\frac{m_k}{6h_k}(x_{k+1}-x)^3+\frac{m_{k+1}}{6h_k}(x-x_k)^3+(\frac{y_k}{h_k}-\frac{m_kh_k}{6})(x_{k+1}-x)+(\frac{y_{k+1}}{h_k}-\frac{m_{k+1}h_k}{6})(x-x_k)
$$
$$
S'_k(x)=-\frac{m_k}{2h_k}(x_{k+1}-x)^2+\frac{m_{k+1}}{2h_k}(x-x_k)^2+(\frac{y_k}{h_k}-\frac{m_kh_k}{6})\cdot-(1)+(\frac{y_{k+1}}{h_k}-\frac{m_{k+1}h_k}{6})
$$
So
$$
S'_k(x_k)=-\frac{m_k}{2h_k}h_k^2+(\frac{y_k}{h_k}-\frac{m_kh_k}{6})\cdot(-1)+(\frac{y_{k+1}}{h_k}-\frac{m_{k+1}h_k}{6})=-\frac{m_k}{3}h_k+d_k+(-\frac{m_{k+1}}{6})h_k,where \ d_k=\frac{y_{k+1}-y_k}{h_k}
$$
By replaceing k with k-1,
$$
S'_{k-1}(x)=-\frac{m_{k-1}}{2h_{k-1}}(x_{k}-x)^2+\frac{m_{k}}{2h_{k-1}}(x-x_{k-1})^2+(\frac{y_{k-1}}{h_{k-1}}-\frac{m_{k-1}h_{k-1}}{6})\cdot-(1)+(\frac{y_{k}}{h_{k-1}}-\frac{m_{k}h_{k-1}}{6})
$$
$$
S'_{k-1}(x_k)=\frac{m_k}{3}h_{k-1}+d_{k-1}+\frac{m_{k-1}h_{k-1}}{6}
$$
Since $S'_{k-1}(x_k)=S'_k(x_k)$,we can obtain:
$$
2m_k(h_{k-1}+h_k)+m_{k-1}h_{k-1}+m_{k+1}h_k=6u_k,\ where \ u_k=(d_k-d_{k-1})
$$

>**There** are 5 conditions for endpoints constraints,I only list 3 of them:
for clamped cubic($S'(x_0)\ and\ S'(x_n)$are known already):
* $m_0=\frac{3}{h_0}(d_0-S'(x_0))-\frac{m_1}{2}$
*  $m_N=\frac{3}{h_{N-1}}(S'(x_N)-d_{N-1})-\frac{m_{N-1}}{2}$

>for natural cubic spline:
* $m_0=0$
* $m_N=0$

>for entrapolate S''(x) to the endpoints:
* $m_0=m_1-\frac{h_0(m_2-m_1)}{h_1}$
* $m_N=m_{N-1}+\frac{h_{N-1}(m_{N-1}-m_{N-2})}{h_{N-2}}$

>Coefficients for $\{s_{k,j}\}$ for $S_k(x)$ are computed using the formulas:
* $s_{k,0}=y_k$ 
* $s_{k,1}=d_k-\frac{h_k(m_{k+1}+2m_k)}{6}$
* $s_{k,2}=\frac{m_k}{2}$
* $s_{k,3}=\frac{m_{k+1}-m_k}{6h_k}$

>**Next** I will give the **proof** of it:
$S(x_k)=S_k(x_k)=s_{k,0}=y_k$
then,constantly derive $S(x)$ :
$$
S^{\prime}(x)=S^{\prime} _k(x)=s_{k,1}+2s_{k,2}(x-x_k)+3s_{k,3}(x-x_k)^2,so\  S^{\prime} (x_k)=S\prime _k(x_k)=s_{k,1}=d_k-\frac{h_k(m_{k+1}+2m_k)}{6}
$$
$$
S''(x)=S''_k(x)=2s_{k,2}+6s_{k,3}(x-x_k)=m_k,so s_{x,2}=\frac{m_k}{2}
$$
For $s_{k,3}$,assume it $s_{k,3}=\frac{m_{k+1}-m_k}{6h_k}$ is right,then
$\frac{S''(x_{k+1})-S''(x_k)}{6(x_{k+1}-x_k)}\cdot 6(x-x_k)+S^{(2)}(x_k)=S^{(2)}_k(x)$
the first term can be viewd as slope,and it is a good approximation to the right.(**I don't know how to explain it...**)
I'd like to include more,but,maybe next time.
