# 搭建个人博客
首先安装好```node```，网站是 [nodejs.org](nodejs.org)，选择 ```LTS``` 的。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/截屏2020-03-30上午10.29.35.png)

安装好之后就打开终端输入命令 ``` sudu su``` 切换到 ```root``` 用户，我们可以查看 ```node``` 和 ```npm``` 的版本
然后安装taobao镜源，输入命令：```npm install -g cnpm --registry=https://registry.npm.taobao.org```  

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/截屏2020-03-30上午11.10.30.png)

然后安装 ```hexo```，输入命令：```cnpm install -g hexo-cli``` 下载 ```hexo```，然后创建一个目录 ```two```，进入这个目录

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.36.04.png)

然后输入命令：```sudo hexo init``` 用于初始化这个博客

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.32.24.png)

然后输入命令：```hexo s``` 来创建自己的博客，然后在本地的网址(http://localhost:4000)可以看见，博客已经创建好了。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.39.19.png)

然后我们来写文章了，输入命令：```hexo n "BucketSort"```




