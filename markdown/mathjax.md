#mathjax
[toc]

 MathJax是一款运行在浏览器中的开源的数学符号渲染引擎，使用MathJax可以方便的在浏览器中显示数学公式，不需要使用图片。目前，MathJax可以解析Latex、MathML和ASCIIMathML的标记语言。 
## 基础
### 公式标记与查看公式
MathJax中的公式排版有两种方式，inline和displayed。inline表示公式嵌入到文本段中，displayed表示公式独自成为一个段落。
>**可以在渲染完成的公式上方右键点击，唤出右键菜单。在菜单中提供了查看公式代码、设置显示效果和渲染模式的选项。**

### 希腊字母
名称| 大写 |tex|小写|tex
:---|:--|:--:|:--:|:--:
alpha|$A$|A|$\alpha$|`\alpha`
beta|$B$|B|$\beta$|`\beta`
gamma|$\Gamma$|`\Gamma`|$\gamma$|`\gamma`
dekta|$\Delta$|`\Delta`|$\delta$|`\delta`

...
### 上标与下标
上标和下标分别使用`^`与`_`，
>**默认情况下，上下标符号仅仅对下一个组起作用。一个组即单个字符或者使用{..}包裹起来的内容**
`10^10`->$10^10$

### 括号

 1. 小括号与中括号可以直接写$(3*4)[4+5]$
 2. 大括号需要转义$\{\} 或者\lbrace \rbrace$
 3. 尖括号$\langle  s+b\rangle$
 4. 上取整数 $\lceil \rceil$
 5. 下取整 $. \lfloor \rfloor$
 >原始符号并不会随着公式大小缩放,可以使用`\left(…\right)`来自适应的调整括号大小。
 >$$\left \{        {s^3_1+1\over s^2_2-1} \right \}$$


### 求和和积分

 1. 求和 \sum
      $$\sum_{i=1}^n {i^2} =1   \tag{1.1}$$
2. 积分`\int`  $\qquad \int_0^\infty$
3.   $\prod$ $\bigcap \bigcup \iint$
### 分式和根式

分式有`\frac` 和`\over`两种写法
$\frac {a+1}{b-1}\qquad {a+1\over b-1}$
根式用`\sqrt`
$\sqrt[3] {s*d}$
### 字体
$普通$
$\Bbb{BDEbFb} $
$\mathbf {黑体 HEITI heiti}$
$\mathtt {打印机 dayinji DAYINJI}$
$\mathrm{罗马体 ROME rome}$
$\mathscr{手写体   shouxieti SHOUXIETI}$
$\mathsrc {手写体 shouxieti SHOUXIETI}$
### 特殊函数与符号
1. $sin (x) cos(x) tan(x) lim_{0\to\infty}$
2. 比较运算符$\lt \gt \neq \le \ge \not \gt \not\le$
3. 四则运算$\times \div \pm \mp \cdot$
4. 集合$\cap \cup \in \not\in \subset \subseteq \not\subseteq \supset \supseteq \not\supset \emptyset \varnothing$
5. 排列组合${n+1 \choose n-2} \binom{n+1}{n-2}$
6. 箭头$\to \rightarrow \leftarrow \Rightarrow \Leftarrow \mapsto$
7. 逻辑运算符 $\land \lor \lnot \exists \forall \top \bot \vdash \Vdash$
8. $\star \ast \oplus \circ \bullet$
9. $\approx \sim \cong \equiv  \prec$
10. $\infty \aleph  \nabla \partial$
11. $a \equiv  b \pmod c$
12. $\cdots \dots \qquad \ldots$
[画符号参考](http://detexify.kirelabs.org/classify.html)
### 空间
可以通过在ab间加入`\,`增加些许间隙，`\;`增加较宽的间隙，`\quad` 与 `\qquad` 会增加更大的间隙
### 顶部符号
$\hat {ss} \qquad \overline{s+b} \vec{a} \dot a\ddot b$
### 结束
转义字符$\$$

##表格
$$
\begin{array}{c|lrc}
n&\text{LEFT}&\text{Right}&\text{Center}\\
\hline
1&0.24&3&12dd5\\
2&22&23&42
\end{array}
$$
可以更复杂，内联
$$% outer vertical array of arrays
\begin{array}{c}
% inner horizontal array of arrays
\begin{array}{cc}
% inner array of minimum values
\begin{array}{c|cccc}
\text{min} & 0 & 1 & 2 & 3\\
\hline
0 & 0 & 0 & 0 & 0\\
1 & 0 & 1 & 1 & 1\\
2 & 0 & 1 & 2 & 2\\
3 & 0 & 1 & 2 & 3
\end{array}
&
% inner array of maximum values
\begin{array}{c|cccc}
\text{max}&0&1&2&3\\
\hline
0 & 0 & 1 & 2 & 3\\
1 & 1 & 1 & 2 & 3\\
2 & 2 & 2 & 2 & 3\\
3 & 3 & 3 & 3 & 3
\end{array}
\end{array}
\\
% inner array of delta values
\begin{array}{c|cccc}
\Delta&0&1&2&3\\
\hline
0 & 0 & 1 & 2 & 3\\
1 & 1 & 0 & 1 & 2\\
2 & 2 & 1 & 0 & 1\\
3 & 3 & 2 & 1 & 0
\end{array}
\end{array}$$
## 矩阵
$$\begin{bmatrix}
1&2&3\\
2&3&4\\
3&4&5
\end{bmatrix}
$$
`\bmatrix \Bmatrix \vmatrix \Vmatrix \pmatrix`
### 省略元素
$$\cdots \qquad  \ddots \qquad \vdots$$
### 增广矩阵
要利用array
$$
\begin{array}{cc|c}
1&2&3\\
2&2&3\\
\end{array}
$$
## 公式对齐
这需要使用形如`\begin{align}…\end{align}`的格式，其中需要使用&来指示需要对齐的位置。
$$
\begin{align}
\sqrt{4\over b+3}&=\sqrt{a\over b}\\
&=\sqrt{c}
\end{align}
$$
## 分类表达式

 

$$
fn=
\begin{cases}
n ,&\text{if $n$ is evn}\\
3N+1,&\text{if $n$ is odd}\\
\end{cases}
$$
## 数学符号查询
[符号查询工具](http://detexify.kirelabs.org/classify.html)

## 空间问题
### 不要在再指数或者积分中使用 \frac

在指数或者积分表达式中使用\frac会使表达式看起来不清晰，因此在专业的数学排版中很少被使用。应该使用一个水平的/来代替，效果如下
$$\begin{array} {c|c}
\text{bad}&\text{better}\\
\hline
e^{\pi\over 2}&e^{\pi/2}
\end{array}$$
### 使用 \mid 代替 | 作为分隔符
符号|作为分隔符时有排版空间大小的问题，应该使用`\mid`代替。效果如下：
### 多重积分

对于多重积分，不要使用\int\int此类的表达，应该使用\iint \iiint等特殊形式。效果如下：
此外，在微分前应该使用\,来增加些许空间，否则TEX会将微分紧凑地排列在一起
## 连分数
书写连分数表达式时，请使用`\cfrac`代替`\frac`或者`\over`两者效果对比如下
$$
a+\cfrac{a}{1+\cfrac {a}{1+\cfrac ab}}
$$
## 方程组
使用`\begin{array} … \end{array`}与`\left{…\right.`配合，表示方程组
同时，还可以使用`\begin{cases}…\end{cases}`表达同样的方程组

$$\left\{
\begin{array}{c}
a_1x+b_1y=c_1 \\
a_2x+b_2y=c_2 \\
\end{array}
\right. $$

$$
\begin{cases}
\begin{align}
a_1x+b_1y+d&=0\\
a_2x+b_2y&=1
\end{align}
\end{cases}
$$
## 颜色

$\color{red}{red_text}$
## 公式标记和引用
使用`\tag{yourtag}`来标记公式，如果想在之后引用该公式，则还需要加上`\label{yourlabel}`在`\tag`之后，如：
$$a := x^2-y^3 \tag{*}\label{*}$$
为了引用公式，可以使用`\eqref{rlabel}`，如：
$$a+y^3 \stackrel{\eqref{*}}= x^2$$
可以看到，通过超链接可以跳转到被引用公式位置。

