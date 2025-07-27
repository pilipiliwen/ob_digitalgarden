---
{"dg-publish":true,"permalink":"/obsidian_learning/LaTex 参考手册/"}
---

# 参考
[LaTeX公式手册(全网最全) - 樱花赞 - 博客园](https://www.cnblogs.com/1024th/p/11623258.html)
[Latex 在线编辑器 Equation Editor for online mathematics - create, integrate and download](https://editor.codecogs.com/)
[SimpleTex - 网页公式编辑器](https://simpletex.net/formula_editor)
# 如何插入公式
LaTeX的数学公式有两种：行中公式和独立公式（行间公式）。
行中公式放在文中与其它文字混编，独立公式单独成行。

- 行中公式可以用如下方法表示：
`$ 数学公式 $`
- 独立公式可以用如下方法表示：
`$$ 数学公式 $$`

# 字体
## 希腊字母
输入 `\小写希腊字母英文全称` 和 `\首字母大写希腊字母英文全称` 来分别输入小写和大写希腊字母。

```Latex
\alpha\ \beta\ \gamma\ \delta\ \epsilon\ \zeta\ \eta\ \theta
```
$\alpha\ \beta\ \gamma\ \delta\ \epsilon\ \zeta\ \eta\ \theta$

```Latex
\iota \kappa \lambda \mu \nu \omicron \xi \pi
```
$\iota\ \kappa\ \lambda\ \mu\ \nu\ \omicron\ \xi\ \pi$

```Latex
\rho \sigma \tau \upsilon \phi \chi \psi \omega
```
$\rho\ \sigma\ \tau\ \upsilon\ \phi\ \chi\ \psi\ \omega$

## 部分字体简称
若要对公式的某一部分字符进行字体转换，可以用 `{\字体 {需转换的部分字符}}` 命令，其中 `\字体` 部分可以参照下表选择合适的字体。一般情况下，公式默认为意大利体 $italicitalic$ 。
```Latex
|\rm|罗马体|SampleSample|\cal|花体|SAMPLESAMPLE|  
|\it|意大利体|SampleSample|\Bbb|黑板粗体|SAMPLESAMPLE|  
|\bf|粗体|SampleSample|\mit|数学斜体|SAMPLESAMPLE|  
|\sf|等线体|SampleSample|\scr|手写体|SAMPLESAMPLE|  
|\tt|打字机体|SampleSample|\frak|旧德式字体|SampleSample|
```
$$
\begin{align*}
&|\rm|罗马体|SampleSample|&\cal|花体|SAMPLESAMPLE|\\
&|\it|意大利体|SampleSample|&\Bbb|黑板粗体|SAMPLESAMPLE|\\ 
&|\bf|粗体|SampleSample|&\mit|数学斜体|SAMPLESAMPLE|\\
&|\sf|等线体|SampleSample|&\scr|手写体|SAMPLESAMPLE|\\
&|\tt|打字机体|SampleSample|&\frak|旧德式字体|SampleSample|\\
\end{align*}

$$
## 注释文本
使用 `\text {文字}` 来添加注释文本（注释文本不会被识别为公式，不用斜体显示）。`\text {文字}` 中仍可以使用 `$公式$` 插入其它公式。
```Latex
\begin{equation}
f (n)= 
\begin{cases}  n/2, & \text {if $n$ is even} \\ 
3 n+1, & \text{if $n$ is odd}
\end{cases}
\end{equation}
```
$$
\begin{equation}
f(n)= 
\begin{cases}  n/2, & \text {if $n$ is even} \\ 
3n+1, & \text{if $n$ is odd}
\end{cases}
\end{equation}
$$

# 基础表示

## 运算符
```Latex
+, -, \pm, \mp, \dotplus
```
$+, -, \pm, \mp, \dotplus$

```LaTex
\times, \div, \divideontimes, /, \backslash
```
$\times, \div, \divideontimes, /, \backslash$


```LaTex
\cdot, * \ast, \star, \circ, \bullet
```
$\cdot, * \ast, \star, \circ, \bullet$

## 根号
```LaTex
\surd, \sqrt{2}, \sqrt[n]{}, \sqrt[3]{\frac{x^3+y^3}{2}}
```
$\surd, \sqrt{2}, \sqrt[n]{}, \sqrt[3]{\frac{x^3+y^3}{2}}$

## 模运算
```LaTex
s_k \equiv 0 \pmod{m}
```
$s_k \equiv 0 \pmod{m}$

## 微分及导数
```Latex
dt, \mathrm{d}t, \partial t, \nabla\psi
```
$dt, \mathrm{d}t, \partial t, \nabla\psi$

```LaTex
dy/dx, \mathrm{d}y/\mathrm{d}x, \frac{dy}{dx}, \frac{\mathrm{d}y}{\mathrm{d}x}, \frac{\partial^2}{\partial x_1\partial x_2}y
```
$dy/dx, \mathrm{d}y/\mathrm{d}x, \frac{dy}{dx}, \frac{\mathrm{d}y}{\mathrm{d}x}, \frac{\partial^2}{\partial x_1\partial x_2}y$

```LaTex
\prime, \backprime, f^\prime, f', f'', f^{(3)}, \dot y, \ddot y
```
 $\prime, \backprime, f^\prime, f', f'', f^{(3)}, \dot y, \ddot y$

## 上下标
```Latex
a^2 , a^{2+2}
```
$a^2,a^{2+2}$

```Latex
a_2 , a_{i,j}
```
$a_2, a_{i,j}$

```Latex
x_2^3,{}_1^2\! X_3^4
```
$x_2^3,{}_1^2\! X_3^4$

## 导数
```Latex
x'
```
$x'$
## 向量
```Latex
\vec{c}
```
$\vec{c}$

## ## 界限，极限
```LaTeX
\lim_{n \to \infty}x_n
```
$\lim_{n \to \infty}x_n$

```LaTeX
\begin{matrix} \lim_{n \to \infty}x_n \end{matrix}
```
$\begin{matrix} \lim_{n \to \infty}x_n \end{matrix}$

```Latex
\min x, \max y, \inf s, \sup t
```
$\min x, \max y, \inf s, \sup t$

```Latex
\lim u, \liminf v, \limsup w
```
$\lim u, \liminf v, \limsup w$


```Latex
\lim_{x \to \infty} \frac{1}{n(n+1)}
```
$\lim_{x \to \infty} \frac{1}{n(n+1)}$
$\lim_{x \to \infty}$

```Latex
\dim p, \deg q, \det m, \ker\phi
```
$\dim p, \deg q, \det m, \ker\phi`$

## 积分
### 求和（累加）
```Latex
\sum_{k=1}^N k^2
```
$\sum_{k=1}^N k^2$

```Latex
\begin{matrix} \sum_{k=1}^N k^2 \end{matrix}
```
$\begin{matrix} \sum_{k=1}^N k^2 \end{matrix}$

### 求积（累乘）
```LaTeX
\prod_{i=1}^N x_i
```
$\prod_{i=1}^N x_i$

```LaTeX
\begin{matrix} \prod_{i=1}^N x_i \end{matrix}
```
$\begin{matrix} \prod_{i=1}^N x_i \end{matrix}$

### 积分
```LaTeX
\int_{-N}^{N} e^x\, {\rm d}x
本例中 `\,` 和 `{\rm d}` 部分可省略，但建议加入，能使式子更美观。`{\rm d}`可以用`\mathrm{d}`等价替换。
```
$\int_{-N}^{N} e^x\, {\rm d}x$

```LaTeX
\begin{matrix} \int_{-N}^{N} e^x\, \mathrm{d}x \end{matrix}
（矩阵中积分符号变小）
```
$\begin{matrix} \int_{-N}^{N} e^x\, \mathrm{d}x \end{matrix}$

```LaTeX
\iint_{D}^{W} \, \mathrm{d}x\,\mathrm{d}y
```
$\iint_{D}^{W} \, \mathrm{d}x\,\mathrm{d}y$

```LaTeX
\iiint_{E}^{V} \, \mathrm{d}x\,\mathrm{d}y\,\mathrm{d}z
```
$\iiint_{E}^{V} \, \mathrm{d}x\,\mathrm{d}y\,\mathrm{d}z$

```LaTeX
\oint_{C} x^3\, \mathrm{d}x + 4y^2\, \mathrm{d}y
```
$\oint_{C} x^3\, \mathrm{d}x + 4y^2\, \mathrm{d}y$

## 分数
通常使用 `\frac {分子} {分母}` 命令产生一个分数，分数可嵌套。  
便捷情况可直接输入 `\frac ab` 来快速生成一个 $\frac ab$。  
如果分式很复杂，亦可使用 `分子 \over 分母` 命令，此时分数仅有一层。

```LaTeX
\frac{2}{4}=0.5
```
$\frac{2}{4}=0.5$

```LaTeX
\tfrac{2}{4} = 0.5 小型分数
```
$\tfrac{2}{4} = 0.5$

```LaTeX
\cfrac{2}{c + \cfrac{2}{d + \cfrac{2}{4}}} = a` 连分式（大型嵌套分式
```
$\cfrac{2}{c + \cfrac{2}{d + \cfrac{2}{4}}} = a$

## 括号和空格
### 括号
`()`、`[]` 和 `|` 表示符号本身，使用 `\{\}` 来表示 `{}`
```LaTeX
(\frac{1}{2}),[\frac{1}{2}],|\frac{1}{2}|
```
$(\frac{1}{2}),[\frac{1}{2}],|\frac{1}{2}|$

```LaTeX
\left\{ \frac {1}{32} \right\}
```
$\left\{ \frac {1}{32} \right\}$

### quad 空格
```LaTeX
\alpha\qquad\beta
```
$\alpha\qquad\beta$

```LaTeX
1 \quad\ 2
```
$1 \quad\ 2$

### 大空格
```LateX
\alpha\ \beta
```
$\alpha\ \beta$

# 矩阵、条件表达式、方程组
- 语法：
```Latex
\begin{类型} 
公式内容 
\end{类型}
```

- 类型可以是：矩阵 `matrix` `pmatrix` `bmatrix` `Bmatrix` `vmatrix` `Vmatrix`、条件表达式 `cases`、多行对齐方程式 `aligned`、数组 `array`。
- 在公式内容中：在每一行中插入 `&` 来指定需要**对齐**的内容，在每行结尾处使用 `\\` **换行**。

## 无框矩阵
```Latex
\begin{matrix} 
x & y \\ 
z & v 
\end{matrix}
```
$$
\begin{matrix} 
x & y \\ 
z & v 
\end{matrix}
$$

## 有框矩阵
```Latex
\begin{vmatrix}
x & y \\
z & v 
\end{vmatrix}
```

$$
\begin{vmatrix}
x & y \\
z & v 
\end{vmatrix}
$$
使用 `\cdots` ⋯⋯ , `\ddots` ⋱⋱ , `\vdots` ⋮⋮ 来输入**省略符号**。

```Latex
\begin{bmatrix}
0      & \cdots & 0 \\ 
\vdots & \ddots & \vdots \\ 
0      & \cdots & 0 
\end{bmatrix}
```
$$
\begin{bmatrix}
0      & \cdots & 0 \\ 
\vdots & \ddots & \vdots \\ 
0      & \cdots & 0 
\end{bmatrix}
$$

##  多行等式、同余式
```Latex
\begin{aligned} 
3^{6n+3}+4^{6n+3} 
&\equiv (3^3)^{2n+1}+(4^3)^{2n+1}\\ 
&\equiv 27^{2n+1}+64^{2n+1}\\
&\equiv 27^{2n+1}+(-27)^{2n+1}\\
&\equiv 27^{2n+1}-27^{2n+1}\\
&\equiv 0 \\ 
\end{aligned}
```
$$
\begin{aligned} 
3^{6n+3}+4^{6n+3} 
&\equiv (3^3)^{2n+1}+(4^3)^{2n+1}\\ 
&\equiv 27^{2n+1}+64^{2n+1}\\
&\equiv 27^{2n+1}+(-27)^{2n+1}\\
&\equiv 27^{2n+1}-27^{2n+1}\\
&\equiv 0 \\ 
\end{aligned}
$$

## 方程组
```Latex
\begin{cases}
3x + 5y + z \\
7x - 2y + 4z \\ 
-6x + 3y + 2z 
\end{cases}
```
$$
\begin{cases}
3x + 5y + z \\
7x - 2y + 4z \\ 
-6x + 3y + 2z 
\end{cases}
$$
```LaTex
\left\{\begin{aligned}
3x + 5y + z \\
7x - 2y + 4z \\
-6x + 3y + 2z 
\end{aligned}\right.
```
$$
\left\{\begin{aligned}
3x + 5y + z \\
7x - 2y + 4z \\
-6x + 3y + 2z 
\end{aligned}\right.
$$
## 数组与表格
通常，一个格式化后的表格比单纯的文字或排版后的文字更具有可读性。数组和表格均以 `\begin{array}` 开头，并在其后定义列数及每一列的文本对齐属性，`c` `l` `r` 分别代表居中、左对齐及右对齐。若需要插入垂直分割线，在定义式中插入 `|` ，若要插入水平分割线，在下一行输入前插入 `\hline` 。与矩阵相似，每行元素间均须要插入 `&` ，每行元素以 `\\` 结尾，最后以 `\end{array}` 结束数组。

```Latex
\begin{array}{c|lc|r} 
n & \text{左对齐} & \text{居中对齐} & \text{右对齐}\\ 
\hline 
1 & 0.24 & 1 & 125 \\
2 & -1 & 189 & -8 \\
3 & -20 & 2000 & 1+10i 
\end{array}
```
$$
\begin{array}{c|lc|r}
n & \text{左对齐} & \text{居中对齐} & \text{右对齐}\\ 
\hline 
1 & 0.24 & 1 & 125 \\
2 & -1 & 189 & -8 \\
3 & -20 & 2000 & 1+10i 
\end{array}
$$

```Latex
\begin{array}{ccc} 
a & b & S \\
\hline 
0&0&1\\ 
0&1&1\\ 
1&0&1\\ 
1&1&0\\ 
\end{array}
```
$$
\begin{array}{ccc} 
a & b & S \\
\hline 
0&0&1\\ 
0&1&1\\ 
1&0&1\\ 
1&1&0\\ 
\end{array}
$$

# 函数
## 标准函数
### 指数
```LaTex
\exp_a b = a^b, \exp b = e^b, 10^m
```
$\exp_a b = a^b, \exp b = e^b, 10^m$

### 对数
```LaTex
\ln c, \lg d = \log e, \log_{10} f
```
$ln c, \lg d = \log e, \log_{10} f$

### 三角函数
```LaTex
\sin a, \cos b, \tan c, \cot d, \sec e, \csc f
```
$\sin a, \cos b, \tan c, \cot d, \sec e, \csc f$

```LaTex
\arcsin (a), \arccos b, \arctan c
```
$arcsin  (a), \arccos b, \arctan c`$





# 颜色

使用 `\color{颜色}{文字}` 来更改特定的文字颜色。  
更改文字颜色 **需要浏览器支持** ，如果浏览器不知道你所需的颜色，那么文字将被渲染为黑色。

- 对于较旧的浏览器（HTML4与CSS2），以下颜色是被支持的：
`\color{red}{text}`
$\color{red}{text}$
![LaTex公式手册-1744362756274.png](/img/user/attachment_manager/LaTex%E5%85%AC%E5%BC%8F%E6%89%8B%E5%86%8C-1744362756274.png)

- 对于较新的浏览器（HTML5与CSS3），额外的124种颜色将被支持：输入 `\color {#rgb} {text}` 来自定义更多的颜色，其中 `#rgb` 的 `r` `g` `b` 可输入 `0-9` 和 `a-f` 来表示红色、绿色和蓝色的纯度（饱和度）。
`\color{#0FF}{text}`
$\color{#0FF}{text}$
`\color{#5FA}{text}`
$\color{#5FA}{text}$
$$
\begin{array}{|rrrrrrrr|} \hline \verb+#000+ & \color{#000}{text} & \verb+#005+ & \color{#005}{text} & \verb+#00A+ & \color{#00A}{text} & \verb+#00F+ & \color{#00F}{text} \\ \verb+#500+ & \color{#500}{text} & \verb+#505+ & \color{#505}{text} & \verb+#50A+ & \color{#50A}{text} & \verb+#50F+ & \color{#50F}{text} \\ \verb+#A00+ & \color{#A00}{text} & \verb+#A05+ & \color{#A05}{text} & \verb+#A0A+ & \color{#A0A}{text} & \verb+#A0F+ & \color{#A0F}{text} \\ \verb+#F00+ & \color{#F00}{text} & \verb+#F05+ & \color{#F05}{text} & \verb+#F0A+ & \color{#F0A}{text} & \verb+#F0F+ & \color{#F0F}{text} \\ \hline \verb+#080+ & \color{#080}{text} & \verb+#085+ & \color{#085}{text} & \verb+#08A+ & \color{#08A}{text} & \verb+#08F+ & \color{#08F}{text} \\ \verb+#580+ & \color{#580}{text} & \verb+#585+ & \color{#585}{text} & \verb+#58A+ & \color{#58A}{text} & \verb+#58F+ & \color{#58F}{text} \\ \verb+#A80+ & \color{#A80}{text} & \verb+#A85+ & \color{#A85}{text} & \verb+#A8A+ & \color{#A8A}{text} & \verb+#A8F+ & \color{#A8F}{text} \\ \verb+#F80+ & \color{#F80}{text} & \verb+#F85+ & \color{#F85}{text} & \verb+#F8A+ & \color{#F8A}{text} & \verb+#F8F+ & \color{#F8F}{text} \\ \hline \verb+#0F0+ & \color{#0F0}{text} & \verb+#0F5+ & \color{#0F5}{text} & \verb+#0FA+ & \color{#0FA}{text} & \verb+#0FF+ & \color{#0FF}{text} \\ \verb+#5F0+ & \color{#5F0}{text} & \verb+#5F5+ & \color{#5F5}{text} & \verb+#5FA+ & \color{#5FA}{text} & \verb+#5FF+ & \color{#5FF}{text} \\ \verb+#AF0+ & \color{#AF0}{text} & \verb+#AF5+ & \color{#AF5}{text} & \verb+#AFA+ & \color{#AFA}{text} & \verb+#AFF+ & \color{#AFF}{text} \\ \verb+#FF0+ & \color{#FF0}{text} & \verb+#FF5+ & \color{#FF5}{text} & \verb+#FFA+ & \color{#FFA}{text} & \verb+#FFF+ & \color{#FFF}{text} \\ \hline \end{array}
$$
