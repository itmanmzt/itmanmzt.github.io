---
layout: post
#标题配置
title: 解释器模式及其实例应用
#时间配置
date:   2019-07-02 14:37:00 +0800
#大类配置
categories: 设计模式


---

* content
{:toc}
---
---

# interpreter pattern
解释器模式（interpreter）给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器用来解释语言中的句子。这里提到的文法和句子的概念同编译原理中的描述相同，“文法”指语言的语法规则，而“句子”是语言集中的元素。<br>
所以我们可以说解释器模式就是一个根据普适规律定义规则来供用户使用的模式。

## 结构
抽象表达式 (AbstractExpression)

定义解释器接口，解释器的解释操作，主要包含interpret()方法，这个方法为抽象语法树中所有的节点所共有；。

终结符表达式 (TermianExpression)

实现与文法中的终结符相关联的解释操作。实现抽象表达式所要求的接口。文法中的每一个终结符都有一个具体终结表达式与之相对应。

非终结符表达式 (NonterminalExpression)

用来实现文法中与终结符相关的操作，文法中的每条规则都对应一个非终结符表达式。

环境角色(Context)

通常包含各个解释器需要的数据或公共的功能等全局信息。

客户端 (Client)

客户端代码，构建表示该文法定义的语言中一个特定的句子的抽象语法树，调用解释操作。

## 优缺点
优点
1、可扩展性比较好，灵活。
2、增加了新的解释表达式的方式。
3、易于实现文法。

缺点
1、执行效率比较低，可利用场景比较少。
2、对于复杂的文法比较难维护。

## 应用场景
1、当语言的文法较为简单，且执行效率不是关键问题是。
2、当问题重复出现，且可以用一种简单的语言来进行表达时。
3、当一个语言需要解释执行，并且语言中的句子可以表示为一个抽象语法树的时候。

## 实例代码
下面我们用解释器模式设计一个搜索音乐的程序
抽象表达式：
```
public interface Expression {
    public boolean interpret(String dex);
}
```
终结符表达式：
```
public class TermainExcepssion implements Expression {
    private Set<String> set=new HashSet<String>();

    public TermainExcepssion(String[] str) {
        for(int i=0;i<str.length;i++){
            set.add(str[i]);
        }
    }

    @Override
    public boolean interpret(String dex) {
        if(set.contains(dex)){
            return true;
        }else{
            return false;
        }
    }
}
```
非终结符表达式：
```
public class NontermainExcepssion implements Expression {
    Expression singer;
    Expression song;

    public NontermainExcepssion(Expression singer, Expression song) {
        this.singer = singer;
        this.song = song;
    }

    @Override
    public boolean interpret(String dex) {
        String[] str=dex.split("的");
        return singer.interpret(str[0])&&song.interpret(str[1]);
    }
}
```
环境角色：
```
public class Context {
    private String[] singers={"邓紫棋","林俊杰","林宥嘉"};
    private String[] songs={"另一个童话","我很想爱他","残酷月光"};
    private Expression expression;

    public Context() {
        Expression singer=new TermainExcepssion(singers);
        Expression song=new TermainExcepssion(songs);
        expression=new NontermainExcepssion(singer,song);
    }
    public void search(String info){
        boolean bb=expression.interpret(info);
        if(bb){
            System.out.println("正在播放"+info);
        }else{
            System.out.println(info+"暂时没有版权");
        }

    }
}
```
测试类：
```
public class Client {
    public static void main(String[] args) {
        Context context=new Context();
        context.search("邓紫棋的另一个童话");
        context.search("林俊杰的我很想爱他");
        context.search("林宥嘉的残酷月光");
        context.search("周杰伦的告白气球");
    }
}
```
运行结果：
![](https://itmanmzt.github.io/styles/images/jieshiqi/001.jpg){:align="center"}<br><br>

## 模式的扩展
在项目开发中，如果要对数据表达式进行分析与计算，无须再用解释器模式进行设计了，Java 提供了以下强大的数学公式解析器：Expression4J、MESP(Math Expression String Parser) 和 Jep 等，它们可以解释一些复杂的文法，功能强大，使用简单。

  <br>

<br>

<center>有Marin的地方就有你的收获</center>