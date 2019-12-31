---
layout: post
title: "git cherry-pick导致的预期之外的冲突"
categories: git cherry-pick merge
---

在使用git开发时，同事合并分支时遇到了一个奇怪的冲突，不是自己修改的文件，并且此前已确认源分支并没用多于目标分支的修改。

问题的原因后来发现是cherry-pick和merge的混用导致。

<!--more-->

![pic1](/assets/images/image2019-12-31-post1.png)

发生冲突的操作如下

1. 从master拉出分支Branch1开发

2. 将master中的commit通过cherry-pick提交到Branch1上

3. master上有对cherry-pick部分新的修改

4. 修改Branch1上不同部分并提交，然后merge进master

冲突的原因是cherry-pick产生的commit并不会与原commit建立关联，此时master上新修改不会认为是一次递进提交，在merge操作时就会根据内容进行冲突判定。

所以如有些地方提到过，不要cherry-pick到一个会往回merge的分支。一般cherry-pick用于将release上的hotfix合入master比较合适，对于需要维护多个release的项目而言。


