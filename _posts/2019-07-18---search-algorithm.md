---
layout: post
#标题配置
title: 常用的查找算法详讲
#时间配置
date:   2019-07-18 12:20:00 +0800
#大类配置
categories: 数据结构和算法


---

* content
{:toc}
---
---

# data structure and algorithm
我希望每个人对算法和数据结构是什么有一个深刻的认识，所以我们会在每一篇的文章的开始给你灌输下面的这些知识点：<br>
1、数据结构是一门研究组织数据方式的学科，有了编程语言也就有了数据结构，学好数据结构可以让你写出更漂亮更高效的代码（时间复杂度和空间复杂度）；<br>
2、算法其实就是解决我们生活中遇到的问题的计算方法；<br>
3、程序=数据结构+算法；要学好数据结构和算法就要在日常生活中把遇到的问题尝试用程序去解决<br>
4、数据结构是实现一个算法的基础，所以想要学好算法，需要把数据结构学到位；<br>
5、数据结构分为线性结构和非线性结构；线性结构的特点是数据元素之间存在一对一的线性关系；<br>
6、线性结构又有顺序存储结构和链式存储结构两种的存储结构，顺序存储结构中存储元素是连续的，而链式存储结构中存储元素不一定是连续的；

## 查找算法
我们常用的查找算法有以下四种：顺序查找、二分查找、插值查找和斐波那契查找；

## 顺序查找
基本思想：从第一个元素m开始逐个与需要查找的元素x进行比较，当比较到元素值相同(即m=x)时返回元素m的下标，如果比较到最后都没有找到，则返回-1。<br><br>
代码实现：<br>
```
public static int seqsearch(int[] ins,int val){
        for(int i=0;i<ins.length;i++){
            if(ins[i]==val){
                return i;
            }
        }
        return -1;
    }
```

## 二分查找
基本思想：将n个元素分成个数大致相同的两半，取a[n/2]与欲查找的x作比较，如果x=a[n/2]则找到x，算法终止。如 果x<a[n/2]我们只要在数组a的左半部继续搜索x（这里假设数组元素呈升序排列）。如果x>a[n/2]，则我们只要在数组a的右 半部继续搜索x。<br><br>
二分查找有递归和非递归两种实现方式；
<br><br>
递归方式代码实现：<br>
```
 public static int binsearch(int[] ins,int left,int right,int val) {
        if (left > right) {
            return -1;
        }
        int mid = (left + right) / 2;
        if (ins[mid] < val) {
           return binsearch(ins, mid + 1, right, val);
        } else if (ins[mid] > val) {
           return binsearch(ins, left, mid - 1, val);
        } else {
            return mid;
        }
    }
```
非递归方式代码实现：<br>
```
public static int binarySearch(int[] ins,int val){
                    int left=0;
                    int right=ins.length-1;
                    int mid;
                    while(left<right){
                        mid=(left+right)/2;
                        if(ins[mid]>val){
                            right=mid-1;
                        }else if(ins[mid]<val){
                            left=mid+1;
                        }else{
                            return mid;
                        }
                    }
                    return -1;
    }
```
当我们遇到当前数组有不止一个我们想寻找的数值大小想等的元素，我们就需要通过一个数组先保存符号条件的元素，等遍历完全部的元素之后再返回该数组，具体实现代码如下：<br>
```
public static ArrayList binasearch(int[] ins,int left,int right,int val){
        if(left>right){
            return new ArrayList();
        }
        int mid=(left+right)/2;
        if(ins[mid]>val){
            return binasearch(ins,left,mid-1,val);
        }else if(ins[mid]<val){
            return binasearch(ins,mid+1,right,val);
        }else{
            ArrayList list=new ArrayList<Integer>();
            int temp=0;
            while(temp<=mid-1){
                if(ins[temp]==val){
                    list.add(temp);
                }
                temp++;
            }
            list.add(mid);
            temp=mid+1;
            while(temp<=right){
                if(ins[temp]==val){
                    list.add(temp);
                }
                temp++;
            }
            return list;
        }
    }
```

## 插值查找
基本思想：插值查找算法是基于二分查找算法的,只是在查找的过程中计算Middle的方式有所改变,比如一组可以通过二分查找算法计算出的nMid索引的值,是这样子计算的nMid=(nLow+nHigh)/2,但是插值查找算法是根据比率算出的,nMid=nLow +(  (Key - nArr[nLow] ) / ( nArr[nHigh]-nArr[nLow] ) *(nHigh - nLow) ),其中Key是关键点,因为这个Key取出来是针对于这组数组的比率,取得好可以达到黄金比例,0.618, (Key - nArr[nLow] ) / ( nArr[nHigh]-nArr[nLow] )  表示是计算出比例,然后最后* (nHigh - nLow)得出nMid的索引。<br><br>
关键字分布比较均匀的查找，插值算法的平均性能比折半查找要好很多。反之，线性表中记录分布不均匀，用插值查找未必是很好的选择。<br><br>
代码实现：<br>
```
public static int insertSearch(int[] ins,int left,int right,int val){
        if(left>right || val<ins[left] || val>ins[right]){
            return -1;
        }
        int mid=left+(val-ins[left])/(ins[right]-ins[left])*(right-left);
        if(ins[mid]>val){
            return insertSearch(ins,left,mid-1,val);
        }else if(ins[mid]<val){
            return insertSearch(ins,mid+1,right,val);
        }else {
            return mid;
        }
    }
```

## 斐波那契查找
基本思想： 斐波那契查找就是在二分查找的基础上根据斐波那契数列进行分割的。在斐波那契数列找一个等于略大于查找表中元素个数的数F[n]，将原查找表扩展为长度为F[n](如果要补充元素，则补充重复最后一个元素，直到满足F[n]个元素)，完成后进行斐波那契分割，即F[n]个元素分割为前半部分F[n-1]个元素，后半部分F[n-2]个元素，找出要查找的元素在那一部分并递归，直到找到。<br><br>
### 斐波那契数列和黄金分割法
黄金分割点是指把一条线段分割为两部分，使其中一部分与全长之比等于另一部分与这部分之比。取其前三位数字的近似值是0.618；<br><br>
斐波那契数列{1,1,2,3,5,8,13,21}发现斐波那契数列的两个相邻数的比例无线接近黄金分割值；
### 斐波那契原理：
斐波那契查找原理获取中间节点的位置的方式是通过mid=low+F(n-1)-1，改点位置位于黄金分割点附近；<br><br>
这个mid=low+F(n-1)-1的计算由来：<br>
1、由斐波那契数列F(n)=F(n-1)+F(n-2)的性质可以得到F(n)-1=F(n-1)-1+F(n-2)-1+1。这个等式说明只要顺序表的长度为F(n)-1，则可以将该表分成长度为F(n-1)-1和F(n-2)-1的两段，再结合上面的等式，上面等式的1就是中间位置，所以中间位置为mid=low+F(n-1)-1。
2、类似的，每一子段也可以用相同的方式分割；
3、但顺序表长度n不一定刚好等于F(n)-1，所以需要将原来的顺序表长度n增加至F(n)-1。这里的n值只要能使得F(n)-1恰好大于等于n即可。

代码实现：<br>
```
/*
实现步骤：
1、获取一个斐波那契数列；
2、比较数组的长度n是否大于F(n)-1，是的话就让n自增，知道n<=F(n)-1；
3、按F(n)-1对原来的数组进行扩容，并将扩容的位置填进原数组最后一位元素；
4、接下来和二分查找类似，计算中值点，如果val<中值点的值，则向左边的数组进行上述的查找，反之则向右，如果刚好找到了，则返回mid和right中的最小值；因为在这个过程中如果一直向右的话，mid值可能会大于right值，这里的right值是原数组的最后一位元素，而right右边的元素都和right的元素是相等的；
*/
 public static int[] fib(){
        int[] ins=new int[maxsize];
        ins[0]=1;
        ins[1]=1;
        for(int i=2;i<maxsize;i++){
            ins[i]=ins[i-1]+ins[i-2];
        }
        return ins;
    }
    public static int fibsearch(int[] ins,int val){
        int low=0;
        int high=ins.length-1;
        int k=0;
        int mid=0;
        int[] fib=fib();

        while(high>fib[k]-1){
            k++;
        }

        int[] temp= Arrays.copyOf(ins,fib[k]);

        for(int i=high+1;i<temp.length;i++){
            temp[i]=ins[high];
        }

        while(low<=high){
            mid=low+fib[k]-1;
            if(temp[mid]>val){
                high=mid-1;
                k--;
            }else if(temp[mid]<val){
                low=mid+1;
                k-=2;
            }else{
                if(mid<high){
                    return mid;
                }else{
                    return high;
                }
            }
        }
        return -1;


    }
```




<br>

<br>

<center>有Marin的地方就有你的收获</center>