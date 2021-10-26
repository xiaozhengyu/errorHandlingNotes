# openssl升级后libssl.so.10缺失问题

---

解决办法：从其他系统的/usr/lib64 下拷贝以下两个文件止当前系统

```
libssl.so.1.0.0
libcrypto.so.1.0.0
```

(当前文档所在目录下已经准备好了这两个文件)

