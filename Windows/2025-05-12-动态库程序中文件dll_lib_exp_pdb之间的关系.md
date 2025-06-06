---
category: Windows
date: 2025-05-12 09:00:00 +0800
layout: post
tag: Windows
title: Windows下动态库程序中文件dll,lib,exp,pdb之间的关系
---
## 简介

+ 在windows下C++开发，常常遇到dll,lib,exp,pdb文件，而不清楚文件的作用。在此学习和记录这些文件的作用。

<!--more-->

## DLL(Dynamic Link Library)

+ 功能：
  + 包含了可在运行时由多个程序共享的代码和数据。实际执行的动态链接库，提供了导出函数和变量供其他程序调用，实现了代码的共享与复用，减少了内存占用。
+ 与其他文件关系：
  + 是动态库的核心执行体。
  + lib 文件用于在链接阶段帮助程序找到 dll 中导出的函数和变量；
  + exp 文件是生成 dll 时产生的，记录了 dll 导出符号等信息，对生成 dll 及其对应的 lib 有重要作用；
  + pdb 文件主要用于调试 dll 代码，在运行时若程序出现问题，可借助 pdb 进行调试定位。

## LIB(Import Library)

+ 功能：
  + 导入库文件，在链接阶段使用。
  + 它包含了一些信息，帮助链接器解析对 dll 中函数和变量的引用。
  + 它不包含实际的代码实现，而是提供了一种映射关系，告诉链接器到哪个 dll 中去寻找特定的函数或变量。
+ 与其他文件关系：
  + 与 dll 紧密相关，是为了方便程序链接 dll 而生成的。
  + 它通常由 dll 的开发者提供，与 dll 一同发布。
  + lib 文件的生成依赖于 exp 文件，exp 文件中记录的导出符号等信息用于生成 lib 文件。在调试时，pdb 文件与 lib 文件配合，帮助开发人员理解程序中对 dll 的调用关系及调试相关问题。

## EXP(Export File)

+ 功能：
  + 导出文件，在生成 dll 时产生。
  + 它记录了 dll 中所有导出函数和变量的符号信息，包括函数名、序号等。这些信息对于生成 dll 对应的 lib 文件至关重要。
+ 与其他文件关系：
  + 是生成 dll 和 lib 文件过程中的中间产物。
  + linker 根据 exp 文件中的导出符号信息来生成 dll 以及对应的 lib 文件。
  + exp 文件不直接参与程序运行时的调用，但它对 dll 和 lib 文件的生成起到关键作用。

## PDB(Program Database)

+ 功能：
  + 程序数据库文件，主要用于调试。
  + 它包含了调试信息，如变量名、函数名、行号等，这些信息将可执行文件（包括 dll）中的地址映射回原始的源代码，方便开发人员在调试时定位问题。
+ 与其他文件关系：
  + 与 dll 紧密相关，在调试 dll 代码时，调试器需要 pdb 文件来将 dll 中的二进制指令与源代码关联起来。
  + 在链接阶段，pdb 文件与 lib 文件等配合，虽然 pdb 文件不直接影响链接过程，但在调试通过链接生成的可执行程序（调用 dll）时，pdb 文件提供了重要的调试辅助信息。