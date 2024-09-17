---
title: vscode的使用
categories:
  - 开发工具
tags:
  - vscode
abbrlink: 861717a6
date: 2022-10-22 12:00:00
cover:
---

## VS Code优化配置项

| 配置名称                           | 配置项                                              | 说明                                                         |
| ---------------------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| Auto Save                          | "files.autoSave": "afterDelay"                      | 开启延迟自动保存                                             |
| Bracket Pair Colorization          | "editor.bracketPairColorization.enabled":  true     | 开启匹配括号着色                                             |
| Mouse Wheel Zoom                   | "editor.mouseWheelZoom": true                       | 开启鼠标滚轮控制字体缩放                                     |
| Smooth Scrolling                   | "workbench.list.smoothScrolling": true              | 开启工作台列表具有平滑滚动效果                               |
| Cursor Smooth Caret Animation      | "editor.cursorSmoothCaretAnimation": "on"           | 开启编辑台光标平滑插入动画                                   |
| Smooth Scrolling                   | "editor.smoothScrolling": true                      | 开启编辑台平滑滚动动画                                       |
| Smooth Scrolling                   | "editor.cursorBlinking": "smooth"                   | 开启光标闪烁时动画                                           |
| Cursor Blinking                    | "terminal.integrated.smoothScrolling": true         | 开启终端平滑滚动动画                                         |
| Format On Paste                    | "editor.formatOnPaste": true                        | 开启粘贴时格式化代码                                         |
| Format On Save                     | "editor.formatOnSave": true                         | 开启保存时格式化代码                                         |
| Format On Type                     | "editor.formatOnType": true                         | 开启输入一行时格式化代码                                     |
| Word Wrap                          | "editor.wordWrap": "on"                             | 开启自动折行                                                 |
| Show Breakpoints In Overview Ruler | "debug.showBreakpointsInOverviewRuler": true        | 开启将断点位置显示在右侧标尺处，方便定位断点位置             |
| Accept Suggestion On Enter         | "editor.acceptSuggestionOnEnter": "smart",          | 将回车键作为接受建议键<br />默认为on，只能使用回车接收建议<br />修改为smart，可以同时使用Tab和回车接受建议 |
| Save All Files Before Run          | "code-runner.saveAllFilesBeforeRun": true           | CodeRunner配置，运行前保存全部文件                           |
| Save File Before Run               | "code-runner.saveFileBeforeRun": true               | CodeRunner配置，运行前保存全部文件                           |
| Run In Terminal                    | "code-runner.runInTerminal": true                   | CodeRunner配置，可以在终端运行                               |
| Default Formatter                  | "editor.defaultFormatter": "esbenp.prettier-vscode" | 设置默认的代码格式化工具，需安装Prettier                     |

```javascript
{
  "files.autoSave": "afterDelay",
  "files.autoGuessEncoding": true,
  "workbench.list.smoothScrolling": true,
  "editor.cursorSmoothCaretAnimation": "on",
  "editor.smoothScrolling": true,
  "editor.cursorBlinking": "smooth",
  "editor.mouseWheelZoom": true,
  "editor.formatOnPaste": true,
  "editor.formatOnType": true,
  "editor.formatOnSave": true,
  "editor.wordWrap": "on",
  "editor.guides.bracketPairs": true,
  "editor.bracketPairColorization.enabled": true, 
  "editor.suggest.snippetsPreventQuickSuggestions": false,
  "editor.acceptSuggestionOnEnter": "smart",
  "editor.suggestSelection": "recentlyUsed",
  "window.dialogStyle": "custom",
  "debug.showBreakpointsInOverviewRuler": true,
}

// Code Runner相关配置根据需要自行配置
```



## VS Code快捷键

| 快捷键                               | 说明                                  | 推荐     |
| ------------------------------------ | ------------------------------------- | -------- |
| Ctrl + F                             | 当前文件内查找指定内容                | &#10004; |
| Ctrl + H                             | 当前文件内查找指定内容并替换          | &#10004; |
| Ctrl + Shift + F                     | 全局文件内查找指定内容                | &#10004; |
| Ctrl + Shift + H                     | 全局文件内查找指定内容并替换          | &#10004; |
| Ctrl + D                             | 逐个选中相同词                        | &#10004; |
| Ctrl + P                             | 查找指定文件                          | &#10004; |
| Ctrl + ,                             | 打开设置                              | &#10004; |
| Ctrl + `                             | 打开终端                              | &#10004; |
| Ctrl + Shift + E                     | 打开资源管理器                        | &#10004; |
| Ctrl + Shift + F                     | 打开搜索栏                            | &#10004; |
| Ctrl + Shift + G                     | 打开源代码管理                        |          |
| Ctrl + Shift + D                     | 打开运行和调试                        |          |
| Ctrl + Shift + X                     | 打开扩展                              |          |
| Ctrl + B                             | 显示/隐藏主侧边栏                     |          |
| 按住Alt点击鼠标                      | 任意处添加光标                        | &#10004; |
| 按住滚轮拖动                         | 同一列处快速添加光标                  | &#10004; |
| Alt + Up                             | 当前行代码上移                        | &#10004; |
| Alt + Shift + Up                     | 复制当前行并放置在上一行              | &#10004; |
| Alt + Down                           | 当前行代码下移                        | &#10004; |
| Alt + Shift + Down                   | 复制当前行并放置在下一行              | &#10004; |
| 光标在末尾时回车                     | 向下重开一行                          |          |
| 光标不在末尾时，Ctrl + Enter         | 向下重开一行                          | &#10004; |
| 光标不在末尾时，Ctrl + Shift + Enter | 向上重开一行                          |          |
| 光标没有选中内容时，Ctrl + X         | 剪切行                                | &#10004; |
| 光标选中内容时，Ctrl + X             | 剪切内容                              | &#10004; |
| Ctrl + Shift + K                     | 删除行                                |          |
| Ctrl + L                             | 选中当前行                            | &#10004; |
| Ctrl + Shift + L                     | 快速选中所有相同内容，快捷版 Ctrl + D |          |
| Ctrl + Backspace                     | 删除前一个词                          | &#10004; |
| Ctrl + Del                           | 删除后一个词                          |          |
| F12                                  | 跳转至定义                            |          |
| Ctrl + 点击鼠标左键                  | 跳转至定义                            |          |

## VS Code插件

| 名称                            | 简介                   | 备注                                                         |
| ------------------------------- | ---------------------- | ------------------------------------------------------------ |
| Prettier - Code formatter       | 格式化代码             |                                                              |
| Path Intellisense               | 路径补全               |                                                              |
| vscode-icons                    | 个性化文件图标         |                                                              |
| CodeSnap                        | 代码截图               | 编辑器内右键，“code snap”，选择需要截图的代码                |
| Live Server                     | 实时服务器             | 编辑器内右键，“open with live server”                        |
| Code Runner                     | 代码运行工具           | 支持多种语言，语言运行环境需要自己配置                       |
| Competitive Programming Helper  | 刷算法题时添加样例     |                                                              |
| IntelliCode                     | AI开发助手             |                                                              |
| GBK to UTF8 for vscode          | 转换文件的编码格式     | 左侧导航栏，文件名称上右键，选择转换                         |
| Hex Editor                      | 查看文件Hex或UTF-8编码 | 文件标签名称上右键“重新选择打开编辑器的方式”，选择Hex Editor |
| Doxygen Documentation Generator | 文档生成器             | 在函数名上方输入 /** 后回车，自动补全文档说明                |
| Remote SSH                      | 连接远程服务器或虚拟机 |                                                              |
| GitHub repositories             | 连接远程Github库       |                                                              |
| Draw.io                         | 画流程图               |                                                              |
| Vue Language  Features(Volar)   | Vue语法支持            | 支持到Vue3，Vetur只支持到Vue2，推荐使用Volar                 |
| Vue VsCode Snippets             | Vue代码片段生成        | 在.vue文件中输入vbase，选择生成需要的代码片段                |
| Vue Jumper                      | Vue组件跳转到文件定义  |                                                              |
| JavaScript (ES6) code snippets  | Js代码片段生成         |                                                              |
| HTML CSS Support                | Html CSS支持           |                                                              |
| Auto Rename Tag                 | 自动重命名标签         |                                                              |
|                                 |                        |                                                              |

