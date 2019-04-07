---
title: cherry-pick
date: 2019-04-07
---

又是一个月过去了。
🤔🤔

<!--more-->
## 前言
在日常的开发工作中，大多数公司都是基于git的`work flow`概念，通常在一个项目中会存在着大量的分支，除了禁止修改的master分支，会包括各种各样的feature分支，dev分支，test分支等等。
每开发一次需求，就切出一个分支来进行coding。
一般我们都是基于master，`git checkout -b`切出一个新分支，在此分支之上进行开发以及commit操作。但是工作中有时候真的太过于忙碌，以至于经常忘记事先先检查开发分支，而直接开发后进行错误的commit操作。

在以前经历过不小心在错误的分支上提交commit之后，当时采取的办法只能把自己每一行的改动复制下来挪到新分支上，同时在旧分支进行`git reset HEAD~1 --hard`的操作来强行回退commit，但终究这是个很麻烦的方法。

所以基于这个需要，去了解`cherry-pick`这个操作。🤔
在平常的开发工作中，可能会出现多个开发人员的开发分支需要同一个commit修改的情况，甚至之后要是万一出现了和我一样的失误，也不至于一行行的把代码复制过去。☹️


## cherry-pick
`cherry-pick`，可以译为摘樱桃，意思为我们挑选出我们所需要的commit来单独进行操作，前提条件是你想要check的更改已经commit。
具体做法是，切换到你想要的分支b，使用`git cherry-pick commit-id`，你会发现b分支的顶部已经是你刚才所做的commit

举个栗子，我在master分支的基础上`checkout`了一个`testA`分支，同时在`master`分支上提交了我的`commit`
`checkout`到`testA`分支，`cherry-pick`刚才的`commit`，如下：
![text](/imgs/cherry1.jpg)
![text](/imgs/cherry2.jpg)
![text](/imgs/cherry3.jpg)

撒花撒花✿✿ヽ(°▽°)ノ✿🤔🤔

每次的`cherry-pick`出来的`commit`其实都是一个新的`commit`。会拥有不同的commit id
如果想要他们保持同一个commit id， 可以使用`git cherry-pick -x <commit id>`

`cherry-pick`也会存在冲突，需要手动解决冲突后重新commit

批量cherry-pick：
    `git cherry-pick <start-commit-id>..<end-commit-id>`
要注意此种方式不会包括第一个start，如果需要包含的话需要多加一个^符号：
    `git cherry-pick <start-commit-id>^..<end-commit-id>`
如果想cherry-pick之后重新编辑commit信息， 可使用`git cherry-pick -e <commit id>`

所有的commitid都可以使用前六位表示

<font face="黑体" size="2px" color="#a6a6a6">我好像太懒了点最近</font>
