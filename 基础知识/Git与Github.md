# 本地库操作

## 本地库基本操作汇总

-   `git init` 初始化本地库
-   `git status` 查看工作区和暂存区状态
-   `git add` 工作区的变化添加到暂存区
-   `git commit -m "<message>" <file> `暂存区的内容提交到本地库
-   `git log`    `git reflog`    `git log --oneline`    `git log --pretty=oneline` 版本历史记录
-   `git reset --hard HEAD <版本索引值>` 控制版本前进后退
-   `git diff <file>`   `git diff HEAD <版本索引值> <file>`    `git diff HEAD <版本索引值>` 比较文件差异

![image-20220330173010403](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330173010403.png)

修改过的文件可以不add直接commit, 但后悔的话就不能再撤回了, 这就是暂存区存在的意义



## 本地库初始化(init)

-   命令 : `git init`
-   效果 :![image-20220330155801611](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330155801611.png)![image-20220330155819525](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330155819525-16486282751261.png)
-   注意 : `.git/`目录中存放的是本地库相关的子目录和文件, 不要随便删除或修改



## 设置(config)签名

-   形式 : 
    -   用户名 : tom
    -   Email 地址 : example@somthing.com
-   作用 : 区分不同开发人员的身份
-   注意 : 这里的签名和远程库(如GitHub)的账号密码没有任何关系
-   命令 ：
    -   项目级别/仓库级别 : 仅在当前本地库范围内有效
        -   `git config user.name tom`
        -   `git config user.email example@somthing.com`
        -   效果 : ![image-20220330162731633](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330162731633.png)
    -   用户级别 : 登录当前操作系统用户的范围内都有效  ==[推荐]==
        -   `git config --global user.name tom_glb`
        -   `git config --global user.email example_glb@somthing.com`
        -   效果 : ![image-20220330162317267](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330162317267.png)
    -   优先级 : 项目级别与用户级别都有, 则会采用项目级别的签名
    -   **通常设置一个系统用户级别的就够用了**



## 查看本地库状态(status)

-   命令 : `git status`
-   效果 : ![image-20220330163421507](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330163421507.png)



## 把工作区的文件添加(add)到暂存区

-   命令 : `git add <file>`

-   效果 : ![image-20220330163921265](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330163921265.png)

## 把暂存区(cached)的文件撤回(remove)到工作区

-   命令 : `git rm --cached <file>`
-   效果 : ![image-20220330164200861](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330164200861.png)

## 将暂存区的文件提交(commit)到本地库

-   命令 : 

    -   `git commit <file>`
    -   `git commit -m "<message>" <file>` ==[推荐]==

-   效果 : ![image-20220330165627961](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330165627961.png)

    *下面这个图是用的视频里的*

![image-20220330165521213](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330165521213.png)

## 修改工作区文件以后的status

![image-20220330170133364](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330170133364.png)

## 修改以后再次commit

![image-20220330172230102](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330172230102.png)



## 本地库的日志/历史记录(log)

-   命令 : 
    -   `git log`
        -   效果 : ![image-20220330173458813](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330173458813.png)
        -   多屏显示控制方式:
            -   空格向下翻页
            -   b向上翻页
            -   q退出
    -   `git log --pretty=oneline` 
        -   每行显示一条
        -   效果 : <img src="git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330173719529.png" alt="image-20220330173719529" style="zoom:150%;" />
    -   `git log --oneline` 
        -   每行一条, 哈希值显示更少
        -   ![image-20220330174133926](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330174133926.png)
    -   `git reflog`  ==[推荐]==
        -   每行一条, 哈希值显示更少, 增加了前进/后退到各个版本需要的步数, 对版本控制更有价值
        -   ![image-20220330174208351](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330174208351.png)
        -   HEAD@{移动到当前版本需要多少步}

## 控制版本前进后退

-   本质 :
    -   控制HEAD指针向后向前移动
    -   ![image-20220330174621802](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330174621802.png)
-   基于索引值操作 ==[推荐]==
    -   `git reset --hard <索引值> ` 
    -   索引值就是`reflog`最前面的黄色的哈希值
-   使用^符号
    -   `git reset --hard HEAD^`
    -   只能后退
    -   HEAD后面加几个^代表后退几步
-   使用~符号
    -   `git reset --hard HEAD~3`
    -   只能后退
    -   HEAD~后面的数字是几就后退几步



## reset命令的三个参数对比

-   `--soft` 
    -   仅在本地库移动HEAD指针
    -   ![demo.drawio](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/demo.drawio.png)
-   `--mixed`
    -   在本地库移动HEAD指针
    -   重置暂存区
    -   ![demo.drawio](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/demo.drawio-16486353297082.png)
-   `--hard` ==[推荐]==
    -   在本地库移动HEAD指针
    -   重置暂存区
    -   重置工作区





## 删除文件后找回

-   前提: 删除前, 文件存在时的状态提交到了本地库
-   操作: `git reset --hard <指针位置>`
    -   删除操作已经提交到本地库 : 指针位置指向历史记录
    -   删除操作尚未提交到本地库 : 指针位置使用HEAD



## 比较文件差异(diff)

-   `git diff <file>`

    -   ![image-20220330211717810](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330211717810.png)

    -   ![image-20220330211940353](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330211940353.png)
    -   **与暂存区的文件进行比较**, add以后再用diff就没差异了

-   `git diff HEAD <file>`

    -   ![image-20220330212222800](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330212222800.png)
    -   **与本地库中的文件进行比较**

-   `git diff HEAD <版本号> <file>`

    -   ![image-20220330212634837](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330212634837.png)
    -   **与本地库的某一历史版本进行比较**

-   `git diff HEAD`
    -   ![image-20220330212929305](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330212929305.png)
    -   **不带文件名即可比较所有文件**







# 分支管理操作

-   概念: 版本控制过程中, 使用多条线同时推进多个任务
-   好处: 
    -   同时并行推进多个任务, 提高开发效率
    -   各个分支开发过程中互不干扰, 失败的分支删除重新开始即可
-   ![image-20220330214435326](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330214435326.png)

## 查看所有分支

-   `git branch` 简略信息
-   `git branch -v` 详细信息

## 创建分支

-   `git branch <分支名>`

## 切换分支

-   `git checkout <分支名>`

![image-20220330215422879](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330215422879.png)

## 合并分支

假设要把hot_fix合并到master上

-   第一步: 切换到需要被修改的分支上 `git checkout master`
-   第二步: `git merge hot_fix`

![image-20220330220135616](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330220135616.png)

## 合并冲突的解决

![image-20220330221004298](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330221004298.png)

![image-20220330221355967](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220330221355967.png)

把`<<<<<<HEAD`等特殊符号去掉, 然后人为修改处理好这些冲突以后

-   第一步 : `git add <file>` 
-   第二步 : `git commit` 
    -   **注意这里不能带文件名**

即可完成merge





# 远程库操作(GitHub)

## 创建远程库

-   在GitHub新建一个repository
-   **远程库最好和本地库一个名字**, 方便自己管理

-   ![image-20220331104845932](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331104845932.png)

## 本地创建远程库的别名

-   `git remote -v` 查看所有远程地址别名
-   `git remote add <别名> <远程地址>` 
    -   别名随便起

![image-20220331105815054](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331105815054.png)

-   `git remote rename <old> <new>` 重命名别名
-    `git remote remove <name>` 删除别名





## 本地库推送(push)到远程库

-   `git push <别名> <分支名>`
    -   注: 首次推送需要填写GitHub账号密码
    -   必须指定分支



## 配置SSH连接(本地库向远程库push时遇到SSL验证的错误解决)

![image-20220331112304436](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331112304436.png)

注 : 可以用 `git config --global http.sslVerify "false"` 来关闭本地的SSL验证, 但不推荐



具体操作在[这一节](##SSH登录)

## 克隆(clone)

-   `git clone <地址>`
    -   ![image-20220331143917914](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331143917914.png)
-   效果:
    -   完整地将远程库下载到本地库
    -   创建远程地址别名
    -   初始化本地库
    -   ![image-20220331144846644](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331144846644.png)![image-20220331145247920](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331145247920.png)

## 邀请团队成员

![image-20220331145714694](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331145714694.png)

![image-20220331145828977](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331145828977.png)

![image-20220331150039841](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331150039841.png)

![image-20220331150323026](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331150323026.png)

![image-20220331150445253](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331150445253.png)



## 如何切换要使用的GitHub账号(git会记住输入过的GitHub用户名密码)

我们发现输入GitHub的账户和密码之后，下次就不用再输入了，为什么呢？ 答：对于http连接来说，Git本身没有记住账户名和密码的功能，是因为我们Win10系统有一个【凭据管理器】记住了GitHub的账户名和密码。 打开【控制面板】-->【类别】-->【小图标】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-16487104989592.png)![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-16487105101554.png)

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-16487105370216.png)

如果下次我们想切换别的GitHub账号和密码进行登录的话，就将该选项删除掉。点击【删除】选项即可。

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-16487105565788.png)

## 拉取(pull)

-   `pull` = `fetch` + `merge`

    -   ```shell
        git fetch <远程库地址别名> <远程分支名>
        ```

    -   ```shell
        git merge <远程库地址别名/远程分支名>
        ```

    -   ![image-20220331152857067](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331152857067.png)



如果修改比较简单可以直接pull, 如果为了保险可以先fetch下来确认以后再merge



-   `注：` `fetch`和`pull`属于读操作，不需要登录账号和密码！ 

-   `补：` 

    -   `git checkout orgin/master` 切换到远程仓库的主分支  

    -   `git checkout master` 切换到本地仓库的主分支



## 解决冲突

-   要点：  

    -   如果`不是基于GitHub远程库的最新版所做的修改`，不能直接推送，必须先拉取。  

    -   拉取下来后如果进入冲突状态，则按照【分支冲突的解决】操作解决即可。 

-   类比：  

    -   债权人：老王（发债的人）  

    -   债务人：小刘（欠债的人）  老王说：10天后归还。小刘接受，双方达成一致。  

    -   老王媳妇说：5天后归还。小刘不能接受。老王媳妇需要找老王确认后再执行。



## 跨团队协作

**Fork**

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880310.png)

正在Forking……请稍等！

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880411.png)

Fork成功后的截图：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880412.png)

“东方不败”将远程仓库的内容克隆到本地仓库后，进行修改，然后推送到“自己的远程仓库”。

“东方不败”将“自己的远程仓库”推送到“岳不群的远程仓库”的操作： 点击【Pull requests】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880413.png)

再点击【New pull request】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880414.png)

再点击【Create pull request】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880415.png)

填写此次修改的【标题】和【修改说明】后，再点击【Create pull request】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880416.png)

创建拉取请求成功后的页面：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880417.png)

------

岳不群这边点击【Pull requests】按钮

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880418.png)

岳不群可以查看东方不败发过来的消息

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880419.png)

消息详情如下：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880420.png)

**对话/评论**

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880521.png)

对话细节：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880522.png)

**审核代码** 经过一番唇枪舌战之后，岳不群要合并代码了，合并代码前需要先审核代码：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880523.png)

**合并代码** 岳不群审核代码没有问题，就可以进行代码合并了！点击【Conversation】

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880524.png)

填写本次操作的日志信息：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871459880525.png)

岳不群再将远程仓库的修改拉取到本地仓库，远程团队协作完成。 





## SSH登录

如果Windows系统没有凭据管理器为我们保存GitHub的账号和密码，那么我们`基于Http地址`的方式进行push操作的时候，就需要每次输入GitHub的账号和密码，比较麻烦，有没有别的方法呢？  答：答案是肯定的！使用`SSH方式`登录。缺点：这种方式只能为一个账号和密码进行设置，实际开发中，我们有一个GitHub账号已经够用了。 

-   进入当前用户的家目录
    -   `$ cd ~` 

-   删除.ssh目录 
    -   `$ rm -rvf .ssh` 

-   运行命令生成.ssh密钥目录 

    -   `$ ssh-keygen -t rsa -C zxy1079669873@gmail.com` 

    -   `注意：`这里-C这个参数是大写的C。  

    -   需要确认几个信息，我们点击几下回车即可。回车表示使用默认值。

-   进入.ssh目录查看文件列表 

    -   `$ cd .ssh` 
    -   `$ ls -lF`

-    查看id_rsa.pub文件内容

    -    `$ cat id_rsa.pub`

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871484409042.png)

-   将 SSH 公钥复制到剪贴板

    -   ```shell
        clip < ~/.ssh/id_rsa.pub
        ```

    -   也可以找到.ssh文件夹打开id_rsa.pub文件复制其中内容

-   在远程库添加公钥
    -   ![image-20220331113525811](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331113525811.png)
    -   ![image-20220331113701419](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331113701419.png)

-   测试SSH连接

    -   ```shell
        ssh -T git@github.com
        ```

    -   ![image-20220331113931832](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/image-20220331113931832.png)

-   再回到客户端Gitbash创建远程地址别名 

    -   ```shell
        git remote add <SSH链接地址别名> <SSH链接地址>
        ```

    -   注意 : **这里的远程地址是ssh链接,** 而不https

推送文件进行测试，没有问题

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871484409043.png)



# Git的工作流

## **概念**

>    在项目开发过程中使用Git的方式。 

## 分类

### **集中式工作流**

>    像SVN一样，集中式工作流以中央仓库作为项目所有修改的单点实体。所有修改都提交到Master这个分支上。 这种方式与SVN的主要区别就是开发人员有本地库。Git很多特性并没有用到。 

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871690391346.png)

### **GitFlow工作流**

>    Gitflow工作流通过为功能开发、发布准备和维护设立了独立的分支，让发布迭代过程更流畅。严格的分支模型也为大型项目提供了一些非常必要的结构。 

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871690391347.png)

### **Forking工作流**

>    Forking工作流是在GitFlow基础上，充分利用了Git的Fork和pull request的功能以达到`代码审核`的目的。更适合安全可靠地管理大团队的开发者，而且能接受不信任贡献者的提交。 

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871690391348.png)

## **详解**

### **分支种类**

-   **主干分支 master**  
    -   主要负责管理正在运行的生产环境代码。永远保持与正在运行的生产环境完全一致。
-    **开发分支 develop**  
    -   主要负责管理正在开发过程中的代码。一般情况下应该是最新的代码。 
-   **bug修理分支 hotfix**  
    -   主要负责管理生产环境下出现的紧急修复的代码。从主干分支分出，修理完毕并测试上线后，并回主干分支。并回后，视情况可以删除该分支。 
-   **准生产分支（预发布分支） release**  
    -   较大的版本上线前，会从开发分支中分出准生产分支，进行最后阶段的集成测试。该版本上线后，会合并到主干分支。生产环境运行一段阶段较稳定后可以视情况删除。
-    **功能分支 feature**  
    -   为了不影响较短周期的开发工作，一般把中长期开发模块，会从开发分支中独立出来。开发完成后会合并到开发分支。

### **GitFlow工作流举例**

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871690391449.png)

### **分支实战**

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871690391450.png)

`注：`创建分支、审查代码、合并分支这些操作都在本地做，不在远程做。而且往master上去合并代码时，一定是有经验的程序员（比如项目经理、架构师等）去合并，这样有保障！ 



# GitLab 服务器搭建过程

>    GitLab是局域网环境内的代码托管中心。 

[虚拟机CentOS 7.5 如何固定IP地址](https://www.cnblogs.com/chenmingjun/p/10048699.html)  

使用终端工具链接远程服务器：推荐使用全能终端：**MobaXterm_Personal_11.0.exe**，此乃神器中的神器！！！比SecureCRT、Xsheel要好！！！

## **官网地址**

  首页：https://about.gitlab.com/  安装说明：https://about.gitlab.com/installation/

## **安装命令摘录**

```javascript
sudo yum install -y curl policycoreutils-python openssh-server cronie
sudo lokkit -s http -s ssh
sudo yum install postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash 
sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlab-ee
```

实际问题：yum安装gitlab-ee(或ce)时，需要联网下载几百M的安装文件，非常耗时，所以应提前把所需RPM包下载并安装好。 下载地址为：

```javascript
https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-10.8.2-ce.0.el7.x86_64.rpm
```

## **调整后的安装过程**

```javascript
sudo rpm -ivh /opt/gitlab-ce-10.8.2-ce.0.el7.x86_64.rpm
sudo yum install -y curl policycoreutils-python openssh-server cronie
sudo lokkit -s http -s ssh
sudo yum install postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash 
sudo EXTERNAL_URL="http://gitlab.example.com"
```

安装过程中的截图：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871721358356.png)

当前步骤完成后重启虚拟机，命令：reboot。 

## **gitlab服务操作**

初始化配置gitlab  gitlab-ctl reconfigure 启动gitlab服务  gitlab-ctl start 查看gitlab服务运行状态  gitlab-ctl status 停止gitlab服务  gitlab-ctl stop

## **浏览器访问**

访问Linux服务器IP地址即可，如果想访问`EXTERNAL_URL`指定的域名还需要配置域名服务器nginx和本地hosts文件。 初次登录时需要为gitlab的root用户设置密码。  用户名：root  密码：root1234

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871721358357.png)

>    `注：`CenOS7需要停止防火墙服务：service firewalld stop，实际生产环境中需要设置相应的`防火墙策略`，用什么端口打开即可，不用的端口一律屏蔽；端口使用多少时间，也要设定。我们学习的时候，建议关闭虚拟机的防火墙。 

登陆成功的界面：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871721358358.png)

后续操作同GitHub上的操作，大同小异，例如：创建一个项目（仓库），点击Create a project：

![img](git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.assets/1620-164871721358359.png)

`补：`  Git远程服务器搭建：https://www.cnblogs.com/chenmingjun/p/10164296.html#_label2 

