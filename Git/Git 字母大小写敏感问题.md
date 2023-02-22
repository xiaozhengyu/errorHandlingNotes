# Git 字母大小写敏感问题

问题描述：在本地将一个名为 Cookie.txt 的文件改名为 cookie.txt，然后执行 git status 命令，发现 Git 并没有发现这次修改。



问题分析：Git 默认的配置是忽略大小写



问题解决：执行命令 `git config core.ignorecase false`，关闭大小写忽略