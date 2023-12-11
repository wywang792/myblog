---
title: key=”Math.random()“问题处理
date: 2023-05-10 14:23:04
categories:
  - 常见问题
tags:
  - web
  - element-ui
abbrlink: 3a983c7a
cover:
---
## 问题现象
最近项目开发中，测试人员提出表格在最初渲染时会出现一定程度的抖动

经过查询和调试，发现是el-table-column标签上的:key=”Math.random()“属性导致

删除该属性后，渲染出的表头又会发生乱序

原来的开发人员应该是为了解决表头乱序的问题，给每列增加了:key=”Math.random()“属性

没想到又导致了渲染时抖动的问题

## 关于key的定义
有相同父元素的子元素必须有独特的 key。重复的 key 会造成渲染错误。
也就是说必须保证key是唯一标识

key主要用在vue的虚拟Dom算法，在新旧节点对比时辨识VNodes。使用key时，它会基于key的变化重新排列元素顺序，并且会移除key不存在的元素。

有相同父元素的子元素必须有独特的key，重复的key会造成渲染错误。
给列加上key之后，再这样切换表格的时候会不出现表头字段的错乱

## 解决办法
替换key属性，不要使用随机数来做，改为固定值，需要注意，不同的column需要使用不同的key
```html
<el-table
  :data="tableData"
  style="width: 100%">
  <el-table-column
    prop="date"
    label="日期"
    width="180">
  </el-table-column>
  <el-table-column
    :key="1"
    prop="name"
    label="姓名"
    width="180">
  </el-table-column>
  <el-table-column
    :key="2"
    prop="address"
    label="地址">
  </el-table-column>
</el-table>
```

