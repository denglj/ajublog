title: git合并多个提交commits
date: 2015-11-11 16:18:35
tags:
  - git
---
阿驹的工作和个人项目都使用Git作为版本控制工具。与Subversion相比，Git更具Geek范，熟练之后感觉灵活强大。

在我们针对某个功能多次commit提交代码以后，很可能会希望将这多个commit合并为一个，再向master分支提MR（Merge Request, GitLab这样称呼；Github称为Pull Request）。
<!--more-->
例如有如下几个commit（git log命令查看）：
```
commit f7fd31b1f98246f6c2d3c71f132e753d920d7284
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:32:43 2015 +0800

    马老板亲自确定首页不用放情趣用品

commit d875ff1692afcb355c7a29887c7279b17dcfa80f
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:31:59 2015 +0800

    产品经理说还是放在醒目位置可以赚眼球

commit 63e2abd652af32b2f02e13730fb10e5c0a21532d
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:31:13 2015 +0800

    项目经理说情趣用品不必放在首页醒目位置

commit 32db1a7e119f09726732936c0dd64714680a172f
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:30:37 2015 +0800

    网站首页按双十一活动全新改版

```

你崩溃了没？这时候我们就希望把前三个commit合并为一个，向上游分支提MR的时候只有 “网站首页按双十一活动全新改版” 与合并后的那个commit， 共两个，而不是杂乱的四个。而且上面三次提交中代码的改动也都融合到这个commit中。

## 第1步：$ git rebase -i <不变动的commit的SHA-1>
`$ git rebase -i 32db1a7e119f09726732936c0dd64714680a172f`

按Enter键后进入编辑状态。

## 第2步：squash 或 fixup
进入编辑状态后，看到下面的视图。
```
pick 63e2abd 项目经理说情趣用品不必放在首页醒目位置
pick d875ff1 产品经理说还是放在醒目位置可以赚眼球
pick f7fd31b 马老板亲自确定首页不用放情趣用品

# Rebase 32db1a7..f7fd31b onto 32db1a7
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

然后将`d875ff1` 与 `d875ff1` (后两行commit) 前面的 `pick` 改为 `squash` 或者是 `fixup`，这里我们使用 `squash` 。

`fixup`会自动将当前commit与**前一个**合并，并放弃当前commit的注释等内容，退出当前编辑状态就结束操作了。而使用 `squash` 当退出当前编辑状态后，会进入新的编辑状态，允许我们再次编辑注释等提交信息，再次退出才结束操作。

然后输入`:wq` 退出当前编辑状态（Vim编辑器的操作）。

## 第3步：填写新的commit信息
紧接着上一步后，会进入如下视图：
```
# This is a combination of 3 commits.
# The first commit's message is:
项目经理说情趣用品不必放在首页醒目位置

# This is the 2nd commit message:

产品经理说还是放在醒目位置可以赚眼球

# This is the 3rd commit message:

马老板亲自确定首页不用放情趣用品

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Wed Nov 11 16:31:13 2015 +0800
#
# rebase in progress; onto 32db1a7
# You are currently editing a commit while rebasing branch 'master' on '32db1a7'.
#
# Changes to be committed:
#       modified:   index.html
```
我们可以删除里面的所有内容（或者只删除 # Please enter之前的所有内容），在第一行留下一句提交注释 `马老板亲自拍板首页不放情趣用品` 。然后 `:wq` 退出，合并就完毕了。

然后我们再用 `git log` 查看，只剩下两个commit了。
```
commit 47c72892ee29dc51a8a55345fd4836893dfe12fb
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:31:13 2015 +0800

    马老板亲自拍板首页不放情趣用品

commit 32db1a7e119f09726732936c0dd64714680a172f
Author: aju <aju@ajucs.com>
Date:   Wed Nov 11 16:30:37 2015 +0800

    网站首页按双十一活动全新改版
```

### 参考资料
[如何合併多個commits](http://zerodie.github.io/blog/2012/01/19/git-rebase-i/)
