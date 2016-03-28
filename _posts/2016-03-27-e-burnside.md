---
layout: post
title: An interesting question about Burnside Lemma
categories: [Algebra]
tags: []
fullview: true
comments: true
---
>**Some** months ago when I learned discrete algebra,a very interesting problem occured to me--How many ways are there to colour 4 vetices of a square with 2 kinds of colour?
>
>**The** theory is **Burside's Counting Theorem**.Before going through the **Burside's Counting Theory**,I have to review some other theorems.
>
>**Firstly**,what is G-set?According to the textbook,the definition of G-set is below:
Let $X$ be a set and $G$ be a group.A (left)action of $G$ on $X$ is a map $G\times X\rightarrow X $given by $(g,x)\rightarrow gx$,where
1.$ex=x$ for all $x\in X$;(propertity of identity)
2.$(g_1g_2)x=g_1(g_2x)$for all $x\in X$ and all $g_1,g_2\in G$(property of association)
>
>**Secondly**,the concept of conjugation is also important.Let $G$ be a group and suppose that $X=G$.If $H$ is a subgroup of $G$,then $G$ is an $H$-set under conjugation;that is,we can define an action of H on G,$H\times G\rightarrow G$ via $(h,g)\rightarrow hgh^{-1}$
In my opinion,conjugation can be interpreted as  a way to divide a group into certain parts and the elements in each partition of the group shared the same rules of arithmetic--$hgh^{-1}$And such parts are called **Conjugate classes**
>
>**Other** concepts such as **Orbits,stabilizer,centralizer** .etc all have connection with **Burside's Counting Theorem**.Especially Orbit,I believe it can be viewed as a set which is one-to-one and onto reflection to the conjugation classes,on strictless condition.I won't go detail into those theorems because to illustrate them all is so time-consuming that I decide to omit some of the pre-knowledge.However,they are also important in obtaining **Burside's Counting Theory**.
>
>**For** **Burside's Counting Theory**,the complete defination is:
Let $G$ be a **finite** group acting on a set $X$ and let $k$ denote the number of orbits of $X$.Then
>
$$k=\frac{1}{|G|}\sum_{g\in G}|X_g|$$
>
>**But how to prove it? I haven't understand the theory of the proof in the textbook,maybe I can solve it in the future.**
>
>**Now**,we back to the interesting problem--how many ways are there to colour a 4 vertices of a square with black and white?
>Considering **Dihedral group**.Since $D_n$is a subgroup of $S_n$ and its order is $2n$ and it reflects 2 kinds of motions to a regular n-gon:**Rotation and Reflection**.(Actually,the elements of group $D_n$ satisfy the following 3 relations:$1.r^n=e;\ 2.s^2=e;\ 3.srs=r^{-1}$ where e is the identity. **are there more than 1 identities in a Dihedral group?**)So,for a square,consider the $D_4$ group,since a dihedral group is a permutation group,the elements of the $D_4$ are:$(1)\ (13)\ (24)\ (1432)\ (1234)\ (12)(34)\ (14)(23)\ (13)(24)$
>From **Burnside theorem**,we can compute that:
>
$X_{(1)}=2^4$
(4 corners with each 2 possible colours)
>
$X_{(13)}=2\ast (2\ast 2)$
(this means that corner 1 and 3 have the same colour,both white or both black)
>
$X_{(24)}=2\ast (2\ast 2)$
>
>$X_{(1432)}=2$
>
$X_{(1234)}=2$
>
$X_{(12)(34)}=2\ast 2$
>
$X_{(14)(23)}=2\ast 2$
>
>$X_{(13)(24)}=2\ast 2$
>
>**Since** $|G|=2n=8$,so $k=\frac{1}{8}\ast (16+8+8+2+2+4+4+4)=6$
there are 6 ways to colour such a square's  vertices with colour black and white.
>
>**At** last,I will note an extended proposition and leave its proof for future study:
Let $G$ be a permutation group of $X$ and $\tilde{X}$ the set of functions from $X$ to $Y$.Then there exists a permutation group $\tilde{G}$ acting on $\tilde{X}$,where $\tilde{\sigma}\in \tilde{G}$ is defined by $\tilde{\sigma}(f)=f\circ\sigma for\ \sigma\in G$ and $f \in\tilde{X}$.Furthermore,if n is the number of cycles in the cycle decomposition of $\sigma$,then $|\tilde{X_{\sigma}}|=|Y|^{n}$.**Still confused to this part......**