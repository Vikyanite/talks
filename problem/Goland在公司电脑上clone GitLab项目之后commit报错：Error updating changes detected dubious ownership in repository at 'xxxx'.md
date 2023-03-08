## Goland在公司电脑上clone GitLab项目之后commit报错：Error updating changes: detected dubious ownership in repository at 'xx/xx'

原因是因为公司电脑上的git config的邮箱与你公司给你的域邮箱不同，可以通过

``` bash
git config --list
```

去查看你的git config相关信息

<u>ps.输入命令后按空格查看更多，按Q退出</u>



然后我们就需要去修改git config中的相关设置，如果你只想要这个项目修改的话就使用：

```bash
git config --local user.email "xxx@xxx"
```

全局修改就使用：

```bash
git config --global user.email "xxx@xxx"
```

修改后重启Goland即可