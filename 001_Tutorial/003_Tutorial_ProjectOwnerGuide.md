# Tutorial

## Gerrit项目管理员指南

### 项目管理员角色简要说明

项目管理员非配置管理员。

项目管理员拥有所属项目的所有权限，也就是拥有配置项目和refs/*的所有权限列表。

作为一个项目管理员应该要了解Git和Gerrit的相关知识。

### 编辑权限列表

Gerrit上一个Git项目的权限配置是保存在该项目的refs/meta/config分支中，在这个分支上有个文件project.config包含了相关配置项，project.config文件的存储格式可以参考章节 [Project Configuration File Format](gerrit/Documentation/config-project-config.html) , 查看该文件的历史记录可以知悉权限变更的原因，所以每次修改权限时最好加上注释。

安装了GitWeb后可以直接在网页上查看project.config的提交历史，如果想本地查看，则需要如下： 
    
    $ git fetch origin refs/meta/config:config
    $ git checkout config
    $ git log project.config

### 权限继承

Gerrit上的Git项目默认继承‘All-Projects’的权限，每个Git项目只能单继承一个‘父项目’的权限。

继承过来的权限能够通过 [BLOCK rule](gerrit/Documentation/access-control.html#block) 进行重写。BLOCK rule往往用于项目管理员限制继承过来的权限。当然如果继承过来的就是BLOCK rule规则的，那么对应相冲突的配置将无效。如果管理的多个项目的权限配置一致，则可以考虑联系配置管理员建立一个统一的父权限项目。

### References

Gerrit所有的权限都是对references进行配置，如普通的分支refs/heads/、tags分支refs/tags/，当然还有些特殊的refs（比如refs/changes/*、refs/meta/config、refs/meta/dashboards/*、refs/notes/review、refs/for/<branch ref>、refs/publish/*、refs/drafts/*），references也可以通过正则表达式表明，所有以正则表达式表明的都必须以^开头，如^refs/heads/rel-.*

### 分组(Groups）

Gerrit所有的权限都是配置给Group的。要想通过 Groups > Create New Group，则需要"Create Group"的权限。

Gerrit拥有一些系统默认分组（Anonymous Users、Change Owners、Project Owners、Registered Users）。

如果要使用外部分组则应该添加前缀，如LDAP分组则应该添加前缀ldap/，如果安装了[singleusergroup](https://gerrit-review.googlesource.com/#/admin/projects/plugins/singleusergroup),则可以通过user/或userid/直接将个人当做分组进行配置。

### Code Review

提交权限： 需配置对应分支refs/for/<branch-name>的Push权限；

入库策略： 可以通过配置项目的submit type配置入库为merge或rebase策略；

特殊情况下不想进行code review工作流程则可以通过类似如下方式（后面加%submit）：

	git push ssh://john.doe@git.example.com:29418/kernel/common HEAD:refs/for/master%submit

### Submit Type（入库策略）

* Fast Forward Only : 所有的提交必须手动rebase到最新的提交上，开发体验会差一些，建议仅用于只有少量提交的项目，或需要自动化入库的项目（减少分支入库冲突的可能）
* Merge If Necessary : 开发体验好一些，但可能破坏目标分支，比如某个提交将某个文件移动到了新目录，而另外一个提交仍然在用旧的位置。
* 可以通过Prolog来配置一个项目不同分支的入库策略，如开发分支权限放开一些，而稳定分支则应该限制严格一些，Prolog的写法是在refs/meta/config中配置reles.pl，具体的配置参考 [Prolog cookbook](gerrit/Documentation/prolog-cookbook.html#HowToWriteSubmitRules)。

### Submit Rules (入库规则）

Gerrit通过评分规则（即Label Code-Review）来确定是否可以入库。

Submit Rules也可以通过Prolog来进行配置。

### CI系统集成

比如可以通过[Gerrit Trigger](https://wiki.jenkins-ci.org/display/JENKINS/Gerrit+Trigger)或[Zuul](http://www.mediawiki.org/wiki/Continuous_integration/Zuul) 来和Jenkins集成。

和Jenkins集成需要创建一个账号，可以通过ssh命令create-account来创建。如果安装了[serviceuser](https://gerrit-review.googlesource.com/#/admin/projects/plugins/serviceuser)则可以直接在web上创建。

自动化集成往往需要以下权限：

* read权限用于获取项目进行编译；
* Verified，用于自动评分；
* stream events，用于监听Gerrit上的提交；

### 提交校验

* [uploadvalidator](https://gerrit-review.googlesource.com/#/admin/projects/plugins/uploadvalidator)插件可以配置相关commit检验规则
* [commit-message-length-validator](https://gerrit-review.googlesource.com/#/admin/projects/plugins/commit-message-length-validator)校验注释信息长度

### Bug系统集成

* [Comment Links](gerrit/Documentation/intro-project-owner.html#comment-links)：提取commit注释消息footers中的IDs来链接Bug跟踪系统，可以通过refs/meta/config分支中的project.config文件进行配置；
* Tracking IDs : 匹配Bug ID来链接；
* Bug跟踪系统插件：常见Bug跟踪系统都有相关插件[Jira](https://gerrit-review.googlesource.com/#/admin/projects/plugins/its-jira), [Bugzilla](https://gerrit-review.googlesource.com/#/admin/projects/plugins/its-bugzilla) and [IBM Rational Team Concert](https://gerrit-review.googlesource.com/#/admin/projects/plugins/its-rtc);

### 导入已有git项目

如果需要删除已有git项目中的大文件可以通过git filter-branch命令：

    git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch path/to/large-file.jar' -- --all

