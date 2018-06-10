---
title: weeklyCoding - RedBlackTree insert with C++
date: 2018-06-10 16:00
categories: blog
tags: [algorithm,tree]
---

> 红黑树是2-3-4树的一种等效树。换句话说，对于每个2-3-4树，都存在至少一个数据元素是同样次序的红黑树。在2-3-4树上的插入和删除操作也等同于在红黑树中颜色翻转和旋转。

### 红黑树的三个特性
1. 首尾节点全黑：根节点和每个叶子节点（NIL）是黑色。  
2. 红节点子全黑：如果一个节点是红色的，则它的子节点必须是黑色的。  
3. 黑节点数相同：从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。  

### [头文件:redblacktree.hpp](https://github.com/bblu/algocpp/tree/master/weeklyPractice)
```cpp
//  redblacktree.cpp  weekly
//  Created by lu wenbo on 2018/6/9.
//  Copyright © 2018 lu wenbo. All rights reserved.
//
#include <stdio.h>
#include <iostream>
using namespace std;

//namespace bblu{
enum Color{
    BLACK,
    RED
};

struct Node{
    int key;
    Node *parent;
    Node *left;
    Node *right;
    Color color;
    Node(Node* p,int k){
        parent = p;
        key = k;
        color = RED;
        left = NULL;
        right = NULL;
    }
    bool isRed(){
        return this->color == RED;
    }
    void setRed(){
        this->color = RED;
    }
    void setBlack(){
        this->color = BLACK;
    }
    bool hasParent(){
        return this->parent != NULL;
    }
    bool hasLeft(){
        return this->left != NULL;
    }
    bool hasRight(){
        return this->right != NULL;
    }
    bool operator < (Node *that){
        return this->key < that->key;
    }
};

class RedBlackTree{
    Node *root;
public:
    RedBlackTree(){
        root = NULL;
    }
    void insert(int key);
    void search(int key);
private:
    void insertfix(Node *node);
    void leftrotate(Node *node);
    void rightrotate(Node *node);
};

//} //bblu end namespace

```

### [源文件:redblacktree.cpp](https://github.com/bblu/algocpp/tree/master/weeklyPractice)

```cpp
#include <array>
#include "redblacktree.hpp"
using namespace std;

//namespace bblu{
void RedBlackTree::insert(int key){
    Node *n = new Node(NULL, key);
    Node *p = root, *q = NULL;
    cout << "|-insert:" << key << endl;
    if(root == NULL){
        root = n;
    }else{
        //find for the property node for insert key
        while(p != NULL){
            q = p;
            if(p < n){
                p = p->right;
                cout << "|--- p = p->right" << endl;
            }else{
                p = p->left;
                cout << "|--- p = p->left" << endl;
            }
        }
        n->parent = q;
        if(q < n){
            q->right = n;
        }else{
            q->left = n;
        }
    }
    insertfix(n);
}

void RedBlackTree::insertfix(Node *node){
    if(root == node){
        node->color = BLACK;
        return;
    }
    Node *uncle;
    while(node->parent!=NULL && node->parent->color == RED){
        Node *gran = node->parent->parent;
        if(gran->left == node->parent){
            if(gran->right != NULL){
                uncle = gran->right;
                if(uncle->isRed()){
                    node->parent->setBlack();
                    uncle->setRed();
                    node = gran;
                }
            }else{
                if(node->parent->right == node){
                    node = node->parent;
                    leftrotate(node);
                }
                node->parent->setBlack();
                gran->setRed();
                rightrotate(gran);
            }
        }else{
            if(gran->left != NULL){
                uncle = gran->left;
                if(uncle->isRed()){
                    node->parent->setBlack();
                    uncle->setBlack();
                    gran->setRed();
                    node = gran;
                }
            }else{
                if(node->parent->left == node){
                    node = node->parent;
                    rightrotate(node);
                }
                node->parent->setBlack();
                gran->setRed();
                leftrotate(gran);
            }
        }
        root->setBlack();
    }
}

/*
 对4进行左旋，意味着将4变成一个原位置节点的左节点。
 
      8                               8
     / \                             / \
    4   12    =leftrotate(4)=>      6   12
   /  \                            / \
  2    6                          4   7
 / \                             / \
5   7                           2   5

 对比234树的操作把节点4升级成3NodeTree，上图旋转前的情况是选择把4提升为父节点，左旋转是换成了把6提成成父节点,这样理解的话右旋就是相反的操作。节点4左旋就是通过旋转操作把4变成6的左节点，。

      8
     / \
   4|6  12
  / | \
 2  5  7
 */

void RedBlackTree::leftrotate(Node *node){
    if(!node->hasRight())
        return;
    cout << "|------ leftrotate for " << node->key << endl;
    Node *r = node->right;
    if(r->hasLeft()){
        node->right = r->left;
        r->left->parent = node;
    }else{
        node->right = NULL;
    }
    if(node->hasParent())
        r->parent = node->parent;
    if(!node->hasParent()){
        root = r;
    }else{
        if(node->parent->hasLeft())
            node->parent->left = r;
        else
            node->parent->right = r;
    }
    r->left = node;
    node->parent = r;
}
void RedBlackTree::rightrotate(Node *node){
    if(!node->hasLeft()){
        return;
    }
    cout << "|------ rightrotate for " << node->key << endl;
    Node *l = node->left;
    if(l->hasRight()){
        node->left = l->right;
        l->right->parent = node;
    }else{
        node->left = NULL;
    }
    if(node->hasParent())
        l->parent = node->parent;
    if(!node->hasParent()){
        root = l;
    }else{
        if(node == node->parent->left)
            node->parent->left = l;
        else
            node->parent->right = l;
    }
    l->right = node;
    node->parent = l;
}

//} //bblu end namespace


int main(){
    array<int,7> keys = {8,4,6,12,2,5,7};
    RedBlackTree *tree = new RedBlackTree();
    
    for(const auto& key: keys)
        tree->insert(key);
    

    cout << "red-black tree:" << endl;
    return 0;
}
```

### output
```
|-insert:8
|-insert:4
|--- p = p->left
|-insert:6
|--- p = p->left
|--- p = p->right
|------ leftrotate for 4
|------ rightrotate for 8
|-insert:12
|--- p = p->right
|--- p = p->right
|-insert:2
|--- p = p->right
|--- p = p->right
|--- p = p->right
|------ leftrotate for 8
|-insert:5
|--- p = p->right
|--- p = p->left
|-insert:7
|--- p = p->right
|--- p = p->left
|--- p = p->right
|------ leftrotate for 5
|------ rightrotate for 8
red-black tree:
Program ended with exit code: 0
```