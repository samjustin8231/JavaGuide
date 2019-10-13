# git撤销commit
写完代码后，我们一般这样

git add . //添加所有文件

git commit -m "本功能全部完成"

执行完commit后，想撤回commit，怎么办？

这样凉拌：

```
git reset --soft HEAD^
```



# 参考文献

- [git commit之后，想撤销commit](https://www.cnblogs.com/lfxiao/p/9378763.html)