# personalSummary
对前端用cordova开发app的一些小总结

新公司采用gitlab基于cordova，通过前端进行需求开发，再由原生慢慢开发进行替换，因为之前没有接触过git、cordova和app开发，所以总结一下方便回顾

一、环境
1.安装：iterm/oh my zsh/cordova/cordova热更新cli/xcode
2.git clone 项目
  进入对应文件夹 yarn install
  yarn run build
  cordova platforms ls
  cordova platform add ios
  cordova platform add android
3.安卓记得安装JDK android studio并配置

二、git常用命令
提交系列：
  gst 查看改动文件
  git diff [src]  查看具体文件的变动
  gaa [src] 把修改的文件进行移动以提交，不想提交的不要gaa
  gcmsg ‘提交信息’ （gaa后如果有不想提交的差异文件，一定要gcmsg后再git stash 不然会丢失）
  git stash 清空未提交的改动，stash后修改的内容会保存，可以恢复
  
  git fetch -p 获取修剪后的远程分支（如果要提交的分支本地还没有）
  git branch -a 查看分支
  git pull origin [远程分支名] 拉取远程文件的修改 （！！一定要在提交前拉取，不然会把别人的代码弄丢失！！）
  git push origin [远程分支名] 把本地修改的（存在gcmsg里）推送到远程分支

新建本地分支
  git checkout -b 要创建的本地分支名 origin/远程分支名
新建远程分支
  git push origin 本地分支名:远程分支名
  
删除本地分支
  git branch -D 要删除的分支名 
删除远程分支
  git push origin --delete 远程分支名
  
三、模拟调试
1.iOS
  打开.xcworkspace，会默认用xcode打开
  找iOS原生开发的同事，配置xcode证书，配置成功后除非删除项目，即使remove platform后再 add platform，也不需要重新配置证书，但是一些选项的修改需要重新设置。
  （1）Product -> Archive -> 打包 -> iTools安装
  （2）或 点击小三角在手机上运行（保持iphone解锁状态）
  （3）手机上 设置-> Safari浏览器 -> 高级 -> JavaScript和Web检查器均打开
  （4）电脑上 Safari浏览器 -> 开发，选择相应的设备和项目进行调试
2.Android
  cordova build android 生成apk包
  cordova run android 在手机上运行










  
