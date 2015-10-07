# Web

## 搜索提交

### Web自带默认搜索

通过网页上的菜单按钮 All>Open、All>Merged、All>Abandoned、My>Drafts、My>Watched Changes等可以快速搜索status:open、status:merged、status:abandoned、owner:self is:draft、status:open is:watched

### 表达式搜索

下面很多搜索在2.11的版本中才开始有

* age:'AGE' ： 根据最后一次修改后的时间
* before:'TIME'/until:'TIME' : 某个时间点之前的修改(TIME格式：2015-09-30[ 15:04:05[.890][ -0700]]
* after:'TIME'/since:'TIME' : 某个时间点之后的修改
* change:'ID' : 根据修改ID查询
* owner:'USER', o:'USER' ：根据提交者查询
* ownerin:'GROUP' ： 根据提交者在某个组内查询
* reviewer:'USER', r:'USER' ： 根据审核者查询
* reviewerin:'GROUP' ： 根据审核者在某个组内查询
* project:'PROJECT', p:'PROJECT' ： 根据项目查询
* projects:'PREFIX' ： 根据项目前缀查询
* branch:'BRANCH' ： 根据分支名查询
* topic:'TOPIC' ： 根据Topic查询
* tr:'ID', bug:'ID' ： 根据BugID查询
* message:'MESSAGE' ： 根据提交的注释信息查询
* comment:'TEXT' ： 根据审核的注释查询
* path:'PATH' ： 根据文件查询，正则匹配以^开头
* file:'NAME', f:'NAME'： 根据文件查询，正则匹配以^开头

### 搜索运算符

* 非操作：在搜索条件前加-
* 与操作：AND
* 或操作：OR
* 限制搜索结果条目数量： limit:'CNT'

