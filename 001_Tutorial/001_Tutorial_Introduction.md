# Tutorial

## Gerrit简要介绍

Gerrit是一个web工具，用于审核（review）Git版本控制中的的提交记录，更多的是代码审核。

传统方式中，代码审核有着不同的形式，有的是和项目主管会议审核每一行修改，有的则是代码提交前进行代码走读；
而Gerrit则是提供了一个轻量级的框架，每一个提交在真正合并到代码基线前都必须经过审核（review）。

Gerrit拥有的好处：

* 基于web更简洁方便
* 有着清晰的审核流程
* 方便异地审核协助，随时随地安排审核
* 可以基于提交进行讨论、注释

**传统基于授权的代码管理：**

![传统基于授权的代码管理](../pic/intro-quick-central-repo.png)

**基于Gerrit的代码管理：**

![基于Gerrit的代码管理](../pic/intro-quick-central-gerrit.png)

基于Gerrit的代码管理，代码提交后是进入待审核状态（Pending），只有在审核者审核之后才能进入代码仓库，也就是上图中的submit过程。
Gerrit实现了功能丰富的授权控制模型（后面有详细介绍）

## 代码提交的时间周期

以RecipeBook项目为例子，我们需要开发一个功能（也就是一次修改提交）。这里假设我们的Gerrit部署在gerrithost:8080上，ssh端口为29418。

* 首先需要的是clone项目

	`git clone ssh://gerrithost:29418/RecipeBook.git RecipeBook`

* 本地实现功能，然后本地提交（正常的git commit流程），此处提交最好有Change-Id（后面详细说明 [Change-Id commit-msg hook](gerrit/Documentation/user-changeid.html)）
* 提交修改到Gerrit

	`git push origin HEAD:refs/for/master`

	此处唯一不同的是提交到 refs/for/<branch_name>，这是Gerrit为每一个分支创建的用于管理要review的提交

* 打开 [http://gerrithost:8080/](http://gerrithost:8080/) 查看提交，查看提交注释，对比提交所做的修改，添加相关的审核人。
* 审核人进行审核。Gerrit默认的审核工作流程是需要两次check：Verification和Code-Review。Verification往往可以由自动化编译工具进行（比如Jenkins），Code-Review则必须详细的审核提交中每一行修改，同时可以双击对修改处进行评审注释。

* 提交审核结果：点击review按钮，选择审核意见，+1和-1表示初步审核结果后还需要有人进一步审核，+2和-2则表示该提交可以进入代码仓库（入库）或该提交有明显不合格不能进入代码仓库。

* 如果审核未通过，则开发者往往需要重新修改上一笔提交，此时只要 git commit --amend , 此时change-id会相同，gerrit会自动关联两笔提交，每个新的提交产生一个patch被关联在web上。

* Verification过程中我们往往需要把还未入库的提交‘检出来’，然后编译运行等操作确认提交可行，检出来的方式在web页面也可以看到，即Download Command，如：
	`git fetch http://gerrithost:8080/p/RecipeBook refs/changes/68/68/2`

上面便是一个提交过程中涉及到的一些流程，所有这些流程都可以在Gerrit中方便的处理，后面会更详细的描述每个流程中涉及到细节。