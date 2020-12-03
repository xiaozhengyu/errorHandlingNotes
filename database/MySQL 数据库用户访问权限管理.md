# MySQL 数据库用户访问权限管理

---

### 1. 授予访问某张表的权限

```mysql
# 授予用户 depuser 访问 zentaopro.bug_to_build 表的 SELECT 权限
GRANT SELECT ON zentaopro.bug_to_build TO 'depuser'
```

