# github创建远程仓库以及和本地仓库关联

##### 1、首先在github网站上面创建一个版本库（注意需要创建一个readme.txt文件或者其他文件，否则空仓库没有master分支）



##### 2、其次将本地git的公钥填进官网的add key当中作为身份校验（廖雪峰见，不细说）



##### 3、本地创建一个文件夹（本地仓库，名字例如repo），在repo目录下输入 :

` git init `

##### 这样代表我们创建了一个本地仓库，文件里面生成一个.git文件  用来记录我们所做的改变



##### 4、然后我们需要将本地仓库和远程仓库进行关联，输入该命令

```git remote add origin git@github.com:michaelliao/learngit.git```



##### 5、尝试将本地仓库的内容推送远程仓库

```git push -u origin master```

如果出现错误 尝试输入命令：

```git pull --rebase origin master```该命令相当于pull=fetch+merge



##### 6、然后尝试查看一下是否和远程分支关联上

![](../img\gitbranch.jpg)



出现如图所示，则说明关联成功，可以进行操作了





#### 第二种方式（摘自一个博客）

`touch README.md` 
`git init` 
`git add README.md` 
`git commit -m “first commit”` 
`git remote add origin https://github.com/zxy987872674/LearnCode.git` 
`git push -u origin master`





 





