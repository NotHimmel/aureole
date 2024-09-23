---
title: CS61B
published: 2024-09-19
description: ''
image: ''
tags: []
category: '驽马十驾'
draft: false 
lang: ''
---

记录CS61B学习过程中的心得和感受  
:::tip
版本：Josh Hug教课的指导书（也就是官网的Reading），统一用的是19sp的版本，里面有一些19（18）独有的lab，建议是跟着[21sp](https://sp21.datastructur.es/)的流程走，如果在reading中看到自己想强化的部分，就自己找每次reading最后的lab习题加练。  
资料：Reading基本上是Video的精华，如果看Reading有不理解的再去看对应的视频。  
作业批改（Autograder）：21版本 MB7ZPY  
:::
# 时间记录
09-19 安装IntelliJ（激活）Lec1  
09-20 Lec2 HW0（Java基础） Pro0（2048）  
09-21 Lec3(Testing) Lec4(Recursion) Lec5(LinkedList)  
09-22 Lab2(Debugging) Lec6(双向链表) Lec7(线性表扩容) Lab3(条件断点，随机性测试)
09-23 Pro1.1(Linked List Deque)

# 学习心得
按照课程计划的周来记录  
## 1week
Lec1,2介绍了main函数和一些基本的关键字、库用法。static关键字是区分是属于类还是实例的关键字。  
Josh还说出了我上学时讨厌Java的原因，看起来非常冗长，定义一个函数public static void main(String[] args)看起来比C++都要长，但他说长有长的坏处，在大型项目中会有优势，这让我开始静下心来敲每一行代码，慢慢熟悉Java的做事方法。  

## Pro0 2048
MVC框架完成了绝大多数功能，只让实现游戏结束条件和响应用户上下左右的按键。   
刚上手时候毫无头绪，看完Tips明白主要实现North方向的功能，就按照TDD（Test Driven Development）的思路来做。  
一个个测试执行，开始实现最简单的NoMerge的功能，就是按列来移动，找到对应的位置直接移动过去；  
第二个Case简单的Merge一次，就移动完之后，判断相邻的值是否相等，等的话就Merge；  
第三个Case是Merge两次，就再执行一次移动和Merge，也ok了；  
第四个Case是2 2 2 2 不能变成8，应该是4 4，我就知道之前的思路有问题了，核心就是移动和合并不能分的太开，不然Merge之后可能还会有要移动的需求，移动之后有合并的条件就一起做了，不然2 0 2 2 可能会变成 4 0 2 0  
核心代码
```java
    public boolean tilt(Side side) {
        boolean changed;
        changed = false;

        board.setViewingPerspective(side);//EAST  WEST
        for (int i = 0; i < board.size(); i++) {
            //move
            for (int j = board.size() - 1; j >= 0; j--) {
                int uhs = j;
                while (board.tile(i, uhs) == null) {
                    if (uhs == 0) {
                        break;
                    }
                    uhs--;
                }
                if (board.tile(i, uhs) == null)
                    break;
                if (uhs != j) {
                    Tile t = board.tile(i, uhs);
                    board.move(i, j, t);
                    changed = true;
                }
            }
            //merge and move
            for (int j = board.size() - 1; j >= 0; j--) {
                if (board.tile(i, j) == null)
                    break;

                if (j - 1 >= 0 && board.tile(i, j - 1) != null &&
                        board.tile(i, j - 1).value() == board.tile(i, j).value()) {
                    Tile t = board.tile(i, j - 1);
                    board.move(i, j, t);
                    score += t.value() * 2;
                    changed = true;
                    if (j-3>=0 && board.tile(i, j - 2) != null &&
                            board.tile(i, j - 3) != null &&
                            board.tile(i, j - 2).value() == board.tile(i, j-3).value()) {
                        Tile t2 = board.tile(i, j - 3);
                        board.move(i, j-2, t2);
                        score += t2.value() * 2;
                        t2 = board.tile(i, j - 2);
                        board.move(i, j-1, t2);
                    } else {
                        for (int o = j - 2; o >= 0; o--) {
                            if (board.tile(i, o) != null) {
                                Tile temp = board.tile(i, o);
                                board.move(i, o + 1, temp);
                            }
                        }
                    }
                }
            }
        }
        board.setViewingPerspective(Side.NORTH);
        checkGameOver();
        if (changed) {
            setChanged();
        }
        return changed;
    }
```
## 2week
Lec3（Testing）这节课给我感触很多，因为最近刚好准备过一个测试岗位的面试，讲的是TDD，也就是单元测试，用的是org.junit库,在不过度写单元测试的情况下，对于写代码的质量和效率提升都是很大的。顺便这节课还讲了递归实现选择排序。  
Lec4这节课就是为新程序员讲清楚值传递和地址传递的区别的，值传递时会复制，改变的是临时值，不会改变原本的值，针对基本数据类型。地址的话会一起改变，针对引用类型。  
Lec5这节课开始接触数据结构了，实现了一个简单的linkedlist，对于size方法给出了递归和迭代两种实现方式，引出了哨兵节点的必要性，让逻辑更统一，不必区分是否存在第一个节点。  
## 3week
Lec6 带有哨兵节点和last节点的单链表在删除尾部元素时的效率较低，自然而然地引出了双向链表，有两种实现形式。一种是前后哨兵节点的双向链表，另一种是带哨兵节点的循环链表。从array和class的使用对比中，引出了反射。  
Lec7 主要讨论读写效率更高的线性表，以及如何扩容。  
Lab3 主要熟练test case怎么写，随机性测试，条件断点以及crash断点的用法，说是会在之后的lab里用到。  

## Pro1
实现Deque



------
期待[gitlet](https://cs-plan.com/CS%E5%9F%BA%E7%A1%80/%E8%AF%BE%E7%A8%8B%E6%8E%A8%E8%8D%90/%E7%AE%97%E6%B3%95%E5%9F%BA%E7%A1%80/UCBCS61B/#gitlet)