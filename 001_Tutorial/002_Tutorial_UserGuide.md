# Tutorial

## Gerrit用户指南

前提：使用者要熟悉Git的基本命令和工作流程。
Gerrit的核心功能就是提供 “提交审核” 功能。

### 工具

Gerrit是基于web的，不需要安装客户端，但下面的客户端仍然可以帮助使用：

* Mylyn Gerrit Connector: Gerrit整合Mylyn
* mGerrit：Android Gerrit客户端
* Gertty： Gerrit命令行接口

### 提交修改&提交Patch

修改必须提交到refs/for/<branch-name>，Gerrit会把这些提交保存在“refs/changes/XX/YYYY/ZZ”命令空间中用于审核。

YYYY是修改提交数，XX是修改提交数的最后两位，ZZ是patch数。

因此获取对应的未审核入库的提交是：

`git fetch https://gerrithost/myProject refs/changes/XX/YYYY/ZZ && git checkout FETCH_HEAD`

Change-Id是Gerrit非常重要的一个概念：一个Change-Id关联一次修改，而一次修改可能有多次提交（多个patch），只有最后一个审核通过的patch能过入库（进入代码仓库）。

Change-Id可以通过一个git的commit-msg钩子自动生成（后面详解）

### 多功能同步开发

由于提交审核需要时间，所以比如：如果B功能和A功能是独立的，但B功能的开发是在A功能提交还未审核的基础上进行开发，那么A功能的审核可能会延误B功能的审核，因此建议独立的功能可以并行的基于项目仓库中已经入库的基础上进行并行开发。

### 添加审核人

每一次修改提交都应该添加相应的审核人，一般是该项目比较资深或熟悉的人。当然也可以通过插件自动添加审核人 [plugins which can add reviewers automatically](gerrit/Documentation/intro-project-owner.html#reviewers)

### Dashboards看板

用于扩展 （后续补充）

### 提交入库 

审核人审核通过后入库需要Submit权限

### 入库冲突

在并行开发或多人协同开发时，审核提交时往往可能出现冲突，此时需要通过rebase来解决：

    // update the remote tracking branches
    $ git fetch
    
    // fetch and checkout the change
    // (checkout command copied from change screen)
    $ git fetch https://gerrithost/myProject refs/changes/74/67374/2 && git checkout FETCH_HEAD
    
    // do the rebase
    $ git rebase origin/master
    
    // resolve conflicts if needed and stage the conflict resolution
    ...
    $ git add <path-of-file-with-conflicts-resolved>
    
    // continue the rebase
    $ git rebase --continue
    
    // push the commit with the conflict resolution as new patch set
    $ git push origin HEAD:refs/for/master

 还有一种可能需要rebase的情况是，如果某个提交review需要长时间，而最新的提交已经可能和正在review的提交差异比较大，此时则应该rebase到最新的提交上再次review （如果没有冲突是直接可以在Gerrit Web上进行rebase)

### 使用Topic

在实现一个功能的时候，往往需要多个提交，甚至可能跨项目，此时可以通过topic对这些提交进行归组，同时可以方便将来通过topic进行搜索，提交方式如下：

`$ git push origin HEAD:refs/for/<target-branch>%topic=multi-master`

### 使用Drafts

在功能未完全实现或仍需要进一步验证时可以先提交到Draft状态，这样只有提交自己可以看到
 
`git push origin HEAD:refs/drafts/<target-branch>`

### 在线编辑 （Inline Edit)

快速的在线进行少量修改并进行提交



