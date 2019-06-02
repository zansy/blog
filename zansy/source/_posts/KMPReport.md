title: KMP 字符串模式匹配算法小解
author: zansy
tags:
  - 小结
categories:
  - 解题观察日记
date: 2018-08-01 12:03:00
---
Knuth-Morris-Pratt字符串查找算法（简称为KMP算法）可在一个主文本字符串 S 内查找一个词 W 的出现位置。
<!--more-->
### 问题
在主字符串中找到模式串的定位，如在主串`a c a b a a b a a b c a c a a b c`中找到模式串`a b a a b c`的定位。简单暴力的方法就是从拿模式串的第一位 `a` 开始和主串的每一位试，试中了就拿模式串第二位第三位接着试，直到全部吻合为止。现在可以给出其简便代码：
```
int index(String Source, String Pattern, int Position){
//返回子串Pattern 在主串Source 中第Position个字符之后的位置，若不存在，则函数值为0
	int Source_index=Position; int Pattern_index=1;
	while(Source_index <= StrLength(Source) && Pattern_index <= StrLength(Pattern)){
		if(Source[Source_index] == Pattern(Pattern_index)){
			++Source_index; ++Pattern_index;
		}else{
			Source_index = Source_index-Pattern_index+2;
			Pattern_index = 1;
		}
	}
	if(Pattern_index > StrLength(Pattern)) return Source_index-StrLenth(Pattern);
	else return 0;
}
```

### 改进
目前存在的问题是一位一位比较效率过低，这里介绍 KMP 算法，此算法可以在 `O(n+m)`的时间数量级上完成串的模式匹配操作。其改进在于：`每当一趟匹配过程中出现字符比较不等时，不需回溯指针，而是利用已经得到的部分匹配的结果将模式向右滑动尽可能远的一段距离后，继续进行比较。`

以问题的例子来说，在第三趟匹配中：
![第三趟和第四趟匹配](/images/pasted-11.png)

模式串第6位在比较主串第8位的时候出现了不匹配。事实上是可以跳过模式串第1位继续和主串第4位的比较，而直接将模式串右移，进行模式串第3位与主串第8位的比较。

那么在比较到模式串不匹配位置的时候，进行右移多少合适呢？我们可以明显了解到，在一个模式串中，每一位出现不匹配右移的位数都不相同，这里我们可以想到用一个数组来表示存储每一位不匹配时右移的位数，其索引就是不匹配的位置。

在KMP算法中，最核心的部分就是进行右移数组的计算。而`这个数组的计算和主串无关，只与模式串有关`，因为它的目的是为了减少比较次数，即需要找到模式串中与不匹配位置相同的其他位置。这句话可能因为我表述不足会引起误解，我举例：在上图第三趟匹配中，模式串第6位c不匹配，我们需要倒回去看第5位，第5位是b，纵览前面的部分，可以看到模式串第2位也是b，那么我们其实可以将其右移5-2=3位，因为前2位都已经比较过了，那么我们直接比较模式串的第3位，拿它和主串之前和第6位c不匹配的地方继续进行比较。这样可以得到右移数组第6位=3，即在模式串第6位（这里是c）出现不匹配的时候需要进行右移3位的操作。

我这里有一种简便方式计算next数组的方法，或许会对KMP算法有一个更好的了解：
![简便计算右移数组步骤](/images/pasted-12.png)
它的步骤其实就是我上述的思想。在每一位中进行思考——前面部分中有没有和自己部分相同的地方，即前后缀是否相同。相同的话可以在这一部分匹配失败的时候，直接从前一部分拿来继续匹配，简化了一位一位比较的操作。

这里给出改进后的KMP算法和右移数组的计算：
```
int index_kmp(String Source, String Pattern, int Position){
	int Source_index = Position; int Pattern_index = 1;
	while(Source_index <= StrLength(Source) && Pattern_index <= StrLength(Pattern)){
		if(Pattern_index == 0 || Source[Source_index] == Pattern[Pattern_index]){
			++Source_index; ++Pattern_index;
		}
		else Pattern_index = next[Pattern_index];//改动处
	}
	if(Pattern_index > StrLength(Pattern)) return Source_index-StrLenth(Pattern);
	else return 0;
}

void get_next(String Pattern, int next[]){
	int index=1; next[1]=0; int flag=0;
	while(index < StrLength(Pattern)){
		if(flag == 0 || Pattern[index] == Pattern[flag]){
			++index; ++flag; next[index]=flag;
		}else flag=next[flag];
	}
}
```