---
layout: post
title: "高效内存分配"
date: 2019-06-06 16:19:59 +0800
comments: true
categories: 
---

# 背景
针对大量小内存分配场景下（例如，企业通讯录，每个用户信息对应一个json node），系统调用malloc/calloc等重复向系统申请内存资源，导致系统性能低下。

<!--more-->

# 解决方案
## 核心思想
每次向系统申请固定大小的内存资源，每次用户申请小内存资源时，直接从应用进程已申请的内存资源中获取。

## 数据结构
`hello world;`

```
sturct ptr {
    int a;
    int b;
    int c;
    char ptr[0];
}
```
## 知识点
- 在结构体内引入指针，实现对特定地址便宜的标记。
- 由于内存对齐，每个结构体成员变量的大小以及顺序尤其需要关注。
- c语言malloc分配的内存地址不能直接赋值给一个c++对象，即malloc与new存在差异性。new操作内部包含以下三个过程：
   - 向系统申请内存空间（类似malloc操作，该内存空间可以称之为裸内存）。
   - 将内存空间与对象函数指针列表绑定。
   - 调用对象构造函数，完成对象初始化。


## 参考文章
- [ACL: https://github.com/acl-dev/acl/](https://github.com/acl-dev/acl/)
