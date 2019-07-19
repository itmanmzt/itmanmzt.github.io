---
layout: post
#标题配置
title: 树的基础详讲和基础应用
#时间配置
date:   2019-07-19 15:49:00 +0800
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

## 二叉树
二叉树这种数据结构是改良了顺序表和链表两者的缺点，在增删改查都基于良好的性能的一种非线性的数据结构。<br><br>
数有很多种，每个节点最多只能有两个节点的一种形式称为二叉树，二叉树的子节点分为左节点和右节点。<br><br>
树存在很多概念：<br>
节点、根节点、父节点、子节点、叶子节点（没有子节点的节点）、节点的权（节点值）、节点的度（节点有多个个子树）、路径（从根节点找到该节点的路线）、层（根节点为第一层、依次类推）、子树（以子节点为根节点的树）、树的高度（最大层数）、数的宽度（每一层的节点数）、森林（多颗子树构成森林）；<br><br>
二叉树也存在多种不同的形式，它们具有不同的特性:<br>
满二叉树：一个二叉树，如果每一个层的结点数都达到最大值，则这个二叉树就是满二叉树。也就是说，如果一个二叉树的层数为K，且结点总数是(2^k) -1 ，则它就是满二叉树。<br>
完全二叉树：只有最下面的两层结点度小于2，并且最下面一层的结点都集中在该层最左边的若干位置的二叉树；<br>
顺序存储二叉树：从数据存储来看，数组存储方式和树的存储方式可以相互转换，即数组可以转换成树，树也可以转换成数组；<br>
线索二叉树：n个节点的二叉链表中含有n+1[2n-(n-1)=n+1]个空指针域。利用二叉链表中的空指针域，存放指向该节点在某种遍历次序下的前驱和后继节节点的指针（这种附加的指针称为“线索”），这种加上了线索的二叉链表称为线索链表，对应的二叉树称为线索二叉树；<br>
平衡二叉树：它是一 棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树 ；<br>
二叉搜索(排序)树：它或者是一棵空树，或者是具有下列性质的二叉树： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为二叉排序树 ;<br>
哈弗曼树(最优二叉树)：给定n个权值作为n个叶子结点，构造一棵二叉树，若带权路径长度达到最小，称这样的二叉树为最优二叉树，也称为哈夫曼树(Huffman Tree)。哈夫曼树是带权路径长度最短的树，权值较大的结点离根较近。 <br><br><br>
二叉树的遍历分为前序、中序和后序，对应有不同的算法，具体算法可以参考我的另外一篇文章：<br>
[二叉树的遍历算法](https://itmanmzt.github.io/2019/07/03/binary-tree/){:target="_blank"}<br><br><br>
然后下面我将通过代码的形式给大家展示二叉树这个数据结构的底层依据以及其一些基础操作的实现：<br>
```
public class BinaryTree {
    Node2 root=null;

    public Node2 getRoot() {
        return root;
    }
    public void add(Node2 node){
        if(root==null){
            root=node;
        }else{
            root.add(node);
        }
    }
    public Node2 search(int val){
        if(root==null){
            return null;
        }else{
            return root.search(val);
        }
    }
    public void midOrder(){
        if(root==null){
            return;
        }else {
            root.midOrder();
        }
    }
    public void remove(int val){
        if(root==null){
            return;
        }else{
            if(root.left==null && root.right==null){
                root=null;
                return;
            }
            root.remove(val);
        }
    }

}
class Node2{
    int val;
    Node2 left;
    Node2 right;

    public Node2(int val) {
        this.val = val;
    }

    public Node2 search(int val){//查找指定值在二叉树所对应的节点
        if(this.val==val){
            return this;
        }
        if(this.val>val && this.left!=null){
            return this.left.search(val);
        }else if(this.val<=val && this.right!=null){
            return this.right.search(val);
        }else{
            return null;
        }
    }
    public Node2 searchParent(int val){//查找指定值在二叉树对应节点的父节点
        if((this.left.val==val&&this.left !=null) || (this.right.val==val&&this.right!=null)){
            return this;
        }else{
            if(this.left!=null &&val<this.val){
                return this.left.searchParent(val);
            }else if(this.right!=null && val>=this.val){
                return this.right.searchParent(val);
            }else{
                return null;
            }
        }

    }
    
    public void add(Node2 Node2){//添加节点
        if(Node2==null){
            return;
        }
        if(Node2.val<this.val){
            if(this.left==null){
                this.left=Node2;
            }else{
                this.left.add(Node2);
            }
        }else{
            if(this.right==null){
                this.right=Node2;
            }else{
                this.right.add(Node2);
            }
        }
    }

    public void remove(int val){//删除节点
        Node2 node2=search(val);
        Node2 parent=searchParent(val);
        if(parent.left==node2){
            parent.left=null;
        }else if(parent.right==node2){
            parent.right=null;
        }
    }

    public void midOrder(){//中序遍历
        if(this.left!=null){
            this.left.midOrder();
        }
        System.out.printf(this.val+"\t");
        if(this.right!=null){
            this.right.midOrder();
        }
    }

    public Node2 getLeft() {
        return left;
    }

    public void setLeft(Node2 left) {
        this.left = left;
    }

    public Node2 getRight() {
        return right;
    }

    public void setRight(Node2 right) {
        this.right = right;
    }

}
```

## 顺序存储二叉树
顺序存储二叉树的特点：<br>
1、顺序二叉树通畅只考虑完全二叉树；<br>
2、第n个元素的左子节点为2*n+1(n表示二叉树中的第几个元素，从0开始编号);<br>
3、第n个元素的右子节点为2*n+2;<br>
4、第n个元素的父节点为(n-1)/2；

## 赫夫曼树
路径和路径长度：在一棵树中，从一个节点往下可以达到的孩子或孙子节点之间的通路，称为路径。通路中分支的数目称为路径长度。若规定根节点的层数为1，则从根节点到第L层节点的路径长度为L-1；<br>
节点的权及带权路径长度：若将树中节点赋给一个有着某种含义的数值，则这个数值称为该节点的权。节点的带权路径长度为从根节点到该节点之间的路径长度与该节点的权的乘积；<br>
树的带权路径长度：树的带权路径长度规定为所有叶子节点的带权路径长度之和，记为WPL，权值越大的节点离根节点越近的二叉树才是最优二叉树；<br><br>


代码实现：<br>
```
/*
实现步骤：
1、从小到大进行排序（通过对节点类实现comparable接口进行比较），将每一个数据，每个数据都是一个节点，每个节点可以看成是一颗最简单的二叉树；
2、取出根节点权值最小的两颗二叉树；
3、组成一颗心的二叉树，该新的二叉树的根节点的权值是前面两颗二叉树根节点权值的和；
4、将新的二叉树的根节点加到原二叉树中，以根节点的权值大小再次进行排序，不断重复前面的步骤直到数列中所有的数据都被处理，就得到一颗赫夫曼树
*/
public class Huffman {
    public static void main(String[] args) {
        int[] ins={2,4,8,6,7,2,6};
        Node node=huffman(ins);
        System.out.println(node);
        preOrder(node);

    }
    public static Node huffman(int[] ins){
        ArrayList<Node> nodes=new ArrayList<>();
        for (int i:ins
             ) {
            nodes.add(new Node(i));
        }
        while(nodes.size()>1){
            Collections.sort(nodes);

            Node left=nodes.get(0);
            Node right=nodes.get(1);

            Node parent=new Node(left.val+right.val);
            parent.setLeft(left);
            parent.setRight(right);

            nodes.remove(left);
            nodes.remove(right);

            nodes.add(parent);
        }
        return nodes.get(0);
    }
    public static void preOrder(Node node){
        if(node!=null){
            node.preOrder();
        }else{
            System.out.println("null");
        }
    }

}
class Node implements Comparable<Node>{
    int val;
    Node left;
    Node right;

    public Node(int val) {
        this.val = val;
    }

    public void preOrder(){
        System.out.printf("node="+this.val+"\t");
        if(this.left!=null){
            this.left.preOrder();
        }
        if(this.right!=null){
           this.right.preOrder();
        }
    }

    public Node getLeft() {
        return left;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public Node getRight() {
        return right;
    }

    public void setRight(Node right) {
        this.right = right;
    }

    @Override
    public int compareTo(Node o) {
        return this.val-o.val;
    }
}
```

## 赫夫曼编码
赫夫曼编码是一种编码方式，属于一种程序算法，是赫夫曼树在电讯通信中的经典应用之一，赫夫曼编码广泛地用于数据文件压缩，其压缩率通常在20%-90%之间。是一种可变字长编码(VLC)<br><br>
在通信领域信息处理的三种方式：<br>
1、定长编码：将字符转换成对应的ASCII码，然后再转换成对应的二进制；<br>
2、变长编码：按照各个字符出现的次数进行编码，原则是出现次数越多的，编码越小，比如出现最多的为0、第二多的为1、第三多的为10、第四多的为11....以此类推；但在这种字符的编码中并不像外面前面说的那样，它要求字符的编码都不能是其他字符编码的前缀，像前面的1就是10和11的前缀了，符合此要求的编码叫做前缀编码，前缀编码能保证你不会匹配到重复的编码；<br>
3、赫夫曼编码：获取各个字符对应的个数，按照上面字符出现的次数构建一颗赫夫曼树，次数作为权值，然后根据赫夫曼树，给各个字符规定编码（前缀编码)，向左的路径为0，向右的路径为1；最后按照规定的编码对字符串进行编码；<br><br>
赫夫曼编码是一种无损的压缩处理方案；<br><br>
注意事项：对于不同排序方法生成的赫夫曼树，其对应的赫夫曼编码也不完全一样，但是WPL是一样的，都是最小的，最后生成的赫夫曼编码长度也是一样的；<br>

## 文件压缩——赫夫曼编码
```
 public static void fileZip(String file,String dest){
        FileInputStream fis = null;
        FileOutputStream fos= null;
        ObjectOutputStream oos= null;
        try{
            fis=new FileInputStream(file);
            byte[] b=new byte[fis.available()];
            fis.read(b);
            byte[] bb=zip(b);
            fos=new FileOutputStream(dest);
            oos=new ObjectOutputStream(fos);
            oos.writeObject(bb);
            oos.writeObject(codeMap);
        }catch (Exception e){
            System.out.println(e.getMessage());
        }finally {
            try{
                fis.close();
                fos.close();
                oos.close();
            }catch (Exception e){
                System.out.println(e.getMessage());
            }
        }
    }
public static byte[] zip(byte[] bytes){
        Map<Byte,Integer> map=getMap(bytes);
        Node1 root=getCode(map);
        getCodeMap(root);
        byte[] codeBytes=zip(bytes,codeMap);
        return codeBytes;
    }

    private static byte[] zip(byte[] bytes, Map<Byte, String> codeMap) {
        StringBuilder stringBuilder=new StringBuilder();
        for (Byte b:bytes
             ) {
            stringBuilder.append(codeMap.get(b));
        }
        System.out.println(stringBuilder);
        int len;
        if(stringBuilder.length()%8==0){
            len=stringBuilder.length()/8;
        }else{
            len=stringBuilder.length()/8+1;
        }
        byte[] codeBytes=new byte[len];
        int index=0;
        for(int i=0;i<stringBuilder.length();i+=8){
            String str;
            if(i+8>stringBuilder.length()){
                str=stringBuilder.substring(i);
            }else {
                str=stringBuilder.substring(i,i+8);
            }
            codeBytes[index]=(byte)Integer.parseInt(str,2);
            index++;
        }
        return codeBytes;
    }

    static Map<Byte, String> codeMap=new HashMap<>();
    static StringBuilder stringBuilder=new StringBuilder();
    public static void getCodeMap(Node1 root) {
            if(root==null){
                return;
            }
            getString(root.getLeft(),"0",stringBuilder);
            getString(root.getRight(),"1",stringBuilder);
    }
    public static void getString(Node1 node,String code,StringBuilder stringBuilder){
        StringBuilder stringBuilder1=new StringBuilder(stringBuilder);
        stringBuilder1.append(code);
        if(node!=null){
            if(node.b==null){
                getString(node.getLeft(),"0",stringBuilder1);
                getString(node.getRight(),"1",stringBuilder1);
            }else{
                codeMap.put(node.b,stringBuilder1.toString());
            }
        }

    }


    public static Map<Byte,Integer> getMap(byte[] bytes){
        Map<Byte,Integer> map=new HashMap<>();
        for (Byte b:bytes
             ) {
            if(map.get(b)==null){
                map.put(b,1);
            }else{
                map.put(b,map.get(b)+1);
            }
        }
        return map;
    }
    private static Node1 getCode(Map<Byte, Integer> map) {
        List<Node1> list=new ArrayList<>();
        for (Map.Entry<Byte,Integer> e:map.entrySet()
             ) {
            list.add(new Node1(e.getValue(),e.getKey()));
        }
        while(list.size()>1){
            Collections.sort(list);

            Node1 left=list.get(0);
            Node1 right=list.get(1);

            Node1 parent=new Node1(left.val+right.val,null);
            parent.setLeft(left);
            parent.setRight(right);

            list.remove(left);
            list.remove(right);

            list.add(parent);
        }
        return list.get(0);
    }


}
class Node1 implements Comparable<Node1>{
    int val;
    Byte b;
    Node1 left;
    Node1 right;

    public Node1(int val, Byte b) {
        this.val = val;
        this.b = b;
    }

    public void preOrder(){
        System.out.printf("node="+this.b+"\t");
        if(this.left!=null){
            this.left.preOrder();
        }
        if(this.right!=null){
            this.right.preOrder();
        }
    }

    public Node1 getLeft() {
        return left;
    }

    public void setLeft(Node1 left) {
        this.left = left;
    }

    public Node1 getRight() {
        return right;
    }

    public void setRight(Node1 right) {
        this.right = right;
    }


    public int compareTo(Node1 o) {
        return this.val-o.val;
    }
}
```
## 文件解压——赫夫曼编码
```
public static void fileUnZip(String file,String dest){
        FileInputStream fis = null;
        FileOutputStream fos= null;
        ObjectInputStream oos= null;
        try{
            fis=new FileInputStream(file);
            oos=new ObjectInputStream(fis);
            byte[] b= (byte[]) oos.readObject();
            Map<Byte,String> map= (Map<Byte, String>) oos.readObject();
            byte[] bb=stringToByte(map,b);
            fos=new FileOutputStream(dest);
            fos.write(bb);
        }catch (Exception e){
            System.out.println(e.getMessage());
        }finally {
            try{
                fis.close();
                fos.close();
                oos.close();
            }catch (Exception e){
                System.out.println(e.getMessage());
            }
        }
    }
public static byte[] unzip(byte[] codeBytes){
        byte[] bytes1=stringToByte(codeMap,codeBytes);
        return bytes1;
    }

    private static byte[] stringToByte(Map<Byte, String> codeMap, byte[] codeBytes) {
        StringBuilder stringBuilder=new StringBuilder();
        for(int i=0;i<codeBytes.length;i++){
            byte b=codeBytes[i];
            boolean flag=(i==codeBytes.length-1);
            stringBuilder.append(byteToString(!flag,b));
        }
        //反转map
        Map<String,Byte> revCodeMap=new HashMap<>();
        for (Map.Entry<Byte,String> e:codeMap.entrySet()
             ) {
            revCodeMap.put(e.getValue(),e.getKey());
        }
        List<Byte> list=new ArrayList<>();
        for(int i=0;i<stringBuilder.length();){
            boolean flag=true;
            int index=1;
            Byte b=null;
            while(flag){
                String str=stringBuilder.substring(i,i+index);
                b=revCodeMap.get(str);
                if(b==null){
                    index++;
                }else {
                    flag=false;
                }

            }
            list.add(b);
            i+=index;
        }
        byte[] bytes1=new byte[list.size()];
        for(int i=0;i<list.size();i++){
            bytes1[i]=list.get(i);
        }
        return bytes1;
    }

    public static String byteToString(boolean flag,byte b){
        int temp=b;
        if(flag){
            temp |= 256;
        }
        String str=Integer.toBinaryString(temp);
        if(flag){
            return str.substring(str.length()-8);
        }else {
            return str;
        }

    }	
```

## 赫夫曼编码总结
1、如果文件本身就是经过压缩处理的，那么使用赫夫曼编码再压缩效率不会有明显变化，比如视频、ppt(我们打开ppt前面的加载过程就是在解压)等文件；<br>
2、赫夫曼编码是按字节来处理的，因此可以处理所有的文件(二进制文件，文本文件);<br>
3、如果一个文件中的内容重复的数据不多，压缩效果也不会很明显。<br>

## 二叉排序树(BST)
二叉排序树能帮助我们高效地进行查询和添加；<br><br>
二叉排序树的中序遍历就是一个有序的数组，下面将通过代码演示二叉排序树的添加、删除、查找和遍历：<br>
```
public class SortTree {
    public static void main(String[] args) {
        int[] ins={7,3,10,12,5,1,9,2};
        binaryTree binaryTree=new binaryTree();
        for(int i=0;i<ins.length;i++){
            binaryTree.add(new PXNode(ins[i]));
        }
        binaryTree.midOrder();
        binaryTree.del(2);
        System.out.println();
        binaryTree.midOrder();
    }

}
class binaryTree{
    PXNode root=null;

    public PXNode getRoot() {
        return root;
    }
    public void add(PXNode node){
        if(root==null){
            root=node;
        }else{
            root.add(node);
        }
    }
    public void midOrder(){
        if(root==null){
            return;
        }
        root.midOrder();
    }
    public PXNode search(int val){
        if(root==null){
            return null;
        }else {
            return root.search(val);
        }


    }
    public PXNode searchParent(int val){
        if(root==null){
            return null;
        }else{
            return root.searchParent(val);
        }
    }
    public int searchmin(PXNode node){
        PXNode node1=node;
        while(node1.left!=null){
            node1=node1.left;
        }
        del(node1.val);
        return node1.val;
    }
/*
删除的三种情况实现步骤：
1）删除叶子节点
1、寻找要删除的节点；
2、找到要删除节点的父节点；
3、确定删除节点是其父节点的左子节点还是右子节点；
4、删除节点
2）删除只有一颗子树的节点
1、前三步同上；
2、如果删除节点是父节点的左子节点
	如果删除节点有左子节点，那么parent.left=node.left;
	如果删除节点有右子节点，那么parent.left=node.right;
   如果删除节点是父节点的右子节点
   	如果删除节点有左子节点，那么parent.right=node.left;
	如果删除节点有右子节点，那么parent.right=node.right;
3)删除有两颗子树的节点
1、前两步同上；
2、从删除节点的右子树找到最小的节点；
3、用临时变量将最小节点的值保存；
4、删除该最小节点；
5、将临时变量的值赋值给删除节点
*/
    public void del(int val){
        if(root==null){
            return;
        }else {
            PXNode node=search(val);
            if(node==null){
                return;
            }
            if(root.left==null && root.right==null){
                root=null;
                return;
            }
            PXNode parent=searchParent(val);

            if(node.left==null && node.right==null){
                if(parent.left==node){
                    parent.left=null;
                }else if(parent.right==node){
                    parent.right=null;
                }
            }else if(node.left!=null && node.right!=null){
                int minval=searchmin(node.right);
                node.val=minval;
            }else{

                    if (node.left != null) {
                        if(parent!=null) {
                            if (parent.left.val == val) {
                                parent.left = node.left;
                            } else {
                                parent.right = node.left;
                            }
                        }else{
                            root=node.left;
                        }
                    }else{
                        if(parent!=null){
                            if(parent.left.val==val){
                                parent.left=node.right;
                            }else{
                                parent.right=node.right;
                            }
                        }else{
                            root=node.right;
                        }
                    }

            }
        }


    }

}
class PXNode{
    int val;
    PXNode left;
    PXNode right;

    public PXNode(int val) {
        this.val = val;
    }

    public PXNode search(int val){
        if(this.val==val){
            return this;
        }
        if(this.val>val && this.left!=null){
            return this.left.search(val);
        }else if(this.val<=val && this.right!=null){
            return this.right.search(val);
        }else{
            return null;
        }
    }
    public PXNode searchParent(int val){
        if((this.left.val==val&&this.left !=null) || (this.right.val==val&&this.right!=null)){
            return this;
        }else{
            if(this.left!=null &&val<this.val){
                return this.left.searchParent(val);
            }else if(this.right!=null && val>=this.val){
                return this.right.searchParent(val);
            }else{
                return null;
            }
        }

    }

    public void add(PXNode node){
       if(node==null){
           return;
       }
       if(node.val<this.val){
           if(this.left==null){
               this.left=node;
           }else{
               this.left.add(node);
           }
       }else{
           if(this.right==null){
               this.right=node;
           }else{
               this.right.add(node);
           }
       }
    }

    public void midOrder(){
        if(this.left!=null){
            this.left.midOrder();
        }
        System.out.printf(this.val+"\t");
        if(this.right!=null){
            this.right.midOrder();
        }
    }

    public PXNode getLeft() {
        return left;
    }

    public void setLeft(PXNode left) {
        this.left = left;
    }

    public PXNode getRight() {
        return right;
    }

    public void setRight(PXNode right) {
        this.right = right;
    }


}
```

## 平衡二叉树(AVL)
平衡二叉树是针对二叉排序树在特殊情况不能发挥其高效查询和添加功能而提出的，对于一个二叉排序树，如果它的左子树前部为空，那么就变成一个单链表结构了，这会很影响它的查询效率；<br><br>
平衡二叉树常用的实现方法有红黑树、AVL、替罪羊树、Treap、伸展树等；<br> <br>
AVL平衡二叉树的实现方式有三种：左旋转、右旋转和双旋转，下面将通过代码具体实现：<br><br>
当右子树高度-左子树高低>1的情况下进行左旋转：<br>
![](https://itmanmzt.github.io/styles/images/datastru/008.jpg){:align="center"}<br><br>
```
private void leftRotate(){
    Node newNode=new Node(value);
    NewNode.left=left;
    NewNode.right=right.left;
    value=right.value;
    right=right.right;
    left=newNode;
}
```
当左子树高度-右子树高低>1的情况下进行左旋转：<br>
![](https://itmanmzt.github.io/styles/images/datastru/009.jpg){:align="center"}<br><br>
```
private void rightRotate(){
    Node newNode=new Node(value);
    NewNode.right=right;
    NewNode.left=right.right;
    value=left.value;
    left=left.left;
    right=newNode;
}
```
<br><br>
在某些情况下，单旋转不能完成平衡二叉树的转换，需要进行双旋转；<br>
双旋转的思路：<br>
1、当符合右旋转条件时，判断它的左子树的右子树高度是否大于它的左子树高度；<br>
2、如果是的话先对当前这个阶段的左节点进行左旋转；<br>
3、再对当前节点进行右旋转；<br><br>

完整代码实现：<br>
```
public class AVLTreeTest {
    public static void main(String[] args) {
        int arr[]={10,11,7,6,8,9};
        AVLTree avlTree=new AVLTree();
        for(int i=0;i<arr.length;i++){
            avlTree.add(new AVLNode(arr[i]));
        }
        avlTree.midOrder();

        System.out.println(avlTree.getRoot().height()+"_"+avlTree.getRoot().getLeftHeight()+"_"+avlTree.getRoot().getRightHeight()+"_"+avlTree.getRoot().val);
    }
}
class AVLTree{
    AVLNode root=null;

    public AVLNode getRoot() {
        return root;
    }
    public void add(AVLNode node){
        if(root==null){
            root=node;
        }else{
            root.add(node);
        }
    }
    public void midOrder(){
        if(root==null){
            return;
        }
        root.midOrder();
    }
    public AVLNode search(int val){
        if(root==null){
            return null;
        }else {
            return root.search(val);
        }


    }
    public AVLNode searchParent(int val){
        if(root==null){
            return null;
        }else{
            return root.searchParent(val);
        }
    }
    public int searchmin(AVLNode node){
        AVLNode node1=node;
        while(node1.left!=null){
            node1=node1.left;
        }
        del(node1.val);
        return node1.val;
    }
    public void del(int val){
        if(root==null){
            return;
        }else {
            AVLNode node=search(val);
            if(node==null){
                return;
            }
            if(root.left==null && root.right==null){
                root=null;
                return;
            }
            AVLNode parent=searchParent(val);

            if(node.left==null && node.right==null){
                if(parent.left==node){
                    parent.left=null;
                }else if(parent.right==node){
                    parent.right=null;
                }
            }else if(node.left!=null && node.right!=null){
                int minval=searchmin(node.right);
                node.val=minval;
            }else{

                if (node.left != null) {
                    if(parent!=null) {
                        if (parent.left.val == val) {
                            parent.left = node.left;
                        } else {
                            parent.right = node.left;
                        }
                    }else{
                        root=node.left;
                    }
                }else{
                    if(parent!=null){
                        if(parent.left.val==val){
                            parent.left=node.right;
                        }else{
                            parent.right=node.right;
                        }
                    }else{
                        root=node.right;
                    }
                }

            }
        }


    }

}
class AVLNode{
    int val;
    AVLNode left;
    AVLNode right;

    public AVLNode(int val) {
        this.val = val;
    }

    public int height(){
        return Math.max((left==null)?0:left.height(),(right==null)?0:right.height()+1);
    }
    public int getLeftHeight(){
        if(left==null){
            return 0;
       }
        return left.height();
    }
    public int getRightHeight(){
        if(right==null){
            return 0;
        }
        return right.height();
    }

    private void leftRotate(){
        AVLNode newNode=new AVLNode(val);
        newNode.left=left;
        newNode.right=right.left;
        val=right.val;
        right=right.right;
        left=newNode;
    }

    private void rightRotate(){
        AVLNode newNode=new AVLNode(val);
        newNode.right=right;
        newNode.left=right.right;
        val=left.val;
        left=left.left;
        right=newNode;
    }

    public AVLNode search(int val){
        if(this.val==val){
            return this;
        }
        if(this.val>val && this.left!=null){
            return this.left.search(val);
        }else if(this.val<=val && this.right!=null){
            return this.right.search(val);
        }else{
            return null;
        }
    }
    public AVLNode searchParent(int val){
        if((this.left.val==val&&this.left !=null) || (this.right.val==val&&this.right!=null)){
            return this;
        }else{
            if(this.left!=null &&val<this.val){
                return this.left.searchParent(val);
            }else if(this.right!=null && val>=this.val){
                return this.right.searchParent(val);
            }else{
                return null;
            }
        }

    }

    public void add(AVLNode node){
        if(node==null){
            return;
        }
        if(node.val<this.val){
            if(this.left==null){
                this.left=node;
            }else{
                this.left.add(node);
            }
        }else{
            if(this.right==null){
                this.right=node;
            }else{
                this.right.add(node);
            }
        }
        if(getLeftHeight()-getRightHeight()>1){
            if(left!=null && left.getRightHeight()>left.getLeftHeight() ){
                left.leftRotate();
                rightRotate();
            }else {
                rightRotate();
            }
            return;
        }
        if(getRightHeight()-getLeftHeight()>1){
            if(right!=null && right.getLeftHeight()>right.getRightHeight()){
                right.rightRotate();
                leftRotate();
            }else {
                leftRotate();
            }
        }
    }

    public void midOrder(){
        if(this.left!=null){
            this.left.midOrder();
        }
        System.out.printf(this.val+"\t");
        if(this.right!=null){
            this.right.midOrder();
        }
    }

    public AVLNode getLeft() {
        return left;
    }

    public void setLeft(AVLNode left) {
        this.left = left;
    }

    public AVLNode getRight() {
        return right;
    }

    public void setRight(AVLNode right) {
        this.right = right;
    }
}
```
<br>

<br>

<center>有Marin的地方就有你的收获</center>




