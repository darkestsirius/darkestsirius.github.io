---
title: "控制台，终端和shell的区别是什么？"
date: 2021-12-12T20:29:42+08:00
draft: true
---

> 原文章链接：[What's the difference between a console, a terminal, and a shell? - Scott Hanselman's Blog](https://www.hanselman.com/blog/whats-the-difference-between-a-console-a-terminal-and-a-shell)

笔者感觉原作者写的很好，故而翻译如下

---

关于这篇文章，我看到过很多类似的问题，但这些问题本身就暗含了对一些重要术语的潜在误解。例如：

- 为什么我要使用Windows Terminal而不是PowerShell？
- 我不需要WSL（Windows Subsystem for Linux）来使用bash，我使用Cygwin。
- 我能使用带着PowerShell内核的ConEmu吗？还是说我需要使用Windows Terminal？
  
让我们先来澄清几个相关专业术语的概念

## TERMINAL（终端）

单词 Terminal 来源于 terminate，用于表示通信过程的“终端”端。过去，当你提到一个基于文本的工作环境时，你经常会听到"dumb terminal"（哑终端）这个词，在这样的环境下，你身旁的计算机只负责接受键盘输入并显示文字输出，而真正的工作发生在另一端的主机或大型计算机上。

TTY 或 "teletypewriter"（电传打字机） 是最早出现的终端。你会发现它是一个打字机而非现代更常见的电子屏幕。当你在上面打字的时候，你会在看到一张纸上文字的同时把它输入计算机。当计算机响应时，你会看到打字机自动在同一张纸上打印输出结果。

当我们在***软件意义***上提到终端时，我们指的是 TTY 或 硬件终端 的文字软件版本，也就是负责输入输出命令行的东西（例如Windows上的那个写着 cmd 的黑框框，但 cmd 并不是终端）。[Windows Terminal](https://docs.microsoft.com/zh-cn/windows/terminal/)就是其中之一。它的作用是显示文本输出，也可以接受输入并将其传递给shell。但是终端并不“聪明”，因为它实际上不会对你的输入做任何处理，也不会看你的文件或进行任何”思考“，它只负责接收输入和显示输出，负责处理你输入信息的另有其人。

## CONSOLE（控制台）

在20世纪中期，人们的起居室里会有一种家具，叫做控制台或陈列柜。而在计算机领域，控制台指的是计算机的操控台或者一个内置屏幕和键盘的机柜，但它本质上仍是一个终端（计算机领域）。从（计算机）技术上讲，控制台是硬件设备，而终端则是其内部运行的软件程序。

***在软件世界中提到控制台和终端时，它们实际上是同义词。（例如IDE中的内置终端，也叫控制台）***

## Shell（操作系统外壳，区别于操作系统内核）

之前提到的终端，会接受用户的输入并将其发送给另一个程序，这个应用程序就是Shell。Shell 会接收你通过终端/控制台输入的命令并将其"翻译"成操作系统内核可以理解的语言，然后调用相应的应用程序，最后生成输出并将其传递回终端以显示给用户。这里是一些常见的shell：

- bash, fish, zsh, ksh, sh, tsch
- PowerShell, cmd, pwsh
- yori, 4dos, command.com

其中 bash、zsh、fish 是 `*nix` 操作系统上常见的 shell，而cmd、power shell则是windows上较为常见的shell

注意：你对终端的选择不会也不应该影响你对Shell的选择，例如，理论上你可以使用Windows Terminal作为你的终端，并使用zsh作为你的Shell（这实际上不太可行，因为zsh不能很好地运行在Windows操作系统上，除非你使用WSL）

> 冷知识: WSL 和 WSL2(the Windows Subsystem for Linux)是一个或多个完整的，运行在Windows 10上的本地 Linux。这些Linux系统是完整且真实的。WSL2通过虚拟技术，提供了一个真正的 Linux 内核，并在 Windows 上运行。而Cygwin不是真正的 Linux。Cygwin 是 GNU 和开源工具的一个大集合，它们提供了类似于 Windows 上的 Linux 的功能——但它不是 Linux。这是一个幻影。它是针对 Win32 编译的 GNU utils。这很不错，但重要的是你要知道它们之间的区别。Cygwin 可以让您运行 bash shell 脚本，但它不会运行 Apache、 Docker 或其他真正的 ELF-binaries 和 Linux 应用程序。
