---
layout: post
title: "Emacs org模式的简单GTD流程"
uid: 5
---

Emacs org模式的简单GTD流程
===============================

## intro
最近转而使用emacs，虽然用来写代码也有几个星期的时间，但是emacs的很多功能都还没用起来，正好需要提高效率，所以尝试使用org模式的todo和agenda来进行GTD的管理。

## 同步
因为毕竟没有云笔记或者其他GTD软件一样的同步功能，所以采用金山快盘作为同步工具。并将文件添加到agenda文件列表。

```lisp
    (setq org-agenda-files (list "e:/快盘/GTD/task.org"))
```

这样子，只要启动org-agenda，就能自动根据添加进来的文件，生成org-agenda-view，效果非常漂亮。

## TODO
添加TODO非常简单，可以使用org模式的分层结构，比如添加以下TODO：

    * Collection
    ** TODO Wash clothes

在TODO行上，可以有很多TODO相关的命令可以执行

    C-c C-t     切换TODO状态，循环选择
    C-u C-c C-t 切换TODO状态，手工选择
    C-c C-c     添加Tag
    C-c C-s     添加Schedule
    C-c C-d     添加Deadline
    C-c .       添加时间戳
    C-u C-c .   添加准确时间的时间戳
    C-c C-w     移动到不同父节点

在TODO行上，增加[%]，可以显示子节点的完成状态,对于项目任务非常有用

TODO状态可以通过以下配置设置，这里我根据我个人需要进行了分类：

```lisp
    (setq org-todo-keywords '((type "TODO" "STARTED" "WAITING" "|" "DONE" "CANCELLED")))
```

20140801增加自动标记任务完成时间和提示记录任务完成note

```lisp
    (setq org-log-done 'time)
    (setq org-log-done 'note)
```

## 时间戳 （20140805添加 ）
最普通的时间戳和默认的 Schedule和Deadline的时间戳都如下：

    <2014-07-31 周四>

带准确时间的时间戳如下, 设置了准确时间之后，agenda视图中才能更好的展示任务的时间序列

    <2014-07-31 周四 22:30>

对时间戳做一些调整，我们就可以添加重复任务，比如说每日训练的时间什么的，就都可以实现了

    <2014-07-31 周四 22:30 .+1d>

上面的时间戳表示上面的任务每天重复。当你用C-c C-t标记完成时候，会自动记录完成信息，并且时间戳会被更改以便标记下一次任务。


## agenda
目前我对agenda的配置只是添加了快捷键

```lisp
    (setq org-todo-keywords '((type "TODO" "STARTED" "WAITING" "|" "DONE" "CANCELLED")))
```

TODO中的大部分命令，在agenda视图中，也可以执行

## GTD流程
目前的简单流程如下：

* 收集任务，并放入收集箱父节点
* 定时为任务增加schedule和改变TODO状态
* 根据schedule完成任务

## 备注
由于学习和实践时间短暂，流程比较简单，并且执行有点繁琐，将在后续学习中不断改善，并更新此文档。
