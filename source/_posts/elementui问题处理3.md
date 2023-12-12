---
title: el-select下拉选项设置为表格
date: 2023-08-11 00:00:00
categories:
  - 常见问题
tags:
  - web
  - element-ui
abbrlink: d5d15103
cover:
---

在工作中，遇到下拉需要打开一个表格，通过双击表格行内容，来进行选择。使用表格的原因是为了在下拉选项中展示更多内容。

### 两种方案：

#### 方案一：

通过el-select原生的slot来处理，el-select原生有具名插槽empty，用于无选项时展示

```html
<!-- 代码仅作简单展示，具体属性请自行绑定-->
<el-select>
  <el-table slot="empty"
    @row-dblclick="(row) => changeUser(scope.$index, row)">
    <el-table-column label="姓名" prop="name" ></el-table-column>
    <el-table-column label="年龄" prop="age"></el-table-column>
    <el-table-column label="性别" prop="sex"></el-table-column>
  </el-table>	
</el-select>
```

#### 方案二：

通过el-popover模拟实现下拉时展示表格

```html
<el-popover
  placement="bottom"
  trigger="manual"
  v-model="popoverVisible">
  <template slot="reference">
    <el-button size="small" @click="popoverVisible = !popoverVisible">
      <span style="display:inline-block; width: 68px">请选择</span>&nbsp;&nbsp;&nbsp;
      <i class="el-icon-arrow-down"></i>
    </el-button>
  </template>
  <el-table
    @row-dblclick="(row) => changeUser(scope.$index, row)">
    <el-table-column label="姓名" prop="name" ></el-table-column>
    <el-table-column label="年龄" prop="age"></el-table-column>
    <el-table-column label="性别" prop="sex"></el-table-column>
  </el-table>
  <span style="display: inline-block;margin-top: 16px;">注：双击一行以选择数据</span>
  <el-button size="small" style="margin-top: 16px; float: right" @click="popoverVisible = false">取消</el-button>
</el-popover>
```