---
layout: post
#标题配置
title: 链表的实现详讲
#时间配置
date:   2019-07-17 15:14:00 +0800
#大类配置
categories: 数据结构和算法


---

* content
{:toc}
---
---

# data structure
我希望每个人对算法和数据结构是什么有一个深刻的认识，所以我们会在每一篇的文章的开始给你灌输下面的这些知识点：<br>
1、数据结构是一门研究组织数据方式的学科，有了编程语言也就有了数据结构，学好数据结构可以让你写出更漂亮更高效的代码（时间复杂度和空间复杂度）；<br>
2、算法其实就是解决我们生活中遇到的问题的计算方法；<br>
3、程序=数据结构+算法；要学好数据结构和算法就要在日常生活中把遇到的问题尝试用程序去解决<br>
4、数据结构是实现一个算法的基础，所以想要学好算法，需要把数据结构学到位；<br>
5、数据结构分为线性结构和非线性结构；线性结构的特点是数据元素之间存在一对一的线性关系；<br>
6、线性结构又有顺序存储结构和链式存储结构两种的存储结构，顺序存储结构中存储元素是连续的，而链式存储结构中存储元素不一定是连续的；

## 链表
链式存储结构对应的数据结构就是链表，链表以节点的方式进行存储，每个节点包括data域（存储本节点的数据）和next域（指向下一个节点）；<br>
链表可以是带头节点或者不带头节点的，同时链表又分为单向链表，双向链表和单向循环链表；<br>
节点类：<br>
```
public class Node {
	//data值
     int val;
    //下一个节点
     Node next;

    public Node(int val) {
        this.val = val;
    }

    public Node getNext() {
        return next;
    }

    public void setNext(Node next) {
        this.next = next;
    }

    public int getVal() {
        return val;
    }

    public void setVal(int val) {
        this.val = val;
    }

    @Override
    public String toString() {
        return "Node{" +
                "val=" + val +
                ", next=" + next +
                '}';
    }
}
```

### 单向链表
下面将创建一个单向链表类，并演示单向链表的增删改查功能：<br>
```
public class SingleLinkedList {
    Node head=new Node(0);
    public void add(Node node){//添加知道节点到链表尾部
        Node cur=head;
        while(cur.next!=null){
            cur=cur.next;
        }
        cur.next=node;
    }
    public void add(Node node,int index) throws Exception {//添加节点到链表指定位置，index表示要插入到第几位
        Node pre=null;
        Node cur=head;
        for(int i=0;i<index;i++){
            if(cur.next!=null) {
                pre = cur;
                cur = cur.next;
            }else {
                throw new Exception("超出链表范围");
            }
        }
        pre.next=node;
        node.next=cur;
    }
    public void remove(Node node) throws Exception {//删除对应的节点，如果节点不存在则抛出异常
        Node pre=null;
        Node cur=head;
        while(cur.next!=null){
            pre=cur;
            cur=cur.next;
            if(cur.val==node.val){
                pre.next=cur.next;
                return;
            }
        }
        throw new Exception("您要删除的节点不存在");
    }
    public void remove(int index) throws Exception {//删除指定位置的节点，如果节点不存在则抛出异常
        if(index<=0){
            throw new Exception("超出链表范围");
        }
        Node pre=null;
        Node cur=head;
       for(int i=0;i<index;i++){
           if(cur.next!=null) {
               pre = cur;
               cur = cur.next;
           }else {
               throw new Exception("超出链表范围");
           }
       }
       pre.next=cur.next;
    }
    public void edit(Node node,int val){//修改指定节点的值
        Node cur=head;
        while(cur.next!=null){
            cur=cur.next;
            if(cur.val==node.val){
                cur.val=val;
                return;
            }
        }
    }
    public int get(int index) throws Exception {//获取指定位置节点的值
        Node cur=head;
        for(int i=0;i<index;i++){
            if(cur.next!=null){
                cur=cur.next;
            }else {
                throw new Exception("超出链表范围");
            }
        }
        return cur.val;
    }
    public void list(){//打印链表
        Node cur=head;
        while(cur.next!=null){
            cur=cur.next;
            System.out.println(cur.toString());

        }
    }
    public Node getHead(){//获取链表的头结点
        return head;
    }

}
```
单链表的灵活使用在实际企业的面试中也经常出现，主要包括：<br>
1、求单链表中有效节点的个数:<br>
```
public static int count(Node head){
        int count=0;
        while(head.next!=null){
            count++;
            head=head.next;
        }
        return count;
    }
```
2、查找单链表中的倒数第k个节点:<br>
```
public static Node daoshu(Node head,int k){
        int len=count(head);
        for(int i=0;i<=len-k;i++){
            head=head.next;
        }
        return head;
    }
```
3、单链表的反转:<br>
```
 public static Node reverse(Node head){
        Node pre=null;
        Node next=null;
        while(head!=null){
            next=head.next;
            head.next=pre;
            pre=head;
            head=next;
        }
        return pre;
    }
```
4、从尾到头打印单链表:<br>
```
 public static void reversePrint(Node head){
        Stack<Integer> stack=new Stack<>();
        while(head.next!=null){
            stack.push(head.next.val);
            head=head.next;
        }
        while(stack.size()!=0){
            System.out.println(stack.pop());
        }
    }
```
5、合并两个有序的单链表:<br>
```
public static Node Merge(Node list1,Node list2) {

        if(list1 == null){
            return list2;
        }
        if(list2 == null){
            return list1;
        }
        if(list1.val <= list2.val){
            list1.next = Merge(list1.next, list2);
            return list1;
        }else{
            list2.next = Merge(list1, list2.next);
            return list2;
        }

    }
```

### 双向链表
双向链表相对与单向链表的优点：<br>
1、可以双向移动，不需要从头结点开始移动，寻找数据更加方便；<br>
2、可以自我删除节点，不需要借助辅助节点；<br>
<br>
双向链表类：<br>
```
public class DoubleLinkedList {
    public int findVal(DoubleNode node,int count){//寻找node1的前count个节点的值
        for(int i=0;i<count;i++){
            node=node.pre;
        }
        return node.val;
    }
    public void remove(DoubleNode node){//删除指定节点
        node.pre.next=node.next;
        node.next.pre=node.pre;
    }
}
class DoubleNode{
    int val;
    DoubleNode pre;
    DoubleNode next;

    public DoubleNode(int val) {
        this.val = val;
    }

    public int getVal() {
        return val;
    }

    public void setVal(int val) {
        this.val = val;
    }

    public DoubleNode getPre() {
        return pre;
    }

    public void setPre(DoubleNode pre) {
        this.pre = pre;
    }

    public DoubleNode getNext() {
        return next;
    }

    public void setNext(DoubleNode next) {
        this.next = next;
    }

    @Override
    public String toString() {
        return "DoubleLinkedList{" +
                "val=" + val +
                ", pre=" + pre +
                ", next=" + next +
                '}';
    }
}
```

### 单向循环链表
单向循环链表相对于单向链表的区别在于其每次添加一个新的元素到链表的尾部，都会将这个元素的next域指向头结点，形成循环环状；在实际的应用中比如Josephu环问题上，我们会选择不太头结点的单向循环链表；<br><br>
单向循环链表类：<br>
```
class JosephuList{
    Node first=null;
    Node cur=null;
    public void add(int num){
        for(int i=1;i<=num;i++){
            if(i==1){
                first=new Node(i);
                cur=first;
                cur.setNext(first);
            }else{
                Node node=new Node(i);
                cur.setNext(node);
                cur=cur.getNext();
                cur.setNext(first);
            }
        }
    }
    public void list(){
        cur=first;
        while(cur!=null){
            System.out.println(cur.getVal());
            cur=cur.getNext();
            if(cur==first){
                break;
            }
        }
    }
}
```

### Josephu问题
osephu问题为：设编号为1，2，…n的n个人围坐一圈，约定编号为k（1<=k<=n）的人从1开始报数，数到m 的那个人出列，它的下一位又从1开始报数，数到m的那个人又出列，依次类推，直到所有人出列为止，由此产生一个出队编号的序列。<br><br>
代码实现：<br>
```
 public void jesephu(int a,int b){
 		//辅助节点，指向当前节点的前一个节点，用于辅助删除；
        Node help=first;
        while(help.getNext()!=first){
            help=help.getNext();
        }
        for(int i=0;i<a-1;i++){
            help=help.getNext();
            first=first.getNext();
        }
        while(first.getNext()!=first){
            for(int j=0;j<b-1;j++){
                first=first.getNext();
                help=help.getNext();
            }
            System.out.println("出队——"+first.getVal());
            first=first.getNext();
            help.setNext(first);
        }
        System.out.println("最终玩家"+first.getVal());
    }
```

<br>

<br>

<center>有Marin的地方就有你的收获</center>
