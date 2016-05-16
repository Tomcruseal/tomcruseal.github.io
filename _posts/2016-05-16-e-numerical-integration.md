---
layout: post
title: "Numerical Integration"
categories: "Numerical Method"
date:  2016-05-16 11:07:00 +0800
---

**Many** days passed since I updated last time.Since I was busy with my Software Engineering project rencently,yet I have no time taking care of  my blog.Today is a little note of some critical points in my Numerical Method class,and I paln to write another one after the presentation of my SE project,which is due on the day after tomorrow.  
Below is my notes of Numerical Integration:  
We know that some integrations are difficult to compute by hands,for example,indeterminate or improper forms.And numerical integration is a primary tool used by engineers and scientists to obtain approximate answers for those integrals.  
Some of the closed Newton-Cotes quadrature formulas:  
$$
\int_{x_0}^{x_1} f(x)dx \approx \frac{h}{2}(f_0+f_1)
$$  
$$
\int_{x_0}^{x_2} f(x)dx \approx \frac{h}{3}(f_0+4f_1+f_2)
$$  
$$
\int_{x_0}^{x_3} f(x)dx \approx \frac{3h}{8}(f_0+3f_1+3f_2+f_3)
$$  
They are **trapezoidal rule**,**Simpson's rule** and **Simpson's $\frac{3}{8}$ rule**.Here,I don't give the progress of induction of those rules.  
**Newton-Cotes Precision:**  
Here,I take down the formular of the error:
$$
E(f)=\left\{
\begin{aligned}
\frac{f^{(n+2)}(c)}{(n+2)!} \int_{a}^{b} (x-x_0)\prod_{i=0}^{n}(x-x_i)dx,\text{n is even} \\
\frac{f^{(n+1)}(c)}{(n+1)!} \int_{a}^{b} \prod_{i=0}^{n}(x-x_i)dx,\text{n is odd}
\end{aligned}
\right.
$$  
That's all.I will update a few days later,which is about dajngo in my SE project.
