```
@Date - 2020年3月20日23点40分

@Author - xzy
```

### 问题描述

向git库推送更新时：

```
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/xiaozhengyu/StudyNotes.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

### 原因

本地现有的库和git上不同（我好像在登陆git，修改了readme文件）。

### 解决方案

1. git pull  //更新本地的库
2. git push //再次上传