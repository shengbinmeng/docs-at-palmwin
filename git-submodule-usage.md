# Git子模块的使用

* 使用者仓库：client、server；
* 子模块仓库：BizCommon、BaseCommon；
* 指针：使用者仓库中储存的hash值，指向子模块仓库中的某个提交。

对于多个开发者需要集成共享的分支develop作如下要求（“develop”换成“master”同样适用）：

1. 使用者仓库的develop分支中的指针，只能指向子模块仓库的develop分支上的提交。这意味着：如果使用者仓库所依赖的子模块的某个提交不在该子模块仓库的develop分支上，需要先在该子模块仓库中把该提交合并到develop上。

2. 使用者仓库的develop分支中的指针的改动，如无特殊情况只能向前移，也就是改为指向相应子模块仓库中develop分支上较新的提交。

3. 使用者仓库的develop分支执行`git submodule update`后，应该能正常编译运行，并尽量测试无问题。

开发者在向远端仓库的develop分支push之前需要检查并确认符合上述要求。

其他分支不做要求。