# Tree树
## 基本概念
- 树本质上即是图
- 对于树的定义，使用了递归的方法，即用“树”本身定义自己（“树”->“树”->“树”->...）
- 在不是空集的情况下，树由根（root）的结点r和0个或多个非空子集（树）组成。
- 若有 $N$ 个结点，则有 $N-1$ 条边
- 同一“父亲”的子集之间均为兄弟（sibling）
- 没有子节点的结点成为“树叶”
- 从节点 $n_1$ 到 $n_k$ 的路径定义为节点为 $n_1, n_2, n_3, ...n_k$ 的序列，使得对于 $1<=i<k$ 的节点 $i$ 是 $i+1$ 的父亲，这条路径的长为路径上的条数 $k-1$
- 度数：在树中，每个节点当前所拥有的子节点（子树）的个数称为该节点的度（degree）
- 阶数：（Order）阶定义为一个节点的子节点数目的最大值。
- 树的高度：最顶层（根）的**高度为0**，之后类推


树节点的申明定义
采用兄弟（sibling）之间互连的方式，以缓解因子树根节点的子集过多而显得臃肿和难以维护带来的问题。 CODE:
```
class TreeNode{
    Object element;
    TreeNode firstChild;// 指针，指向其第一个子集，若无则为null
    TreeNode nextSibling;// 指针，指向它的下一个兄弟，若无则为null
}
```
## 遍历
遍历分为 **先序遍历** 和 **后序遍历**

- 先序遍历：节点在子节点被处理 前 被处理
- 后序遍历：节点在子节点被处理 后 被处理
## 二叉树 Binary Tree
此树的每个子节点不能有超过两个的子节点

最坏二叉树：每个节点只有一个子节点。

定义二叉树： CODE：
```
class BinaryTree{
     Object element;
     BinaryTree left;
     BinaryTree right;
}
```
Tips:

1.单目运算符：只有一个运算数，如i++ !i
2. 双目运算符：两个运算数，如a+b, a!=b
3. 三目运算符：三个运算数，如i=a?b:c

二叉树的应用：

1. 表达式：定义二叉树的叶子为运算数，其余的节点为运算符，使用中序遍历的方式读取整棵树。

## 查找树ADT-二叉查找树
- 定义：对于树中任意节点 $X$ ，其左子树的任一项均小于 $X$ ，其右子树的任意一项均大于 $X$ 。
- 普通二叉树在查找过程中需要按一定的顺序遍历左子树->右子树，机器时间耗费大。二叉查找树通过为树的基本单位“根 root-[左子树 left tree，右子树 right tree]”中的根及左右子树之间定义一关系，通过关系确定是遍历左子树亦或右子树，节省时间。
- 查找二叉树的基本操作(contains, add, remove)的操作所花费的时间为 $O(logN)$
### 删除remove
当需要删除某个节点时，若删除后会出现树的结构发生改变，这会使删除变得复杂 最好的方法就是不更改结构/最小更改结构（即改变整棵树的“叶子”）的情况下进行删除 （只要保证内部存储的数据不变，数据之间的关系/结构不变即可）

- 对于树而言，删除是非常复杂的。
- 对于二叉查找树而言，删除指定的节点（非叶子）即替换指定节点的element——需要符合二叉查找树的定义（大于左子树任一项，小于右子树任一项）。
- 一般使用右子树最小的一项（即右子树的最左节点），p.s. 左子树的最大一项也ok（即左子树最右的一项）。
### 懒惰删除
在删除次数较少的情况下，仍保留被删除的节点，并标记上删除标记。在处理有重复项时用处大，因为此时记录出现频率数的域可以减小1(?)。

### Other详见CODE
## AVL平衡二叉查找树
- 平衡二叉查找树需要保证树的深度是 $O(logN)$。
- 一棵AVL树的每个节点，要求其左子树和右子树之间的高度差最多为1，当树为空时规定树的高度为-1。

### 单旋转和双旋转
在出现不平衡时，往往由以下4点所产生，设不平衡点为 $a$

1. 对 $a$ 的左儿子的左子树进行一次插入
2. 对 $a$ 的左儿子的右子树进行一次插入
3. 对 $a$ 的右儿子的左子树进行一次插入
4. 对 $a$ 的右儿子的右子树进行一次插入

本质上"1, 4"，"2, 3"为相同情况，但编程中要分开处理

对于"1, 4"情况，使用**单旋转**; 对于"2, 3"情况，使用**双旋转**

单旋转和双旋转都为对在满足二叉查询树要求下对节点及其子树顺序进行调整以满足平衡条件。

**注意上述4点，均为对不平衡点 $a$ 的x儿子的y子树进行操作**

**1. 单旋转**
需要单旋转的节点为 $a$ 节点和其对应的儿子节点。 在出现1，4情况时使用单旋转

- 确定不平衡点——即左子树和右子树深度相差大于1（==2）的根节点。
- 将儿子与不平衡节点位置对调，且满足二叉查找树的基本要求

实现旋转的方法以待平衡节点为参数，即旋转使以一个待平衡节点为根节点的子树为单位进行旋转 旋转实质上是将对应节点左右指针所指向的对象更改（非多么复杂），以实现对应的方法

**2. 双旋转**
双旋转本质上为**两次单旋转**， 在出现2，3情况时使用双旋转，

- 需要将进行插入的子树的根节点显示出来（无显示时将子树表示为三角△；显示节点则节点为○，其子树表示方法同上）。详见Book 4.4.2 双旋转。

1.先从最底部的节点开始（进行插入操作的子树的根节点）与父节点（非平衡点的子节点）单旋转
2. 旋转后，底部节点上升，随后与非平衡点单旋转。

****

## 伸展树 Splay tree
Tips：不需要考虑平衡情况

### 1. 一个简单的做法（问题很大）
将底部的节点通过不断的单旋转从而提升高度，达到顶端，方便读取。 但问题在于高高度的节点（如根节点）经过调整后变深（甚至达到底部），读取困难。 按照顺序访问全部节点需要耗费 $\sum_{i=1}^{n-1}i=$ $\Omega(N^2)$

### 2. 展开 Splaying
展开的状况分为“之字形”( "zig-zag" )和“一字型”( "zig-zig" ) Tips：处理时以3个节点为单位。

- “之字形”( "zig-zag" )仍为双旋转
- “一字型”( "zig-zig" )则为对称，节点顺序调转（非单旋转）
- 详见Book 4.5.2
- 本质上也是将目标节点提升高度，向上浮，但此方法对于量大的树（很差的树），高度上会减小且读取速度加快

使用“展开”后，整体树的深度相较“简单的做法”变浅了。
***

## B树
B树，可理解为每个节点包含了一个表，非叶子节点的作用同二叉树相似
实现B树要求：
1. 明确B树的阶数（各节点的子节点树最大值）
2. 数据均存储在树叶上
3. 在非子叶节点上存储M-1个关键字（功能类似于二叉树的非叶子节点）；关于关键字，关键字i（i指关键子在节点中的位置）为子树I+1（I指子树在父节点所有子树中的位置）中最小的关键字（此关键字即子树所有的叶子中最小的值）
4. 除了根，所有非叶子节点的子树数在$[M/x2]$和$M$之间（奇数时，值取半使用四舍五入，如5/2为3，下同）
5. 相同深度的叶子其存储的数据项为$[L/2]$到$L$之间（此值需保证B树不会退化为二叉树）
6. B树所放的值从左向右增加，即最小值在最左子树中，最大时在最右子树中。

如图所示
![title](../.local/static/2019/4/4/1557373274371.1557373274519.png)

## 满二叉树，完全二叉树，理想二叉树
- 满二叉树：数的所有节点要不为叶子，要不只有两个子树，没有其他情况的为满二叉树
- 完全二叉树：
	- 对于一棵高为$h$的树，除了$h$层，其余层皆满（各个节点的子节点数为2）且$h$层的节点**连续**集中于左边
	- 一棵高为$h$的平衡二叉树有：$2^h$（当高度为0时）**~**$2^{h+1}-1$（高度大于0时）个节点，**注意**，此处的节点数为**范围/界**，而不是单一的值
	- 非叶子节点全满，且叶子节点均在同一高度上。
- 理想二叉树：
	- 高为$h$时，树的节点数为$2^{h+1}-1$
	- 其所有节点的**高度**合为：$2^{h+1}-1-(h+1)$

e.g.
```
             a
          /     \
         b       c
       /   \   /   \
      d     e f     g
     / \   /
    h   i j
```



