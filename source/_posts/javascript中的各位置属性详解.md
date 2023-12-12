---
title: javascript中的各位置属性详解
date: 2023-07-22 00:00:00
categories:
  - 常见问题
tags:
  - javascript
  - web
  - element-ui
abbrlink: f55dc13
cover:
---

工作中需要开发一个滚动条滚动到底时加载数据的功能，需要判断滚动条位置，动态去加载数据，对javascript中的所有位置属性做一个总结
如图：
![20180104085724397](./assets/javascript中的各位置属性详解/20180104085724397.gif)

假设 obj 为某个 HTML 控件，以下位置信息的单位均为像素

**obj.offsetWidth**: 指 obj 控件自身的宽度，包含边框
**obj.offsetHeight**: 指 obj 控件自身的高度，包含边框
**obj.offsetTop**: 指 obj 对象上侧与定位父级之间的距离
**obj.offsetLeft**: 指 obj 对象左侧与定位父级之间的距离

**obj.scrollWidth**: 指滚动条可滚动区域整个的宽度
**obj.scrollHeight**: 指滚动条可滚动区域整个的高度
**obj.scrollTop**: 指滚动条上方已经经过的区域的高度
**obj.scrollLeft**: 指滚动条左方已经经过的区域的宽度

**obj.clientWidth**: 指控件可视区域内，内容区的宽度
**obj.clientHeight**: 指控件可视区域内，内容区的高度
**obj.clientTop**: 指控件上方边框的高度，如果没有边框或者不定位该元素，它的值就是0
**obj.clientWidth**: 指控件左方边框的宽度，如果没有边框或者不定位该元素，它的值就是0


```javascript
// 计算滚动条距离容器底部的距离
const scrollDistance = dom.scrollHeight - dom.scrollTop - dom.clientHeight
```

## 完整实现代码
```javascript
// 寻找元素
// element2中可滚动区域容器为el-table__body-wrapper
// element3中可滚动区域容器为el-scrollbar__wrap
let dom = this.$refs.dataElementTable.$el.querySelector('.el-table__body-wrapper')

// 为了避免滚动条触底时，触发过多的加载事件，简单的加上节流函数处理
// hasScrollEventListener为了避免多次给元素添加监听事件
let timeout = null
if (!dom.hasScrollEventListener) {
  // 给容器添加滚动条监听事件
  dom.addEventListener('scroll', () => {
    const scrollDistance = dom.scrollHeight - dom.scrollTop - dom.clientHeight
    if(scrollDistance < 10) {
      // 每次触发scroll，先清除定时器
      clearTimeout(timeout)
      timeout = setTimeout(this.queryDataElementTableData, 200)
    }
  })
  dom.hasScrollEventListener = true
}

```
