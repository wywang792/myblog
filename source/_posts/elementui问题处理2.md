---
title: element-ui中input回车触发页面刷新问题及其解决方法
date: 2023-07-28 00:00:00
categories:
  - 常见问题
tags:
  - web
  - element-ui
abbrlink: 49d9275e
cover:
---

在element-ui的el-form表单中，如果只存在一个el-input输入框，当你输入值后按下回车，页面会发生刷新。这是因为当form元素中只有一个输入框时，按下回车将触发表单的默认提交事件，这是W3C标准的规定。

### 解决方法：

ElementUI也给出了解决方法。如果你希望阻止这一默认行为，可以在`<el-form>`标签上添加`@submit.native.prevent`。例如：

```html
<el-form 
  :model="form" 
  ref="form" 
  label-width="200px" 
  class="form" 
  @submit.native.prevent
>
  <el-form-item
    label="姓名"
    prop="name"
  >
    <el-input v-model.number="form.name"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="submit('form')">提交</el-button>
    <el-button @click="reset('form')">重置</el-button>
  </el-form-item>
</el-form>
```

然而，上述方法只是阻止了默认行为，如果你仍需要回车来提交表单，你可以通过以下方式解决：

```html
<el-form 
  :model="form" 
  ref="form" 
  label-width="200px" 
  class="form" 
  @submit.native.prevent="() => submit('form')"
>
  <el-form-item
    label="姓名"
    prop="name"
  >
    <el-input v-model.number="form.name"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" native-type="submit">提交</el-button>
    <el-button @click="reset('form')">重置</el-button>
  </el-form-item>
</el-form>
```

请注意以下几点：

- 确保使用 @submit.native.prevent 阻止原生事件，执行方法为搜索按钮按click事件

- 搜索按钮需要被包裹在 el-form 中
- 移除搜索按钮的click事件
- 确保增加 native-type="submit" 属性

此外，原生的form表单的单input(输入)和form包裹el-input回车刷新的问题也可以解决，有以下几种方法：

1. 在form表单内再增加一个隐藏的input(输入)框，可以通过hidden定义隐藏的输入字段，或者通过CSS样式进行隐藏
2. 去掉输入框的回车事件（根据需求来定，有的需要回车事件提交表单来查询或者执行其他操作则该方法不适用）
3. 阻止表单默认提交事件。例如，使用onsubmit句柄返回false来阻止原生form内的input(输入)的默认提交事件

原文地址：[ElementUI中input回车触发页面刷新问题及其解决方法_回车刷新页面-CSDN博客](https://blog.csdn.net/qq_39997939/article/details/131048775)