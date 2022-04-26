<!--yml
category: 未分类
date: 2022-04-26 14:47:26
-->

# [DASCTF 2020 四月春季赛] not_RSA 题解_随缘懂点密码学的博客-CSDN博客

> 来源：[https://blog.csdn.net/qq_42667481/article/details/105757426](https://blog.csdn.net/qq_42667481/article/details/105757426)

# 题目分析

## 题目内容

**这道题是 Paillier加密方案，然而做题时我并不知道 …*

已 知 c ,   n = p ⋅ q ,   g = n + 1    。 0 < r < n ,   r 未 知 。 求 m 已知 c,\ n = p\cdot q, \ g=n+1 \ \ 。0<r<n, \ r 未知。求 m 已知c, n=p⋅q, g=n+1  。0<r<n, r未知。求m

加 密 过 程 为 ： 加密过程为： 加密过程为：
c ≡ g m ( m o d   n 2 ) ⋅ r n ( m o d   n 2 )   ( m o d   n 2 ) ≡ g m r n ( m o d   n 2 ) c ≡ g^m (mod \ n^2) \cdot r^n (mod \ n^2) \ (mod \ n^2) \\ \equiv g^m r^n (mod \ n^2) c≡gm(mod n2)⋅rn(mod n2) (mod n2)≡gmrn(mod n2)

## 解题思路

有 两 个 未 知 量 r 和 m ， 考 虑 先 求 r 。 0 < r < n , 故 求 r   m o d   n 有两个未知量 r 和 m ，考虑先求 r。0<r<n, 故求 r\ mod\ n 有两个未知量r和m，考虑先求r。0<r<n,故求r mod n

注 意 到   g = n + 1     ,    c ≡ g m ⋅ r n   ( m o d   n 2 ) 注意到\ g = n+1 \ \ \ , \ \ c ≡ g^m \cdot r^n \ (mod \ n^2) 注意到 g=n+1   ,  c≡gm⋅rn (mod n2)

而 根 据 g m 的 二 项 式 展 开 ， 易 知 g m ≡ 1   ( m o d   n ) 而根据 g^m 的二项式展开，易知 g^m\equiv1\ (mod \ n) 而根据gm的二项式展开，易知gm≡1 (mod n)。

因 此 ， r n ≡ c   ( m o d   n ) 。 要 解 这 个 方 程 就 要 求 出 n 的 分 解 。 因此，r^n \equiv c\ (mod \ n) 。要解这个方程就要求出 n 的分解。 因此，rn≡c (mod n)。要解这个方程就要求出n的分解。

用 y a f u 求 出 p , q 。 计 算 φ ( n ) = ( p − 1 ) ( q − 1 ) 用yafu求出 p,q 。计算 \varphi(n) =(p-1)(q-1) 用yafu求出p,q。计算φ(n)=(p−1)(q−1) 。

( n , φ ( n ) ) = 1 ， 因 此 计 算 d = n − 1 ( m o d   φ ( n ) ) ， 得 r ≡ ( r n ) d   ( m o d   n ) (n,\varphi(n)) =1 ， 因此计算 d=n^{-1}(mod\ \varphi(n))，得 r\equiv(r^n)^d\ (mod \ n) (n,φ(n))=1，因此计算d=n−1(mod φ(n))，得r≡(rn)d (mod n)

接 下 来 ， 再 用 c ≡ g m ⋅ r n   ( m o d   n 2 ) ， 再 对 g m 二 项 式 展 开 得 \\接下来，再用 c ≡ g^m \cdot r^n \ (mod \ n^2) ，再对 g^m 二项式展开得 接下来，再用c≡gm⋅rn (mod n2)，再对gm二项式展开得

m n + 1 ≡ c ⋅ r − n   ( m o d   n 2 )    ⟺    n m ≡ c ⋅ r − n − 1 ( m o d   n 2 ) mn+1 \equiv c \cdot r^{-n} \ (mod\ n^2) \iff nm \equiv c \cdot r^{-n}-1(mod\ n^2) mn+1≡c⋅r−n (mod n2)⟺nm≡c⋅r−n−1(mod n2)

记   a = c ⋅ r − n − 1 。 n   ∣   a ， 故 可 对 上 式 左 右 和 模 同 除 以 n ， 得 记\ a=c \cdot r^{-n}-1。n \ |\ a ，\\ 故可对上式左右和模同除以n，得 记 a=c⋅r−n−1。n ∣ a，故可对上式左右和模同除以n，得 m ≡ a n   ( m o d   n ) \\ m \equiv \dfrac{a}{n}\ (mod\ n) m≡na​ (mod n)
接 下 来 就 是 不 解 释 连 招 了 接下来就是不解释连招了 接下来就是不解释连招了

## exploit.py (solve.py)

```
 from gmpy2 import *
from Crypto.Util.number import long_to_bytes

p = 80006336965345725157774618059504992841841040207998249416678435780577798937819
q = 80006336965345725157774618059504992841841040207998249416678435780577798937447
n = 6401013954612445818165507289870580041358569258817613282142852881965884799988941535910939664068503367303343695466899335792545332690862283029809823423608093
c = 29088911054711509252215615231015162998042579425917914434962376243477176757448053722602422672251758332052330100944900171067962180230120924963561223495629695702541446456981441239486190458125750543542379899722558637306740763104274377031599875275807723323394379557227060332005571272240560453811389162371812183549

g = n + 1

φ = (p-1) * (q-1)     

r = pow(c%n, invert(n, φ), n)

a = c * pow(invert(r,n*n), n, n*n) % (n*n) - 1

m = a//n 
flag = long_to_bytes(m)

if __name__ == '__main__':

    print(flag) 
```

## TODO: Pailier方案总结