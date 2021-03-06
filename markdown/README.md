# 阅读笔记

[TOC]

## 第一章

第一章用一个对磁盘文件进行归并排序的问题进行切入，并且严格限制了内存使用的大小和程序运行的时间。

如果默认我们需要对一个32位的整数进行排序，那么多路归并排序就成为了唯一的选择。

事实上，如果对问题的实际应用场景进行分析，我们也许会得到一些能够简化问题的信息，在这一章中的一个好消息是每个数字都小于一千万，而且不会重复，这样首先想到可以用更少的位去表示这些数字。但是即便用7个字节存储每一个数，仍然不能将这些数据一次性在内存中装下，但是按位存给了我们一个提示，就是用位去表征一个数。那么久自然想到用一个位表示一个数。将可用的1MB的内存转化为一个有800多万个可用位的位向量，从左往右分别代表0,1,2...，但是800万个可用位显然存不下1000万个数，所以看上去一趟算法并不能实现将1000万个数完全排序。

因此我们需要想一想1000万个数据如何高效的存储，根据信息论的知识，如果单纯的把一个数用1bit来存，1MB的空间显然是不够的，这种情况就在问题5中进行了思考。

最后在“简单的设计”里面有一句话说的很好“设计者确定其设计已经达到了完美的标准不是不能在增加任何东西，而是不能再减少任何东西...简单的程序通常比具有相同的功能的复杂程序更可靠、更健壮、更高效。而且易于实现和维护”。想起《吕氏春秋》的“一字千金”，都是将要表达的信息用尽可能少的可见载体呈现。

## 第二章

第二章开篇提出了三个问题

* 给定一个最多包含40亿个随机排列的21位整数，找出一个不在文件中的32位整数
  * 在足够的内存情况下，如何解决该问题
  * 内存很少，但是有几个外部的“临时”文件可用，如何解决该问题
* 将一个n元向量向左旋转i个位置。例如，当n=8，i=3时，向量abcdefgh旋转为defghabc。如何使用数十个额外字节的存储空间，在正比于n的时间完成向量的旋转
* 给定一个英语词典，找出其中的所有变位词的结合。例如"pots","stops","tops"互为变位词

### 问题1

(1) 在内存足够的情况下，可以使用位向量的方法表征每一个出现的数，至少需要512Mb的内存空间存储时间复杂度是O(n)

(2) 内存不足的情况

内存不足的情况下，对于输入的数据，先根据最高位按照0和1进行划分，写到两个文件中，文件名是“0”和“1”，然后看这两个文件中的数是否是2^32个，如果有一个不满，说明有缺的数，那么把这个文件的数作为输入进行"0/1"探测，一直这样进行下去，知道发现“0/1”探测的结果中有一个文件为空，说明当前位没有数据，这样就能找到不存在的数

答案中说算法复杂度在O(nlgn)，但我觉得因为每次是将空间折半

如果是要找到0和1文件中多个不存在的数，那么对0和1的文件都进行探测，用主定理法
> T(n) = 2T(n/2) + n

可以得到复杂度确实是O(nlgn)

但是如果只是要找到一个不存在的数，只需要对一个文件(数量少的)进行探测
> T(n) = T(n/2) + n

用主定理法可以得到T(n) = O(n)，更加确切的$T(n) = n + \frac{n}{2} + \frac{n}{4} + ... + 1​$ = $n (1 + \frac{1}{2} + \frac{1}{4} + ... + (\frac{1}{2})^{lgn})​$ = n $\frac{1-(\frac{1}{2})^{lgn+1}}{1-\frac{1}{2}}​$ = $n(2-\frac{1}{n}) = 2n-1​$

<center>![](../img/charpter2-Q1.png)</center>

### 问题2

这个问题关键要找到技巧，先分析一下问题，相当于有两个相邻的块，现在要交换它们的位置。一个最简单的方法是开辟一个新的空间，将其中的一块暂存起来，之后把第二块往前移，最后把暂存起来的第一块放到相应的位置上去。

如果块很大而可用的空间很少，就需要一些精巧的方法，一种巧妙的实现是通过三次翻转完成的

```Java
public static void swap(char[] str, int from, int to){
  for(int i = from,j = to; i < j; i++,j--){
    char tmp = str[i];
    str[i] = str[j];
    str[j] = tmp;
  }
}

public static void main(String[] args){
  char[] str = {'a','b','c','d','e','f','g','h'};
  swap(str, 0, 2);
  swap(str, 3, 7);
  swap(str, 0, 7);
}
```

### 问题3

问题三感觉可以将字典中的每一个单词按照字典序排序，然后相同的变位词得到的排序结果一样

### 小结

不是随便什么人都会灵机一动的，我记得[《暗时间》](http://mindhacks.cn/)一书中曾经提到“关于学习密度和专注力”的问题，持续保持思考状态很重要，无形中就可以多很多可以利用的时间。这应该才是灵机一动的源泉。

在回到这一章中，这一章着重要掌握的还是二分查找的思想，二分查找的具体实现在第九章中仔细研究。二分为什么应用的如此广泛呢，不光是在计算机科学，在现实生活中，一个十进制的世界里面，人们也会将问题二分化，一般演变为两方的势力对决，但是三极、甚至更多(战国、三国时期)的持续时间很短暂，而且只要有一极解体，原有的格局便不复存在了。“2”这个数字为什么这么特殊，这个问题我一直没有想明白。是因为我们习惯用“二分”的眼光去看待事物的吗？

## 第三章

说了半天的反面教材，概括起来就是能写短的程序就不要写长的程序。

现在许多语言都提倡函数式编程，可以说将这方面的思想发挥到了极致，然而程序写的越短，可读性就降低了，需要补很多注释，也是编程人员最头疼的地方，注释是写给别人看的不是自己看的。

## 第四章

```Java
public static int binarySearch(int target, int[] arr, int len){
  int lower = 0, upper = len - 1;
  int index = -1;
  while(true){
    if(lower > upper){
      index = -1;
      break;
    }
    int tmp = lower + ((upper - lower) >> 1);
    if(arr[tmp] == target){
      index = tmp;
      break;
    } else if (arr[tmp] > target) {
      upper = tmp - 1;
    } else {
      lower = tmp + 1;
    }
  }
  return index;
}

public static void main(String[] args) {
  int[] arr = {2,3,5,7,11,13,17,19,23,29};
  System.out.println(binarySearch(6, arr, 10));
  System.out.println(binarySearch(7, arr, 10));
  System.out.println(binarySearch(8, arr, 10));
  System.out.println(binarySearch(30, arr, 10));
  System.out.println(binarySearch(1, arr, 10));
  System.out.println(binarySearch(23, arr, 10));
}
```

二分搜索最重要的是下面这句话，原书中没有考虑两个大数想加造成溢出的情况，而且不用语言中>>操作的优先级也不同，保险起见需要赢括号包裹起来

> int tmp = lower + ((upper - lower) >> 1);

刚开始的时候没写测试，程序有死循环的问题。原因还是上面计算中间索引的问题。多写测试有助于提高代码的质量和对问题的理解

## 第五章

测试的确很重要。在上学的过程中，许多课堂作业的程序都是写完一次就扔掉的那种，自然用不到什么测试，也体会不到测试对于代码正确性的帮助。但是随着参与到实际的项目中去，测试的作用就体现出来了，无论是代码正确性的测试，还是重构之后的正确性，测试能起很大的作用。要好好写测试，多补充测试用例，覆盖边界条件。

## 第六章

### 实例研究

开篇举的例子很有趣，一开始我以为是n个物体在三维空间的运动，忽然想到不是说“三体”问题没有通解，怎么可以用计算机进行模拟，看到下面才发现是二位平面的模拟，这样就说的通了。但是文中提到的优化技术的细节，我一时还想不明白，对于“代码调优”和“硬件”层面的优化我能够理解。但是对于数据结构和算法的部分，还有些没搞明白。如果要计算物体之间的相互作用力，按照牛顿万有引力的方法，应该是C(n,2)也就是O(n^2)的复杂度，但是文中说用树形结构表示物体之间的关系，不太明白为什么是二叉的，如果说相互靠近的物体可以近似的看做是一个整体，那是不是还要聚类，而且每个时间步之后，物体的位置发生变化，行程新的整体，这个时候又该如何更新。这些问题恐怕要看论文才能知道了。不过不得不说，能够认识并模拟物体的运动轨迹，表明我们队所处的世界的认识有更加深入了，这是人类文明的进步。

### 设计层面

问题定义我觉得是在开始做一件事情之前最先要考虑的事情，如果问题定义出现了偏差，那么很有可能最后研究的结果不符合预期

系统结构简单的说就是模块化，高内聚低耦合。各个模块先评估过后再组装起来，看最后运行的瓶颈

算法和数据结构，这就考验基本功了

代码调优，实践过程中不断总结经验和教训，同时还要多向别人学习

### 原理

> 计算机系统中最廉价、最快速且最可靠的元件是根本不存在的

如果仅仅需要较小的加速，那么看各个模块的运行时间，对最有可能获得加速的模块进行改进。如果需要较大的加速，那么就需要全方位考虑问题，对不同方向进行深入研究，通常需要付出巨大的努力

## 第七章

这一章主要讲了估算的艺术，在实际生活中，估算常常用到。每天都会估计从一个地方到另一个地方去要花多少时间。但是估算的问题在于有些起决定性的因素由于自己对问题认识的不足，常常被忽略，造成和实际结果有大的偏差，还是需要在实践过程中不断的积累经验

> π秒就是一个纳世纪（一年有3.155x10^7秒，一纳秒=10^-9秒，一个纳世纪=10^-9*100 = 10^-7年，误差不超过千分之五）

### 安全系数

用安全系数来补偿自己对知识局限的不足。

### Little定律

> 系统中物体的平均数量等于物体离开系统的平均速率和每个物体在系统中停留的平均时间的乘积

### 原理

> 做任何事情都应该尽量简单，但是不能过于简单

之前的章节也有过类似的表述，简单的含义是不能缺少任何必要的组件。对于必要组件的定义，应该包含安全系数、一补偿估算参数时的错误和对问题了解的不足

## 第八章

本章就给定数组的子数组之和的最大值问题进行了分析，主要要思考的是分治算法和动态规划方法。

先分析分治算法，基本思想是将原来的问题变成若干个子问题。如果给定数组的起始范围是lower, upper，中间值是mid，那么最大的子数组之和应该在\[lower, mid\]或者\[mid+1, upper\]中去寻找，亦或者是\[p, q\]，其中(p <= mid <= q)

在计算\[p, q\]的时候可以采用贪心的方法，问题划分为两部分，\[p, mid\]和\[mid+1, q\]，分别求以mid结尾的最大子数组的和，和以mid+1开头的最大子数组的和

分治方法的T(n) = 2T(n/2) + O(n)，解为T(n) = O(nlgn)

```Java
// 书中原版
public static int max(int a,int b){
  return a > b ? a : b;
}

public static int binaryMaxSum(int arr[], int lower, int upper){
  if(lower > upper) return 0;
  if(lower == upper) return arr[lower];
  int mid = lower + ((upper - lower) >> 1);
  int lmax = 0, lsum = 0;
  for(int i = mid; i >= lower; i--){
    lsum += arr[i];
    lmax = max(lmax, lsum);
  }
  int rmax = 0, rsum = 0;
  for(int i = mid+1; i <= upper;i++){
    rsum += arr[i];
    rmax = max(rmax, rsum);
  }
  return max(lmax+rmax, max(binaryMaxSum(arr, lower, mid), binaryMaxSum(arr, mid+1, upper)));
}
```
这里有个小问题，当arr中的数全为负数的时候，会返回负数中的最大的那个，如果认为这是后应该范围没有，可以在最后加一个判断就可以了。

上面的方法不能返回最大子数组的起始范围，修改一下程序，使用一个三元组TriInteger代替

```Java
// 改进版，增加了范围
public static TriInteger max(TriInteger a, TriInteger b){
  return a.value > b.value ? a : b;
}

public static TriInteger binaryMaxSumWithIndex(int arr[], int lower, int upper){
  if(lower > upper) return new TriInteger(0, -1, -1);
  if(lower == upper) return new TriInteger(arr[lower], lower, upper);
  int mid = lower + ((upper - lower) >> 1);
  int lmax = arr[mid], lsum = arr[mid], lindex = mid;
  for(int i = mid-1; i >= lower; i--){
    lsum += arr[i];
    if(lsum > lmax){
      lmax = lsum;
      lindex = i;
    }
  }
  int rmax = arr[mid], rsum = arr[mid], rindex = mid;
  for(int i = mid+1; i <= upper;i++){
    rsum += arr[i];
    if(rsum > rmax){
      rmax = rsum;
      rindex = i;
    }
  }
  return max(new TriInteger(lmax+rmax-arr[mid], lindex, rindex), max(binaryMaxSumWithIndex(arr, lower, mid), binaryMaxSumWithIndex(arr, mid+1, upper)));
}

// TriInteger.java
public class TriInteger {
	public int value;
	public int lower;
	public int upper;

	public TriInteger(int value, int lower, int upper){
		this.value = value;
		this.lower = lower;
		this.upper = upper;
	}

	@Override
	public String toString(){
		return String.format("value %d, from %d to %d", value, lower, upper);
	}
}

public static void main(String[] args) {
  // TODO Auto-generated method stub
  int[] arr = {31,-41,59,26,-53,58,97,-93,-23,84};
	System.out.println(binaryMaxSum(arr, 0, 9));
  System.out.println(binaryMaxSumWithIndex(arr, 0, 9));
}
```

接下来看动态规划方法，书上讲的太简单，这里参考《编程之美》2.14节的讲解，

首先考虑arr\[0\]和arr\[i\]..arr\[\j]之间的关系
1. 0 = i = j，那么arr\[0\]就是最大的那一段
2. 0 = i < j，那么最大的那一段以arr\[0\]开头
3. 0 < i，最大的那一段和arr\[0\]无关

对于arr\[i+1]...arr\[n-1\]，如果已经知道最大的那一段是arr\[p\]...arr\[q\]，那么对于arr\[i\]而言，最大的一段有三种情况
1. arr\[i\]
2. 以arr\[i\]开头的一段
3. arr\[p\]...arr\[q\]

```Java
public static int dynamicMaxSum(int arr[], int len){
  int all = arr[len-1];
  int start = arr[len-1];
  for(int i = len-2;i >=0;i--){
    start = max(arr[i], arr[i]+start);
    all = max(all, start);
  }
  return all;
}
```

这里的时间复杂度为O(n)

如果需要指示位置，仍然用上面三元组的方式

```Java
public static TriInteger dynamicMaxSumWithIndex(int arr[], int len){
  TriInteger all = new TriInteger(arr[len-1], len-1, len-1);
  TriInteger start = new TriInteger(arr[len-1], len-1, len-1);
  for(int i = len-2 ; i >= 0;i--){
    if(arr[i] > arr[i]+start.value){
      start.value = arr[i];
      start.lower = i;
      start.upper = i;
    }else{
      start.value = arr[i]+start.value;
      start.lower = i;
    }

    if(start.value > all.value){
      all.value = start.value;
      all.upper = start.upper;
      all.lower = start.lower;
    }
  }
  return all;
}
```

动态规划技术写起来容易想起来难，关键是要将计算的中间状态保存起来，同时建立起各个子问题之间的联系

## 第九章

本章主要是针对CPU bound的情况进行了代码调优，但原则应当是“尽量减少调优”，尤其是在自己不确定的情况下，不能瞎调，给系统的稳定性带来危害

比较稳健的方法是先找到系统运行中某个最耗时的部分，针对这一部分进行突破。本章中针对n等于具体数据而进行的优化我个人不喜欢，因为扩展性不好，容易出错，代码也很难维护。

> 玩火者，小心自焚

## 第十章

本章主要从以下几个方面介绍了如何节省程序运行时所占用的空间

节省数据空间的技术
* 重新计算
* 稀疏结构
* 信息论
* 重新分配空间

节省代码空间的技术
* 函数定义
* 解释程序
* 翻译

最重要的原则：简单性

现在在寻找解决问题的方法的时候，往往需要考虑“折中”(trage-off)，没有完美的解决方案，尽管存在同时能降低存储空间和计算时间的方案，但这往往是基于原有的方法远非最优。当系统优化到一定的程度之后，就需要考虑针对特定的需求努力进行折中。

## 第十一章
