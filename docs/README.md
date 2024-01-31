# git命令

#### **1、克隆指定分支**

**git clone -b <指定分支名> <远程仓库地址>**

示例：git clone -b bestore_master ssh://git@git-ssh.xxx.com/xxx.git

#### **2、 查看当前分支**

**git branch**

#### **3、查看所有分支**

**git branch -r 或者git branch -a**

#### **4、切换分支**

**git checkout <指定分支名>**

示例：git checkout bestore_sprint_1115

#### **5、拉取代码**

**git pull**

#### **6、添加代码到缓存**

**git add -A : 提交所有变化**

**git add -u : 提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)**

**git add .  : 提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件**

#### **7、提交代码**

**git commit -m "注释"**

#### **8、推送代码**

**git push**

#### **9、初始化：创建一个git仓库，创建之后就会在当前目录生成一个.git的文件**

**git init**

#### **10、添加文件：把文件添加到缓冲区**

**git add filename**

#### **11、删除文件**

**git rm filename**

#### **12、查看git库的状态，未提交的文件，分为两种，add过已经在缓冲区的，未add过的**

**git status**

#### **14、查看日志**

**git log**

#### **15、版本回退**

**git reset --hard HEAD^：回退到上一个版本（HEAD代表当前版本，有一个^代表上一个版本，以此类推）**

**git reset --hard d7b5：回退到指定版本(其中d7b5是想回退的指定版本号的前几位)**

#### **16、查看命令历史：查看仓库的操作历史**

**git reflog**

#### **17、增加了远程仓库abc**

**git remote add origin git://127.0.0.1/abc.git**

#### **18、移除远端仓库**

**git remote remove origin**
