---
title: VsCode配置LaTeX环境
date: 2021-06-30 23:10:52
categories: 码农笔记
tags:
  - VsCode
  - LaTeX
toc: true
---

## **0，前言**

其实这类的经验帖一大把，但总是有些问题，本来我是懒得写了的，为了自己下次别再踩坑就勤快一次吧。

LaTeX的学习成本还是蛮高的，不过确实是水论文利器，然后颜值就是战斗力，所以必须上VsCode，不接受反驳。

<!--more-->

## **1，安装 texlive, VsCode, SumatraPDF(推荐)**

软件都是直接搜，官网下载，按提示安装就行。（实在有点懵就搜下安装教程）

texlive包含巨多的宏包，文件多所以安装需要2个小时左右，但是一劳永逸。

## **2，VsCode安装Latex Workshop插件**

直接在插件栏搜索```Latex Workshop```安装。

### **配置settings.json文件**

在VsCode中搜索打开```settings.json```，下面是我的设置，添加到大括号里，注意看注释部分，pdf.viewer和正反搜索要改成自己的VsCode和SumatraPDF路径。（配置部分可以参考下这里：[使用VSCode编写LaTeX](https://zhuanlan.zhihu.com/p/38178015)）

```powershell
// latex
"latex-workshop.latex.tools": [
{
    // 编译工具和命令
    "name": "pdflatex",
    "command": "pdflatex",
    "args": [
    "-synctex=1",
    "-interaction=nonstopmode",
    "-file-line-error",
    "%DOCFILE%"
    ]
},
{
    "name": "xelatex",
    "command": "xelatex",
    "args": [
    "-synctex=1",
    "-interaction=nonstopmode",
    "-file-line-error",
    "-pdf",
    "%DOCFILE%"
    ]
},
{
    "name": "bibtex",
    "command": "bibtex",
    "args": [
    "%DOCFILE%"
    ]
}
],
"latex-workshop.latex.recipes": [
{
    "name": "xelatex",
    "tools": [
    "xelatex"
    ],
},
{
    "name": "pdflatex",
    "tools": [
    "pdflatex"
    ]
},
{
    "name": "xe->bib->xe->xe",
    "tools": [
    "xelatex",
    "bibtex",
    "xelatex",
    "xelatex"
    ]
},
{
    "name": "pdf->bib->pdf->pdf",
    "tools": [
    "pdflatex",
    "bibtex",
    "pdflatex",
    "pdflatex"
    ]
}
],

// pdf.viewer
// "latex-workshop.view.pdf.viewer": "tab",
"latex-workshop.view.pdf.viewer": "external",
"latex-workshop.view.pdf.external.viewer.command": "C:/Users/xiaomi/AppData/Local/SumatraPDF/SumatraPDF.exe",
// 正反搜索
"latex-workshop.view.pdf.external.synctex.command": "C:/Users/xiaomi/AppData/Local/SumatraPDF/SumatraPDF.exe",
"latex-workshop.view.pdf.external.synctex.args": [
    "-forward-search",
    "%TEX%",
    "%LINE%",
    "-reuse-instance",
    "-inverse-search",
    "\"D:/Program Files/Microsoft VS Code\" \"D:/Program Files/Microsoft VS Code/resources/app/out/cli.js\" -r -g %f:%l",
    "%PDF%"
],
```

## **3，LaTex的使用**

推荐：

[在线LaTeX编辑器](https://www.overleaf.com)

[一份不太简短的LaTeX介绍](https://github.com/CTeX-org/lshort-zh-cn)

使用的时候要注意，先新建一个文件夹，再在文件夹中新建```xxx.tex```文件。（编译的时候会生成很多文件，都放一起谁能接受）

![文件管理](https://wx1.sinaimg.cn/mw2000/0069EgM5gy1gs0qvpftrjj30dz0hi0t7.jpg)

之后再来补充些，先这样吧，祝我水论文快乐。
