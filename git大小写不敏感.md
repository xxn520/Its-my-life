用了这么久git，今天第一次遇到文件大小写不区分的问题，因为node找依赖模块时区分大小写，项目就跑不起来了！！！

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=188647&auto=1&height=66"></iframe>

只能找办法解决：首先修改git的配置，使其大小写敏感
```
git config core.ignorecase false
```
其次需要删除本地文件重新add和commit
```
git rm -f filename

git add filename

git commit -m "message"
```