Simula语言教程以及中文翻译项目


————————————————————————————————————————

###关于

Simula语言是面向对象的鼻祖语言，出于兴趣，出去对翻译技术文档的热情，我决定整理一下这个语言的教程并进行翻译。也希望想练习英文、想试试看翻译技术文档、想提高英文水平的同学一起来参与这个项目，最后的翻译结果大家都可以相互review，相互学习。

原文地址：
[An Introduction to Programming in Simula](http://www.macs.hw.ac.uk/~rjp/bookhtml/) by Rob Pooley

###线上预览地址
[Simula语言教程](http://fkysly.github.io/learn-simula)

###如何运行和开发
1. 安装gitbook-cli，利用它安装gitbook v2.2.0。(也可以不看效果，照着英文版本的直接翻译就行了。)
2. 根目录下运行gitbook serve，可以开启本地服务器，访问http://localhost:4000，可以看到效果。(也可以不看效果，照着英文版本的直接翻译就行了。)
3. 根目录下运行gitbook build，可以构建项目，会生成_book目录，可以进行部署。
4. 根目录下运行gulp或者gulp deploy，可以部署到git的远程仓库下的gh-pages分支。（如果你fork，那么是你自己的git远程仓库地址哦）
5. 部署完毕之后，打开http://(your_name).github.io/(repo_name)即可访问。

###如何参与

1. 请先自行学习gitbook的相关知识。
2. fork此项目到自己的账号下。
3. 参见[wiki](https://github.com/fkysly/learn-simula/wiki)和[issues](https://github.com/fkysly/learn-simula/issues)的进度，找到自己感兴趣且无人认领的章节。
4. 新开一个issue申请认领的页面。
5. en为英文原版目录，zh为中文翻译目录，文件一一对应，按照英文的格式来翻译放到zh目录即可。
   (请注意一点，{{ book.ChapterX }} 相应改为 {{ book.zh-ChapterX }}, {{ book.AppendixX }} 相应改为 {{ book.zh-AppendixX }})
6. 翻译完毕，请直接pull request到此目录的master。

###Licence

licence请参阅en/README.md的内容。
