---
title: 浏览器结构
date: 2019-03-29 02:02:01
tags: 浏览器
---

简单来说浏览器可以分为两个部分，shell+内核，其中shell的种类比较多，内核的种类则比较少。

Shell是指浏览器外壳：菜单、工具栏等；主要提供给用户操作，参数设置等，调用内核实现各种功能。

内核是浏览器的核心，基于标记语言显示内容的程序或模块。可分两部分，渲染引擎和JS引擎，负责渲染网页内容，计算网页的显示方式，不同内核的渲染效果不同。JS引擎则执行javascript实现网页的动态效果。

<!-- more -->

### 组件

浏览器一般由以下组件构成：
1.用户界面：包括地址栏、前进/后退按钮、书签菜单等。
2.浏览器引擎：在用户界面和呈现引擎之间传送指令。
3.呈现引擎：负责显示请求的内容，解析html和css内容，显示在屏幕上、
4.网络：用于网络调用，比如HTTP请求，其接口与平台无关，并为所有平台提供底层实现。
5.用户界面后端：绘制基本的窗口小部件，比如组合框和窗口。 其公开了与平台无关的通用接口，而在底层使用操作系统的用户界面方法。
6.JavaScript解析器：解析执行JavaScript代码。
7.数据存储：持久层。实现了Cookie，web Stroage、indexDB的功能支持。

Chrome 浏览器的每个标签页都分别对应一个呈现引擎实例。每个标签页都是一个独立的进程。