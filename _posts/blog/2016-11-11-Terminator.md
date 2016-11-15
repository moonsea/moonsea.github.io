---
layout: post
title: Terminator
category: blog
description: Linux平台下一款超赞的终端
---

## Introduction

Terminator是Linuxi平台下的一款终端工具，支持横向分屏、纵向分屏以及快捷键复制、粘贴等功能，可以通过快捷键或者鼠标拖拽改变终端分屏窗口的大小.

## Install

### Ubuntu


    sudo apt-get install terminator

### Redhat

    sudo yum install terminator

## Hotkey

    Ctrl+Shift+O
        Split terminals Horizontally.（上下开新窗口）
    Ctrl+Shift+E
        Split terminals Vertically.（垂直开新窗口）
    Ctrl+Shift+Right
        Move parent dragbar Right.（放大当前窗口 向右）
    Ctrl+Shift+Left
        Move parent dragbar Left.(放大当前窗口 向左)
    Ctrl+Shift+Up
        Move parent dragbar Up.(放大当前窗口 向上)
    Ctrl+Shift+Down
        Move parent dragbar Down.(放大当前窗口 向下)
    Ctrl+Shift+S
        Hide/Show Scrollbar.（隐藏滚动条）
    Ctrl+Shift+F
        Search within terminal scrollback.(当前屏幕内容内查找)
    Ctrl+Shift+N or Ctrl+Tab
        Move to next terminal within the same tab.(切换到当前Tab页下一个窗口)
    Ctrl+Shift+P or Ctrl+Shift+Tab
        Move to previous terminal within the same tab.(切换到当前Tab页上一个窗口)
    Alt+Up
        Move to the terminal above the current one.（切换到当前窗口的上方窗口）
    Alt+Down
        Move to the terminal below the current one.(切换到当前窗口的下方窗口)
    Alt+Left
        Move to the terminal left of the current one.(切换到当前窗口的左侧窗口)
    Alt+Right
        Move to the terminal right of the current one.(切换到当前窗口的右侧窗口)
    Ctrl+Shift+C
        Copy selected text to clipboard.(复制选中内容)
    Ctrl+Shift+V
        Paste clipboard text.(粘贴)
    Ctrl+Shift+W
        Close the current terminal.(关闭当前窗口)
    Ctrl+Shift+Q
        Quits Terminator.(关闭终端)
    Ctrl+Shift+X
        Toggle between showing all terminals and only showing the current one (maximise).（最大化当前窗口）
    Ctrl+Shift+Z
        Toggle between showing all terminals and only showing a scaled version of the current one (zoom).(还原当前窗口)
    Ctrl+Shift+T
        Open new tab.(新建标签页)
    Ctrl+Shift+Alt+T
        Open new tab at root level, if using extreme_tabs.(新建终端窗口)
    Ctrl+PageDown
        Move to next Tab.(切换到下一个标签)
    Ctrl+PageUp
        Move to previous Tab.(切换到上一个标签)
    Ctrl+Shift+PageDown
        Swap tab position with next Tab.
    Ctrl+Shift+PageUp
        Swap tab position with previous Tab
    Ctrl+Shift+F
        Open buffer search bar to find substrings in the scrollback buffer. Hit Escape to cancel.(窗口全部内容中查找)
    Ctrl+Plus (+)
        Increase font size. Note: this may require you to press shift, depending on your keyboard.(放大字体)
    Ctrl+Minus (-)
        Decrease font size. Note: this may require you to press shift, depending on your keyboard.(缩小字体)
    Ctrl+Zero (0)
        Restore font size to original setting.(回复字体初始设置)
    F11
        Toggle fullscreen.（全局）
    Ctrl+Shift+R
        Reset terminal state.(重置终端)
    Ctrl+Shift+G
        Reset terminal state and clear window.(重置终端并清除窗口内容)

## References

\[1\] [terminator 的常用快捷键][1]  
\[2\] [使用Terminator增强你的终端][2]

[1]: http://www.cnblogs.com/xiazh/articles/2407328.html
[2]: http://blog.wentong.me/2014/05/work-with-terminator/
