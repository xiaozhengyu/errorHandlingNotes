# Git 强制覆盖本地数据

---

``` shell
git fetch --all
git reset --hard
git pull
```

![img](markdown/Git 强制覆盖本地数据.assets/git.jpg)

`git fetch`是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。而`git pull` 则是将远程主机的最新内容拉下来后直接合并，即：`git pull = git fetch + git merge`，这样可能会产生冲突，需要手动解决。