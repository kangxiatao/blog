---
title: c++笔记
date: 2022-07-28 12:00:00
categories: 码农笔记
tags:
  - c++
toc: true
---

## 前言

太久没用C了，记录下C++的一些用法，无他。

<!--more-->

## 数据结构

- 优先队列
 
```
struct comp {
	bool operator()(ListNode* a, ListNode* b) {
		return a->val > b->val;  // 大顶堆
	}
};

priority_queue<ListNode*, vector<ListNode*>, comp> q;
```

