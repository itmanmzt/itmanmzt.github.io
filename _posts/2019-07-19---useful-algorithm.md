---
layout: post
#标题配置
title: 十种常用算法祥讲
#时间配置
date:   2019-07-19 18:02:00 +0800
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

## 二分查找的非递归算法
```
public class BinarySearchNo {
    public static void main(String[] args) {
        int[] ins={1,2,3,4,5,6,7,8,9};
        System.out.println(binarySearch(ins,8));
    }
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
}
```

## 分治算法
分治算法介绍：<br>
分治法是一种很重要的算法。字面上的解释是“分而治之”，就是把一个复杂的问题分成两个或更多的相同或 相似的子问题，再把子问题分成更小的子问题……直到最后子问题可以简单的直接求解，原问题的解即子问题 的解的合并。这个技巧是很多高效算法的基础，如排序算法（快速排序，归并排序），傅立叶变换（快速傅立叶变换）<br>
分治算法可以求解的一些经典问题 :<br>
1、二分搜索；2、大整数乘法；3、棋盘覆盖；4、合并排序；5、快速排序；6、线性时间选择；7、最接近点对问题；8、循环赛日程表；9、汉诺塔 <br>
分治算法的实现步骤：<br>
分治法在每一层递归上都有三个步骤： <br>
1)分解：将原问题分解为若干个规模较小，相互独立，与原问题形式相同的子问题;<br>
2)解决：若子问题规模较小而容易被解决则直接解，否则递归地解各个子问题 ;<br>
3)合并：将各个子问题的解合并为原问题的解。 <br>
<br><br>
汉罗塔实现代码：<br>
```
public class HanoiTower {
    public static void main(String[] args) {
        hanoi(5,'A','B','C');
    }
    public static void hanoi(int val,char a,char b,char c){
        if(val==1){
            System.out.println(a+"->"+c);
        }else{
            hanoi(val-1,a,c,b);
            hanoi(1,a,b,c);
            hanoi(val-1,b,a,c);
        }

    }
}
```

## 动态规划算法
算法介绍：<br>
I)动态规划（Dynamic Programming）算法的核心思想是：将大问题划分为小问题进行解决，从而一步步获取最优解 的处理算法 ；<br>
2)动态规划算法与分治算法类似，其基本思想也是将待求解问题分解成若干个子问题，先求解子问题，然后从这 些子问题的解得到原问题的解；<br>
3)与分治法不同的是，适合于用动态规划求解的问题，经分解得到子问题往往不是互相独立的。（即下一个子 阶段的求解是建立在上一个子阶段的解的基础上，进行进一步的求解）；<br>
4)动态规划可以通过填表的方式来逐步推进，得到最优解 。<br><br>
<br>
针对背包问题的实现步骤：<br>
关于什么是背包问题以及一些相关的算法的更详细的信息，可以查看我的另外一篇博客：[0-1背包问题的多种求解方法](https://itmanmzt.github.io/2019/07/06/knapsack-problem/){:target="_blank"}<br><br>
算法的主要思想，利用动态规划来解决。每次遍历到的第i个物品，根据w[i]和v[j]来确定是否需要将该物品 放入背包中。即对于给定的n个物品，设v[i]、 w[i]分别为第i个物品的价值和重量，C为背包的容量。再令v[i][j] 表示在前i个物品中能够装入容量为J的背包中的最大价值。则我们有下而的结果：
(1) v[i][0]=v[0][j]=0;//表示填入表第一行和第一列是0
(2）当w[i]> j时：v[ij[j]=v[i- I][j]//当准备加入新增的商品的容量大于当前背包的容量时，就直接使用上个 单元格的装入策略
(3）当j>=w[i]时：v[i] [j]=max {v{i- I ][j], v{i]+v[i- 11 [1-w[i]] } 
	//当准备加入的新增的商品的容量小于等于当前背包的容量，
	//装入的方式： 
	v[i-1][j]：就是上一个单元格的装入的最大值 
	v[i]：表示当前商品的价值 
	v[i-1][i-w[i]]：装入i-I商品，到剩余空间j-w[i]的最大值
    当J>=w[i]时：v[i][j]=max{v[i-][j],v[i]+v[i-][j-w[i]]};
<br><br>


## KMP算法
应用场景——字符串匹配<br>
解决算法一：暴力匹配（用暴力方法解决的话就会有大量的回溯，每次只移动一位，若是不匹配，移动到下一位接着判断，浪费了大量的时间，所以在实际应用中是低效不可行的）；<br>
解决算法二：KMP算法<br><br>
KMP是一个解决模式串在文本串是否出现过，如果出现过，最早出现的位置的经典算法 <br>
KMP方法算法就利用之前判断过信息，通过一个next数组，保存模式串中前后最长公共子序列的长度，每次 回溯时，通过next数组找到，前面匹配过的位置，省去了大量的计算时间 <br>
KMP的具体实现步骤演示和原理讲解可以查看这篇博客：[https://www.cnblogs.com/ZuoAndFutureGirl/p/9028287.html](https://www.cnblogs.com/ZuoAndFutureGirl/p/9028287.html){:target="_blank"}<br><br>
代码实现：<br>
```
public class KMPSearch {
    static int[] ins;
    public static void main(String[] args) {
        String str="BBC ABCDAB ABCDABCDABDE";
        String str1="ABCDABD";
        KMPNext(str1);
        int index=KMP(str,str1);
        System.out.println(index);

    }
    public static int KMP(String str,String str1){
        int len=str.length();
        int len1=str1.length();
        for(int i=0,j=0;i<len;i++){
            while(j>0 && str.charAt(i)!=str1.charAt(j)){
                j=ins[j-1];
            }
            if(str.charAt(i)==str1.charAt(j)){
                j++;
            }
            if(j>=len1){
                return i-j+1;
            }
        }
        return -1;
    }
    public static void KMPNext(String str){
        ins=new int[str.length()];
        int len=str.length();
        ins[0]=0;
        for(int i=1,j=0;i<len;i++){
            while(j>0 && str.charAt(i)!=str.charAt(j)){
                j=ins[j-1];
            }
            if (str.charAt(i) == str.charAt(j)) {
                j++;
            }
            ins[i]=j;
        }
    }
}
```

## 贪心算法
基本思想：从问题的某一个初始解出发一步一步地进行，根据某个优化测度，每一步都要确保能获得局部最优解。每一步只考虑一个数据，他的选取应该满足局部优化的条件。若下一个数据和部分最优解连在一起不再是可行解时，就不把该数据添加到部分解中，直到把所有数据枚举完，或者不能再添加算法停止  。<br><br>
贪心算法得到的结果不一定是最优的结果，但是都是相对接近最优解的正确解；<br><br>
用贪心算法解决集合覆盖问题的代码实现：<br>
```
/*
实现的思路：
1、遍历所有的广播电台，找到一个覆盖了最多未覆盖的地区的电台；
2、将这个电台加入到一个集合中，然后把该电台覆盖的地区在下次比较时去掉；
3、重复第一步直到覆盖了全部的地区；
*/
public class TanXin {
    public static void main(String[] args) {
        Map<String, HashSet<String>> map=new HashMap<>();
        HashSet<String> h1=new HashSet<>();
        h1.add("北京");
        h1.add("广州");
        h1.add("上海");
        HashSet<String> h2=new HashSet<>();
        h2.add("深圳");
        h2.add("广州");
        h2.add("上海");
        HashSet<String> h3=new HashSet<>();
        h3.add("深圳");
        h3.add("广州");
        h3.add("北京");

        map.put("k1",h1);
        map.put("k2",h2);
        map.put("k3",h3);

        HashSet<String> all=new HashSet<>();
        all.addAll(h1);
        all.addAll(h2);
        all.addAll(h3);
        System.out.println(map);
        System.out.println(all);
        ArrayList<String> list=tanxin(map,all);
        System.out.println(list);

    }
    public static ArrayList<String> tanxin(Map<String, HashSet<String>> map, HashSet<String> all){
        ArrayList<String> list=new ArrayList<>();
        String maxindex=null;
        HashSet<String> set=new HashSet<>();
        HashSet<String> maxset=new HashSet<>(1000);
        while(all.size()>0){
            maxindex=null;
            for (String s:map.keySet()
                 ) {
                set=map.get(s);
                set.retainAll(all);
                if( set.size()>0 && (maxindex==null || set.size()>maxset.size())){
                    maxindex=s;
                    maxset.clear();
                    maxset.addAll(set);
                }
            }
            if(maxindex!=null){
                list.add(maxindex);
                all.removeAll(map.get(maxindex));
            }
        }
        return list;
    }
}
```
## 最小生成树问题
最小生成树（MST）：给定一个带权的无向连通图，如何选取一棵生成树，使树上所有边权的总和最小；<br>
求最小生成树的算法主要有普里姆算法和克鲁斯卡尔算法；<br>

## 普里姆算法(Prim)
普利姆（(Prim）算法求最小生成树，也就是在包含n个顶点的连通图中，找出只有（(n-I)条边包含所有n个顶点的 
连通子图，也就是所谓的极小连通子图 普利姆的算法如下： <br>
1)设G=(V,E）是连通网，T=(U,D）是最小生成树，v,U是顶点集合，E,D是边的集合；<br>
2)若从顶点U开始构造最小生成树，则从集合V中取出顶点U放入集合U中，标记顶点V的visited[u]=1;<br>
3)若集合U中顶点Ui与集合V-U中的顶点Vi之间存在边，则寻找这些边中权值最小的边，但不能构成回路，将 顶点Vi加入集合U中，将边（ui ,vj）加入集合D中，标记visited[vj]=1 ;<br>
4)重复上述步骤，直到U与V相等，即所有顶点都被标记为访问过，此时D中有n-i条边 ;<br><br>

代码实现：<br>
```
public class Prim {
    public static void main(String[] args) {
        char[] data={'A','B','C','D','E','F','G'};
        int length=data.length;
        final int N=Integer.MAX_VALUE;
        int[][] weight=new int[][]{
                {N,5,7,N,N,N,2},
                {5,N,N,9,N,N,3},
                {7,N,N,N,8,N,N},
                {N,9,N,N,N,4,N},
                {N,N,8,N,N,5,4},
                {N,N,N,4,5,N,6},
                {2,3,N,N,4,6,N}
        };
        Graph graph=new Graph(length,data,weight);
        MinTree minTree=new MinTree();
        minTree.prim(2,graph);


    }

}
class MinTree{
    public void prim(int val,Graph graoh){
        int lenght=graoh.length;
        char[] data=graoh.data;
        int[][] weight=graoh.weight;
        int[] visited=new int[lenght];
        visited[val]=1;
        int min=Integer.MAX_VALUE;
        int h1=-1;
        int h2=-1;
        int count=1;
        while(count<lenght){
            for(int i=0;i<lenght;i++){
                for(int j=0;j<lenght;j++){
                    if(visited[i]==1 && visited[j]==0 && weight[i][j]<min){
                        min=weight[i][j];
                        h1=i;
                        h2=j;
                    }
                }
            }
            System.out.println(data[h1]+"-->"+data[h2]+":"+min);
            visited[h2]=1;
            min=Integer.MAX_VALUE;
            count++;
        }
    }
}
class Graph{
    int length;
    char[] data;
    int[][] weight;

    public Graph(int length, char[] data, int[][] weight) {
        this.length = length;
        this.data = data;
        this.weight = weight;
    }
}
```

## 布鲁斯卡尔算法(Kruskal)
基本思想：按照权值从小到大的顺序选择n-1条边，并保证这n-1条边不构成回路；<br>
1、通过排序算法对权值按照从小到大进行排序；<br>
2、记录顶点在“最小生成树”中的终点（将所有顶点按照从小到大的顺序排列好之后，某个顶点的终点就是与它连通的最大顶点）。然后每次需要将一条边添加到最小生存树时，判断边的两个顶点的终点是否重合，重合的话则会构成回路。<br><br>
实现代码：<br>
```
public class Cruskal {
    static final int N = Integer.MAX_VALUE;
    static int[] ends;
    public static void main(String[] args) {
        char[] data = {'A', 'B', 'C', 'D', 'E', 'F', 'G'};
        int length = data.length;
        ends=new int[length];
        int[][] weight = new int[][]{
                {N, 5, 7, N, N, N, 2},
                {5, N, N, 9, N, N, 3},
                {7, N, N, N, 8, N, N},
                {N, 9, N, N, N, 4, N},
                {N, N, 8, N, N, 5, 4},
                {N, N, N, 4, 5, N, 6},
                {2, 3, N, N, 4, 6, N}
        };
        EData[] eData = getEdata(data, length, weight);
        print(eData);
        System.out.println();
        sortEData(eData);
        print(eData);
        System.out.println();
        cruskal(eData,length,data);



    }
    public static void cruskal(EData[] eData,int length,char[] data){
        EData[] result=new EData[eData.length];
        int[] visited=new int[length];
        int index=0;
        for(int i=0;i<eData.length;i++){
            int a1=getPosition(eData[i].left,length,data);
            int a2=getPosition(eData[i].right,length,data);

            int e1=getEnd(a1);
            int e2=getEnd(a2);

            if(e1!=e2 ){
                result[index++]=eData[i];
                ends[e1]=e2;

            }
        }
        for(int i=0;i<index;i++){
            System.out.println(result[i].toString());
        }
    }

    public static int getBLength(int[][] weight, int length) {
        int num = 0;
        for (int i = 0; i < length; i++) {
            for (int j = i + 1; j < length; j++) {
                if (weight[i][j] != N) {
                    num++;
                }
            }
        }
        return num;
    }

    public static EData[] getEdata(char[] data, int length, int[][] weight) {
        int num = getBLength(weight, length);
        EData[] eData = new EData[num];
        int index = 0;
        for (int i = 0; i < length; i++) {
            for (int j = i + 1; j < length; j++) {
                if (weight[i][j] !=N) {
                    eData[index++] = new EData(data[i], data[j], weight[i][j]);
                }
            }
        }
        return eData;
    }

    public static void sortEData(EData[] eData) {
        for (int i = 0; i < eData.length; i++) {
            for (int j = 0; j < eData.length - i - 1; j++) {
                if (eData[j].weight > eData[j + 1].weight) {
                    EData eData1 = null;
                    eData1 = eData[j];
                    eData[j] = eData[j + 1];
                    eData[j + 1] = eData1;
                }
            }
        }

    }
    public static void print(EData[] eData){
        for(int i=0;i<eData.length;i++){
            System.out.print(eData[i].toString()+"  ");
        }
    }
    public static int getPosition(char c,int length,char[] data){
        for(int i=0;i<length;i++){
            if(c==data[i]){
                return i;
            }
        }
        return -1;
    }
    public static int getEnd(int val){
        while(ends[val]!=0){
            val=ends[val];
        }
        return val;
    }
}
    class EData {
        char left;
        char right;
        int weight;

        public EData(char left, char right, int weight) {
            this.left = left;
            this.right = right;
            this.weight = weight;
        }

        @Override
        public String toString() {
            return "EData{" +
                    "left=" + left +
                    ", right=" + right +
                    ", weight=" + weight +
                    '}';
        }
    }
```

## 迪杰斯特拉算法(Dijkstra)
迪杰斯特拉（dijkstra)算法是典型最短路径算法，用于计算一个结点到其他结点的最短路径，它的主要特点是以 起始点为中心向外层层扩展（广度优先搜素思想），直到扩展到终点为止。 <br><br>
代码实现：<br>
```
/*
实现过程 ：
1、设置出发顶点为V，顶点集合V(vI,v2,vi..4. V到V巾各顶点的距离构成距离集合Dis. Dis{dl,d2,di..}， Dis 集合记录着V到图中各顶点的距离（到自身可以看作0，V到vi距离对应为di)
2、从Dis中选择值最小的di并移出Dis集合，同时移出V集合中对应的顶点vi，此时的V到vi即为最短路径 
3、更新Dis集合，更新规则为：比较v到V集合中顶点的距离值．与v通过vi到V集合中顶点的距离值，保留 值较小的一个（同时也应该更新顶点的前驱节点为vi.表明是通过vi到达的） 
4、重复执行上述步骤，直到最短路径顶点为目标顶点即可结束 
*/
public class Dijkstra {
    static char[] data;
    static int[][] weight;
    static VisitedNode visitedNode;
    public static void main(String[] args) {
        data=new char[]{'A','B','C','D','E','F','G'};
        int N=65535;
        weight=new int[][]{
                {N,5,7,N,N,N,2},
                {5,N,N,9,N,N,3},
                {7,N,N,N,8,N,N},
                {N,9,N,N,N,4,N},
                {N,N,8,N,N,5,4},
                {N,N,N,4,5,N,6},
                {2,3,N,N,4,6,N}
        };
        dijkstra(2);

    }
    public static void dijkstra(int val){
        int len=data.length;
        int length=0;
        visitedNode=new VisitedNode(len,val);

        for(int i=0;i<len;i++){
            if(i>0) {
                val = visitedNode.updateArr();
            }
            for(int j=0;j<len;j++){
                length=visitedNode.min[val]+weight[val][j];
                if(visitedNode.isvisited[j]==0 && length<visitedNode.min[j]){
                    visitedNode.min[j]=length;
                    visitedNode.pre[j]=val;
                }
            }
        }
        for(int i=0;i<visitedNode.min.length;i++){
            System.out.println(visitedNode.min[i]+"("+data[i]+")");
        }

    }

}

class VisitedNode{
    int[] isvisited;
    int[] pre;
    int[] min;

    public VisitedNode(int len,int index) {
        isvisited=new int[len];
        pre=new int[len];
        min=new int[len];
        isvisited[index]=1;
        Arrays.fill(min,65535);
        this.min[index]=0;
    }

    public int updateArr(){
        int low=65535;
        int index=0;
        for(int i=0;i<isvisited.length;i++){
            if(isvisited[i]==0 && min[i]<low){
                low=min[i];
                index=i;
            }
        }
        isvisited[index]=1;
        return index;
    }

}
```

## 弗洛伊德算法(Floyd)
和Dijkstra算法一样，弗洛伊德(Floyd)算法也是一种用于寻找给定的加权图中顶点间最短路径的算法；<br>
弗洛伊德算法vs迪杰斯特拉算法：迪杰斯特拉算法通过选定的被访问顶点，求出从出发访问顶点到其他顶点 的最短路径：弗洛伊德算法中每一个顶点都是出发访问点，所以高要将每一个顶点看做被访问顶点，求出从每 一个顶点到其他顶点的最短路径。 <br><br>
代码实现：<br>
```
/*
实现过程：
1、设置顶点vi到顶点vk的最短路径已知为Lik.顶点vk到vj的最短路径已知为Lkj.顶点vi到vj的路径为Ltj. 则vi到vj的最短路径为：min((Lik+Lkj)，Lij). vk的取值为图中所有顶点，则可获得vi到vj的最短路径 
2、至于vi到vk的最短路径Lik或者vk到勺的最短路径Lkj，是以与迪杰斯特拉算法同样的方式获得 
*/
public class Floyd {
    static int[][] weight;
    static int[][] pre;
    static char[] data;
    static final int N=65535;
    public static void main(String[] args) {
        data=new char[]{'A','B','C','D','E','F','G'};
        weight=new int[][]{
                {0,5,7,N,N,N,2},
                {5,0,N,9,N,N,3},
                {7,N,0,N,8,N,N},
                {N,9,N,0,N,4,N},
                {N,N,8,N,0,5,4},
                {N,N,N,4,5,0,6},
                {2,3,N,N,4,6,0}
        };
        pre=new int[][]{
                {0,0,0,0,0,0,0},
                {1,1,1,1,1,1,1},
                {2,2,2,2,2,2,2},
                {3,3,3,3,3,3,3},
                {4,4,4,4,4,4,4},
                {5,5,5,5,5,5,5},
                {6,6,6,6,6,6,6}
        };
        print(data,weight,pre);
        floyd(data,weight,pre);
        print(data,weight,pre);


    }
    public static void floyd(char[] data,int[][] weight,int[][] pre){
        for(int i=0;i<data.length;i++){
            for(int j=0;j<data.length;j++){
                for(int z=0;z<data.length;z++){
                    if( weight[j][z]>weight[j][i]+weight[i][z]){
                        weight[j][z]=weight[j][i]+weight[i][z];
                        pre[j][z]=pre[i][z];
                    }
                }
            }
        }
    }
    public static void print(char[] data,int[][] weight,int[][] pre){
        for(int i=0;i<data.length;i++){
            for(int j=0;j<data.length;j++){
                System.out.println(data[pre[i][j]]+"-->"+data[j]+"------:"+weight[i][j]);
            }
        }
    }

}
```

## 马踏棋盘算法
马踏棋盘问题（骑士周游问题）实际上是图的深度优先搜索(DFS)的应用;<br>
如果使用回溯（就是深度优先搜索）来解决，假如马儿踏了53个点，如图：走到了第53个，坐标(i,j)，发 现己经走到尽头，没办法．那就只能回退了，查看其他的路径．就在棋盘上不停的回溯……<br>
可以使用贪心算法来进行优化！<br><br>
代码实现：<br>
```
/*
实现步骤：
1、创建棋盘chessBoard，是一个二维数组 
2、将当前位置设置为已经访问，然后根据当前位置，计算马儿还能走哪些位置，并放入到一个集台中，最多有8个位置，每走一步，就使用step+1；
3、遍历ArrayList中存放的所有位置，看看哪个可以走通，如果走通，就继续，走不通，就回溯； 
4、判断马比是否完成了任务，使用Step和应该走的步数比较，如果没有达到数量，则表示没有完成任务，将整个棋盘清零；
注意：马儿不同的走法（策略），会得到不同的结果，效率也会有影响（优化） 
*/
public class ChessBoard {
    static int[][] board;
    static boolean[] visited;
    static boolean finished;
    static final int X=8;
    static final int Y=8;
    public static void main(String[] args) {

       board=new int[X][Y];
       visited=new boolean[X*Y];
       int row=1;
       int colum=1;
       chessboard(board,row-1,colum-1,1);
       print(board);
    }
    public static void chessboard(int[][] board,int x,int y,int step){
        board[x][y]=step;
        visited[y*X+x]=true;
        ArrayList<Point> arrayList=next(x,y);
        Sort(arrayList);
        while(!arrayList.isEmpty()){
            Point point=arrayList.remove(0);
            if(!visited[point.y*X+point.x]){
                chessboard(board,point.x,point.y,step+1);
            }
        }
        if(step<X*Y && !finished){
            board[x][y]=0;
            visited[y*X+x]=false;
        }else {
            finished=true;
        }

    }
    public static ArrayList<Point> next(int x,int y){
        ArrayList<Point> arrayList=new ArrayList<>();
        if(x-1>=0 && y-2>=0){
            arrayList.add(new Point(x-1,y-2));
        }
        if(x-2>=0 && y-1>=0){
            arrayList.add(new Point(x-2,y-1));
        }
        if(x-1>=0 && y+2<Y){
            arrayList.add(new Point(x-1,y+2));
        }
        if(x-2>=0 && y+1<Y){
            arrayList.add(new Point(x-2,y+1));
        }
        if(x+1<X && y-2>=0){
            arrayList.add(new Point(x+1,y-2));
        }
        if(x+2<X && y-1>=0){
            arrayList.add(new Point(x+2,y-1));
        }
        if(x+1<X && y+2<Y){
            arrayList.add(new Point(x+1,y+2));
        }
        if(x+2<X && y+1<Y){
            arrayList.add(new Point(x+2,y+1));
        }
        return arrayList;
    }
    /*
    贪心算法优化实现：
    1、获取当前位置可以走的下一个位置的集合；
    2、按当前位置可以走的下一个位置的集合中元素的个数进行非递减排序（因为下一位置集合中的元素个数少，也就说明回溯所需要走的步数更好，是当前的最优解）
    */
    public static void Sort(ArrayList<Point> arrayList){
        Collections.sort(arrayList, new Comparator<Point>() {
            @Override
            public int compare(Point o1, Point o2) {

                    int count1=next(o1.x,o1.y).size();
                    int count2=next(o2.x,o2.y).size();
                    if(count1<count2){
                        return -1;
                    }else if(count1==count2){
                        return 0;
                    }else {
                        return 1;
                    }

                }

        });
    }
    public static void print(int[][] board){
        for(int i=0;i<board.length;i++){
            for(int j=0;j<board[0].length;j++){
                System.out.print(board[i][j]+"  ");
            }
            System.out.println();
        }
    }
}
```

<br>

<br>

<center>有Marin的地方就有你的收获</center>