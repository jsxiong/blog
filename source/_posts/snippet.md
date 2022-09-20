---
title: 使用vscode代码片段提升开发效率
date: 2022-09-20 14:25:40
tags: 效率
---

## 前言 

在日常业务开发的过程中，经常需要写一些重复的、记不清全称或懒得打全代码。

之前我都是找到以前的代码，然后再复制过来。找代码需要时间，难免影响效率，而且去其他文件复制看起来也不是很专业的样子，哈哈。  

后来有一天我发现了vscode支持配置代码片段，于是便愉快的使用起来了，搬砖也变得流畅起来了。 好了废话不多说了， 下面说说这个功能是怎么使用的吧

## 基础使用

### 1. 找到配置入口。
- 在vscode界面的左下角，点击设置，然后点击配置用户代码片段

![配置用户代码片段入口](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-bdf3cd71-1e2a-4583-b759-8239cf991f4c/bd424c3c-0819-480e-a60b-ede9a1f3974e.png)

- 或者在vscode界面按快捷键 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>， 然后输入 **snippets**

![通过搜素的方式找到代码片段配置](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-bdf3cd71-1e2a-4583-b759-8239cf991f4c/2985ab9a-6702-4f1f-a305-5c1bbacbbb55.png)


### 2. 配置代码片段

- 通过上面找到入口后，然后选择 **代码片段:配置用户代码片段**

- 然后可以选择在已有的代码片段创建新的片段，或者是创建一个新的代码片段文件（新创建的可以说全局生效，也可以是针对这个项目生效）
  
  ![选择要新增代码的文件](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-bdf3cd71-1e2a-4583-b759-8239cf991f4c/b137f90b-384d-4464-bdb6-95397efc61c2.png)

#### 配置方式如下 

  ``` json
  // 完整代码
   {
  	"element confirm": {
  		"scope": "javascript,typescript",
  		"prefix": "eledel",
  		"body": [
  			"this.$confirm(`是否确定删除?`).then(() => {",
  				"  ${1}",
  			"})"
  		],
  		"description": "确定框"
  	}
  }
  ```
##### 配置参数说明

第一个 "element confirm" 是个名称

**scope**：此代码段使用的语言名称列表

**prefix**: 调用代码片段时使用的命令

**body**: 片段内容，$1表示光标的最终所在位置。注意: 每一行都要用引号包起来

**description**: 备注，片段作用描述

### 3.使用配置好的片段

#### 直接在需要的地方调用刚刚配置的 prefix 命令

![使用代码片段](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-bdf3cd71-1e2a-4583-b759-8239cf991f4c/2e0e44c0-e423-4aa8-a50b-e7e87144478d.gif)


### 4. 一个生成片段的小工具

把代码片段放进去，然后复制到代码片段里。 主要是片段body里面每一行都加引号，手动加就比较麻烦，用这个工具直接就生成好了。

[https://snippet-generator.app](https://snippet-generator.app)

*****

END