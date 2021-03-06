# 搭建个人博客
首先安装好```node```，网站是 [Node.js](nodejs.org)，选择 ```LTS``` 的下载。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/截屏2020-03-30上午10.29.35.png)

安装好之后就打开终端输入命令 ``` sudu su``` 切换到 ```root``` 用户，我们可以查看 ```node``` 和 ```npm``` 的版本
然后安装taobao镜源，输入命令：```npm install -g cnpm --registry=https://registry.npm.taobao.org```  

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/截屏2020-03-30上午11.10.30.png)

然后安装 ```hexo```，输入命令：```cnpm install -g hexo-cli``` 下载 ```hexo```，然后创建一个目录 ```two```，进入这个目录

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.36.04.png)

然后输入命令：```sudo hexo init``` 用于初始化这个博客

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.32.24.png)

然后输入命令：```hexo s``` 来创建自己的博客，然后在本地的网址(localhost:4000)可以看见，博客已经创建好了。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8A%E5%8D%8811.39.19.png)

然后我们来写文章了，输入命令：```hexo n "BucketSort"``` (名字就自己安排啦)，创建好文件后就进入到文件所在的路径下，用 ```vim``` 编辑它

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%8812.13.29.png)

然后我们回到上两级(根目录)下，使用命令：```hexo clean``` 来清除缓存，然后用：```hexo g``` 来生成

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%8812.17.38.png)

然后我们在用命令：```hexo s``` 来重新启动自己的博客，然后在本地的网址(localhost:4000)可以看见，博客已经更新好了。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%8812.17.38.png)

# 个人博客与GitHub关联

首先创建一个GitHub仓库，仓库名字命名必须为：XX(你的名称).github.io，(由于我创建过了，所以名字重复了，一定要注意与前面那个用户名一样)

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%881.23.41.png)

然后我们要在根目录下安装一个插件，输入命令：```cnpm install --save hexo-deployer-git```

安装好之后我们查看目录，发现有一个叫 ```_config.yml``` 的文件

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%881.31.09.png)

我们去用 ```vim``` 去修改它，打开 ```_config.yml``` 后，我们拖到最底部，在 ```Deployment``` 下面修改一下(repo是刚刚创建的那个仓库的地址、branch默认下是master分支，写了也没问题)：
```
type: git
repo: https://github.com/Coding-in-SCNU/Coding-in-SCNU.github.io.git
branch: master
```

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%881.38.23.png)

然后我们就开始部署了，部署很简单，命令：```hexo d```，这个时候输入自己的GitHub账号和密码(可能中途输入密码后会报错，是因为我没有设置git的global身份，重新设置密码就可以解决了)

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%881.43.42.png)

这个时候只需要将自己的仓库名输到浏览器里面就能看到自己的blog了，至于博客页面的主题风格就看自己喜爱了

# 更换主题
首先我们去到主题下载的GitHub仓库中去，会有克隆的命令给你的，输入然后克隆就好

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%889.19.06.png)

然后用 ```vim``` 修改 ```_config.yml``` 文件，在最底下，修改主题 ```theme``` 为自己克隆好的主题(人家的GitHub仓库下也会给你)

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%889.24.00.png)

然后输入一系列命令：```hexo clean```, ```hexo g```, ```hexo s```, ```hexo d```, 就搞定了。

![p](https://github.com/Coding-in-SCNU/Upload_Picture/blob/master/%E6%88%AA%E5%B1%8F2020-03-30%E4%B8%8B%E5%8D%889.30.33.png)





