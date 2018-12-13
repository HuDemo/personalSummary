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

参考链接：
		https://blog.csdn.net/csdn100861/article/details/78585333		cordova入门
		
		https://segmentfault.com/a/1190000014992947			iterm2 + oh my zsh配置

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

*避免提交时乱merge的pull方法

  git pull --rebase	（ = git fetch + git rebase ）
  
  在rebase的过程中，有时也会有conflict，这时Git会停止rebase并让用户去解决冲突，解决完冲突后，用git add命令去更新这些内容，然后不用执行git-commit,直接执行git rebase --continue,这样git会继续apply余下的补丁。
在任何时候，都可以用git rebase --abort参数来终止rebase的行动，并且mywork分支会回到rebase开始前的状态。
  
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
  
四、UI兼容方面

1.ios的scroll滑动问题

在ios中表单页滑动不流畅 -> -webkit-overflow-scrolling

-webkit-overflow-scrolling造成滑动时有白屏 -> 

.form-item {

  &:not(.upload) {
  
     -webkit-transform: translateZ(0px);
     
  }
  
}

2.dialog被遮罩层罩住

添加属性 :modal-append-to-body='false'

3.滑动穿透问题

方案1：添加属性 @touchmove.prevent

方案2：若方案一无效，采取动态判断给父元素添加属性unscroll

（打开dialog时有，关闭后去掉）

        .unscroll{
	
            position: fixed;
	    
            top: calc(#{$topBackHeight} + #{$topSpace});
	    
            height: calc(100vh - #{$topBackHeight} - #{$topSpace});
	    
            overflow: hidden;
	    
        }
	
	（注意样式的优先级，要大，最重要的是overflow）

4.上方的topSpace

iOS、iPhone X、android分别不同，参考分别为20px、44px、0px

同时需配合变化的还有页面剩余部分的高度

兼容iPhone X 还要注意的一点是：如果页面底部有按钮，一定要避开最下面的感应键，或者给底部按钮足够的高度，否则很容易造成点击底部按钮唤醒感应键的问题。

五、物理返回

因为app有Android和iOS两种，所以会涉及到安卓手机的物理返回键问题。

常规来讲，用前端写的app，点击物理返回键时会返回上一页面，但是当我们在页面A跳到页面B，在页面B打开了一些弹窗来显示提示操作或者协议等等时，用户点击物理返回键是不应当返回上一页面的，而是应该关闭当前页面的弹窗，页面维持在B不动。

我个人觉得在vue项目中，可以用beforeRouteEnter钩子和localStorage搭配实现，亲测好用。

1.在弹窗出现时：
localStorage.setItem('标识名',true);

2.在弹窗被正常关闭时（例如点击其他地方关闭，点击确认按钮关闭，点击✘号关闭等）：
localStorage.removeItem('标识名');

3.钩子函数

	beforeRouteEnter(to,from,next){

		if(localStorage.getItem('标识名A')){

			next(false);

			localStorage.removeItem('标识名');

			return;

		} else if (localStorage.getItem('标识名B')){

			console.log('关闭弹窗');

			next(false);

			this.removeDialog();

			return;

		}

		next();
	
	}

这里代码是当页面有两个可弹出的弹窗时，判断localstorage里面是否有这两个标识名，如果没有，注意！一定要有正常情况下的next()，否则无弹窗的正常退回也会失效。而如果检测到有标识名，一定要有return！不然还是会走next()。

另外，鉴于用户使用app时，并不能像网页一样看到console.log，而且每当代码有改动时都要重新打包或者热更新来查看变化比较麻烦，可以在打包前在每个位置加上console.log，便于调试修改，过后酌情删掉。

六、常用的表单验证

app内经常有表单填写页面，提交时需要对数据进行校验，整理了几个常用的校验方便以后使用：

1.验证必填项

	let must = opt => {
		if (isEmptyArray(opt.val) || opt.val === '' || opt.val === null) {
			return {
				text: `“${opt.text}”为必填项！`,
				flag: false
			};
		} else {
			return {
				flag: true
			};
		}
	};

2.验证金额为两位小数且大于0

	let checkNumber = opt => {
		if (!/^(([1-9][0-9]*)|0)(\.[0-9]{1,2})?$/.test(opt.val)) {
			return {
				text: `“${opt.text}”限制为两位小数！`,
				flag: false
			};
		}else{
			return {
				flag: true
			};
		}
	};

3.验证车辆数目为正整数

	let checkInteger = opt => {
		if(!/^[1-9]+\d*$/.test(opt.val)){
			return {
				text: `“${opt.text}”限制为正整数！`,
				flag: false
			};
		}else{
			return {
				flag: true
			};
		}
	};

4.验证金额价格在指定范围内

	let checkGuaNumber = opt => {
		if (!/^([1-9]\d*|[0]{1,1})$/.test(opt.val)) {
			return {
				text: `“${opt.text}”限制为含0正整数！`,
				flag: false
			};
		} else if(opt.val < 价格边界1 || opt.val > 价格边界2) {
			return {
				text: `请修改价格在m%-n%才能发布！`,
				flag: false
			};
		}else{
			return {
				flag: true
			};
		}
	};

=》使用时

	formData: {
		product_time: {
			val: '',
			rules: must,
			text: '生产日期'
		}
  	}

七、判断机型为安卓/iPhone/iPad

	export function curEnv () {
		const userAgentInfo = navigator.userAgent.toLocaleLowerCase();
		if (userAgentInfo.indexOf('ipad') > -1) {
			return 'iPad';
		} else if (userAgentInfo.indexOf('iphone') > -1) {
			return 'iPhone';
		} else if (userAgentInfo.indexOf('android') > -1) {
			return 'android';
		} else {
			return '';
		}
	};










