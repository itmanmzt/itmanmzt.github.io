---
layout: post
#标题配置
title: 常用的八种内部排序算法详讲
#时间配置
date:   2019-07-17 23:16:00 +0800
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

## 排序算法
排序算法是将一组数据，依指定的顺序进行排列的过程；<br>
排序分内部排序和外部排序两类，内部排序是指将需要处理的所有数据都加载到内部存储器（内存）中进行排序；外部排序是指由于数据量过大，无法全部加载到内存中，需要借助外部存储（文件等）进行排序；<br>
![](https://itmanmzt.github.io/styles/images/datastru/005.jpg){:align="center"}<br><br>

### 冒泡排序
基本思想：依次比较相邻的两个数，将小数放在前面，大数放在后面。即在第一趟：首先比较第1个和第2个数，将小数放前，大数放后。然后比较第2个数和第3个数，将小数放前，大数放后，如此继续，直至比较最后两个数，将小数放前，大数放后。重复第一趟步骤，直至全部排序完成。<br><br>
优化：因为排序的过程中，各元素不断接近自己的位置，<b>如果一趟比较下来没有进行过交换，就说明序列已经有序</b>,因此要在排序过程中设置一个flag判断元素是否进行过交换，从而减少不必要的比较；<br><br>

代码实现：<br>
```
public class MaoPao {
    public static void main(String[] args) {
        int[] ins=new int[80000];
        for(int i=0;i<80000;i++){
            ins[i]= (int) (Math.random()*80000);
        }
        long start=System.nanoTime();
        maopao(ins);
        long end=System.nanoTime();
        System.out.println(end-start);
    }
    public static void maopao(int[] ins){

        for(int i=0;i<ins.length-1;i++){
            boolean flag=false;
            for(int j=0;j<ins.length-1-i;j++){
                if(ins[j]>ins[j+1]){
                    int temp=ins[j];
                    ins[j]=ins[j+1];
                    ins[j+1]=temp;
                    flag=true;
                }
            }
            if(flag==false)
                break;
        }
    }
}
```

### 直接选择排序
基本思路：对于具有 n 个记录的无序表遍历 n-1 次，第 i 次从无序表中第 i 个记录开始，找出后序关键字中最小的记录，然后放置在第 i 的位置上。<br><br>
代码实现：<br>
```
public static void xuanze(int[] ins){
        for(int i=0;i<ins.length-1;i++){
            int index=i;
            int min=ins[i];
            for(int j=i+1;j<ins.length;j++){
                if(min>ins[j]){
                    min=ins[j];
                    index=j;
                }
            };
            if(index!=i){
                ins[index]=ins[i];
                ins[i]=min;
            }
        }
}

```

### 直接插入排序
基本思想：把n个待排序的元素看成一个有序表和一个无序表，开始时有序表中只有一个元素，无序表中有n-1个元素；排序过程即每次从无序表中取出第一个元素，将它插入到有序表中，使之成为新的有序表，重复n-1次完成整个排序过程。<br><br>
代码实现：<br>
```
    public static void charu(int[] ins){
        for(int i=1;i<ins.length;i++){
            int index=i-1;
            int val=ins[i];
            while(index>=0 &&  val<ins[index]){
                ins[index+1]=ins[index];
                index--;
            }
            ins[index+1]=val;
        }
    }

```

### 希尔排序
希尔排序是在直接插入排序的基础上提出的，因为直接插入排序在某些特殊情况下会非常低效，比如我们在很小的数初始位置在数组的后面的时候，需要进行多次比较进行多次移动才能实现有序；希尔排序的提出就是为了避免这种极端情况所带来的效率低下问题；<br><br>
基本思想：希尔排序在数组中采用跳跃式分组的策略，通过某个增量将数组元素划分为若干组，然后分组进行插入排序，随后逐步缩小增量，继续按组进行插入排序操作，直至增量为1。希尔排序通过这种策略使得整个数组在初始阶段达到从宏观上看基本有序，小的基本在前，大的基本在后。然后缩小增量，到增量为1时，其实多数情况下只需微调即可，不会涉及过多的数据移动。<br><br>
希尔排序在对有序序列插入时，可以采用交换法和移动法两种，下面我们将实现这两种方式并进行排序速度的测试，看看哪种算法更优或者说在哪些情况下使用哪种算法更优；<br><br>
交换法实现希尔排序：<br>
```
public static void xier1(int[] ins){
        int temp;
        for(int z=ins.length/2;z>0;z/=2){
            for(int i=z;i<ins.length;i++){
                for(int j=i-z;j>=0;j-=z){
                    if(ins[j]>ins[j+z]){
                        temp=ins[j];
                        ins[j]=ins[j+z];
                        ins[j+z]=temp;
                    }
                }
            }
        }
    }
```
移动法实现希尔排序：<br>
```
public static void xier2(int[] ins){
        for(int z=ins.length/2;z>0;z/=2) {
            for (int i = z; i < ins.length; i++) {
                int index=i;
                int val=ins[i];
                if(ins[index]<ins[index-z]){
                    while(index-z>=0 && ins[index]<ins[index-z]){
                        ins[index]=ins[index-z];
                        index-=z;
                    }
                    ins[index]=val;
                }
            }
        }
    }
```
通过速度测试我们知道，由于交换法需要进行多次的交换，所以它的效率相对移动法会更低<br>

### 快速排序
快速排序是对冒泡排序的一种改进。<br>
基本思想：通过一次排序将整个无序表分成相互独立的两部分，其中一部分中的数据都比另一部分中包含的数据的值小，然后继续沿用此方法分别对两部分进行同样的操作，直到每一个小部分不可再分，所得到的整个序列就成为了有序序列。<br><br>
快速排序是所有时间复杂度相同的排序方法中性能最好的排序算法。快速排序在序列中元素很少时，效率将比较低，不如插入排序，因此一般在拆分到序列中元素很少时使用插入排序，这样可以提高整体效率。<br><br>
代码实现：<br>
```
public static void quickSort(int[] arr,int left, int right) {
        int l = left; //左下标
        int r = right; //右下标
        //pivot 中轴值
        int pivot = arr[(left + right) / 2];
        int temp = 0; //临时变量，作为交换时使用
        //while循环的目的是让比pivot 值小放到左边
        //比pivot 值大放到右边
        while( l < r) {
            //在pivot的左边一直找,找到大于等于pivot值,才退出
            while( arr[l] < pivot) {
                l += 1;
            }
            //在pivot的右边一直找,找到小于等于pivot值,才退出
            while(arr[r] > pivot) {
                r -= 1;
            }
            //如果l >= r说明pivot 的左右两的值，已经按照左边全部是
            //小于等于pivot值，右边全部是大于等于pivot值
            if( l >= r) {
                break;
            }

            //交换
            temp = arr[l];
            arr[l] = arr[r];
            arr[r] = temp;

            //如果交换完后，发现这个arr[l] == pivot值 相等 r--， 前移
            if(arr[l] == pivot) {
                r -= 1;
            }
            //如果交换完后，发现这个arr[r] == pivot值 相等 l++， 后移
            if(arr[r] == pivot) {
                l += 1;
            }
        }

        // 如果 l == r, 必须l++, r--, 否则为出现栈溢出
        if (l == r) {
            l += 1;
            r -= 1;
        }
        //向左递归
        if(left < r) {
            quickSort(arr, left, r);
        }
        //向右递归
        if(right > l) {
            quickSort(arr, l, right);
        }
}
```

### 归并排序
基本思想：归并排序是利用归并的思想实现的排序方法，该算法采用经典的分治策略，分的阶段将问题分成一些小的问题然后递归求解，而治的阶段则将分的阶段得到的各答案“修补”在一起，即分而治之；归并排序合并两个有序字段，按原字段的顺序进行比较排序，当某一字段都比较完之后，另一字段剩余的部分都放在尾部。<br><br>
代码实现：<br>
```
public static void guibing(int[] ins,int left,int right,int[] temp){
        if(left<right){
            int mid=(left+right)/2;
            guibing(ins,left,mid,temp);
            guibing(ins,mid+1,right,temp);
            merge(ins,left,mid,right,temp);
        }
    }
    public static void merge(int[] ins,int left,int mid,int right,int[] temp){
        int l=left;
        int r=mid+1;
        int t=0;
        while(l<=mid && r<=right){
            if(ins[l]<ins[r]){
                temp[t]=ins[l];
                t++;
                l++;
            }else{
                temp[t]=ins[r];
                t++;
                r++;
            }
        }
        if(l<=mid){
            temp[t]=ins[l];
            t++;
            l++;
        }
        if(r<=right){
            temp[t]=ins[r];
            t++;
            r++;
        }

        t=0;
        int index=left;
        while(index<=right){
            ins[index]=temp[t];
            index++;
            t++;
        }
    }
```

### 基数排序
基本思想：将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。<br><br>
基数排序算法是效率高的稳定性排序算法，它是桶排序的扩展。<br><br>
代码实现：<br>
```
public static void jishu(int[] ins){
        int[][] tong=new int[10][ins.length];
        int[] tongshu=new int[10];
        int max=ins[0];
        for(int i=0;i<ins.length;i++){
            if(ins[i]>max){
                max=ins[i];
            }
        }
        int maxlenght=(max+"").length();

        for(int i=0,n=1;i<maxlenght;i++,n*=10){
            for(int j=0;j<ins.length;j++){
                int wei=ins[j]/n%10;
                tong[wei][tongshu[wei]]=ins[j];
                tongshu[wei]++;
            }
            int index=0;
            for(int z=0;z<tongshu.length;z++){
                if(tongshu[z]!=0){
                    for(int t=0;t<tongshu[z];t++){
                        ins[index]=tong[z][t];
                        index++;
                    }
                }
                tongshu[z]=0;
            }
        }

    }
```


