---
title: el-date-picker自动格式化输入时间
date: 2023-08-20 00:00:00
categories:
  - 常见问题
tags:
  - web
  - element-ui
abbrlink: c15868c5
cover:
---

在el-date-picker中，如果用户自行输入时间，总是要按照标准的时间格式(yyyy-MM-dd HH:mm:ss)进行输入，对于用户很不友好，我们可以通过代码允许用户随意输入，由代码来自动进行格式化

### 解决办法：

通过自定义指令实现

```html
<el-date-picker
  v-date-format
  v-model="searchDate"
  type="datetimerange"
  value-format="yyyy-MM-dd HH:mm:ss"
  range-separator="至"
  start-placeholder="开始日期"
  end-placeholder="结束日期">
</el-date-picker>
```

```javascript
// 以下代码，仅对一种格式进行了处理，即用户输入全数字(yyyyMMddHHmmss)
// 如果需要允许更多格式，可自行对代码进行修改
// 最后的事件是为了，用户完成输入后，绑定格式化后的时间，并关闭输入窗口

import Vue from 'vue'
import moment from 'moment'
// 时间选择器 - 输入格式转换
Vue.directive('dateFormat', {
  inserted: function(el, binding, vnode) {
    const { context: _this, data } = vnode
    const { expression: key } = data.model
    const modelValue = function(value, index) {
      // 把所有数字提取出来
      value = value.replace(/\D/g, '')
      // 按照指定格式解析日期
      const time = value && value.constructor === String ? moment(value, 'YYYYMMDDHHmmss').format('YYYY-MM-DD HH:mm:ss') : value  // 转换时间格式
      // 如果存在index，说明是范围选择的，绑定值为数组
      if (index === 0 || index === 1) {
        let keys = key.split('.')
        const [key1, key2] = keys
        if (key2) {
          if (!_this[key1][key2]) {
            _this.$set(_this[key1], key2, [])
          }
          _this.$set(_this[key1][key2], index, time)
          if (index === 0 && !_this[key1][key2][1]) {
            _this.$set(_this[key1][key2], 1, time)
          }
          if (index === 1 && !_this[key1][key2][0]) {
            _this.$set(_this[key1][key2], 0, time)
          }
        } else {
          if (!_this[key1]) {
            _this.$set(_this, key1, [])
          }
          _this.$set(_this[key1], index, time)
          if (index === 0 && !_this[key1][1]) {
            _this.$set(_this[key1], 1, time)
          }
          if (index === 1 && !_this[key1][0]) {
            _this.$set(_this[key1], 0, time)
          }
        }
      } else {
        _this.$set(_this, key, time)
      }
    }
    if (_this && _this._isVue) {
      const input1 = el.getElementsByTagName('input')[0]
      const input2 = el.getElementsByTagName('input')[1]
      // 判断是范围的还是单个独立的日期时间控件，范围的两个输入框都要绑定change事件
      if (el.getElementsByTagName('input').length > 1) {
        input1.addEventListener('change', function() {
          modelValue(input1.value, 0)
        })
        input1.addEventListener('keyup', function(event) {
          if (event.key === 'Enter') {
            el.__vue__.handleClose()
          }
        })
        input2.addEventListener('change', function() {
          modelValue(input2.value, 1)
        })
        input2.addEventListener('keyup', function(event) {
          if (event.key === 'Enter') {
            el.__vue__.handleClose()
          }
        })
      } else {
        input1.addEventListener('change', function() {
          let value = input1.value
          modelValue(value)
        })
      }
    }
  }
})

export default {}

```