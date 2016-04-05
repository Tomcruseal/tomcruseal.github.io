---
layout: post
title: Proof for Taylor's Theorem
categories: [Numerical Method]
tags: []
fullview: true
comments: true
---
>**In** the class **Numerical Method**,a question arose for some times that how to prove **Taylor's Theorem**?At the early trials,I couldn't even think of any evidence to the right proof,so I looked over my Calculus book and finally got the answer.
>**Here** below is **Taylor's Theorem**:
Let $f$ be a function whose $(n+1)$st derivative $f^{(n+1)}(x)$ exists for each x in an open interval $I$ containing $a$.Then,for each $x$ in $I$,
$$
f(x)=f(a)+f'(a)(x-a)+\frac{f''(a)}{2!}(x-a)^2+...+\frac{f^{(n)}(a)}{n!}(x-a)
^n+R_{n}(x)$$
where the remiander (or error) $R_n(x)$ is given by the formula
$$
R_n(x)=\frac{f^{(n+1)}(c)}{(n+1)!}(x-a)^{n+1}
$$
and $c$ is some point between $x$ and $a$ .
>
**Obviously**,this theorem can be extented from interval $I$ to a $Field$,strictly,but I just leave it at this time.
>
>**Next**,I will note my proof,which is modified from that in textbook.
>From **Taloy's Theorem**,we can write $R_n(x)$ as:
$$
R_n(x)=f(x)-f(a)-f'(a)(x)(x-a)-\frac{f''(a)}{2!}(x-a)^2-...-\frac{f^{(n)}(a)}{n!}(x-a)^n
$$
Then,consider a new function $g$ on $I$ whrere $x$ and $a$ are constants:
>
$$
g(t)=f(x)-f(t)-f'(t)(x-t)-\frac{f''(t)(x-t)^2}{2!}-...-\frac{f^{(n)}(t)}{n!}(x-t)^n-R_n(x)\frac{(x-t)^{n+1}}{(x-a)^{n+1}}
$$
Clearly,$g(x)=0$
>
>Then,
$$
g(a)=f(x)-f(a)-f'(a)(x-a)-\frac{f''(a)}{2!}(x-a)^2-...-\frac{f^{(n)}}{n!}(x-a)^n-R_n(x)\frac{(x-a)^{n+1}}{(x-a)^{n+1}}=R_n(x)-R_n(x)=0.
$$
>
>**Since** a and x are constants in $I$ and $g(a)=g(x)=0$,according to **Mean Valure Theorem for Derivatives**,there exists a c such that $g'(c)=0$
>then
$$
g'(t)=0-f'(t)-[f'(t)(-1)+f''(t)(x-t)]-\frac{1}{2!}[2(x-t)(-1)f''(t)+(x-t)^2f(t)^{(3)}]-...-\frac{1}{n!}[n(x-t)^{n-1}(-1)f^{(n)}(t)+f^{(n+1)}(t)(x-t)^n]+(n+1)R_n(x)\frac{(x-t)^n}{(x-a)^{n+1}}=-\frac{1}{n!}f^{n+1}(t)(x-t)^n+(n+1)R_n(x)\frac{(x-t)^n}{(x-a)^{n+1}}
$$
>
>and then
$$
g'(c)=0=-\frac{1}{n!}f^{n+1}(c)(x-c)^n+(n+1)R_n(x)\frac{(x-c)^n}{(x-a)^{n+1}}
$$
>
$$
\Rightarrow 
$$
>
$$
\frac{1}{n!}f^{n+1}(c)(x-c)^n=(n+1)R_n(x)\frac{(x-c)^n}{(x-a)^{n+1}}
$$
>
$$
\Rightarrow
$$
>
$$
R_n(x)=\frac{1}{(n+1)!}f^{n+1}(c)(x-c)^n\frac{(x-a)^{n+1}}{(x-c)^n}=\frac{f^{n+1}(c)}{(n+1)!}(x-a)^{n+1}
$$
>
>**By** now,we have successfully proved it.For this mehthod of proof ,the most difficult part is how to imagine to construct the function $g$