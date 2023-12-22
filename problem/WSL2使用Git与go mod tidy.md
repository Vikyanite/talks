## WSL2使用Git拉取私有库与go build

> WSL2感觉就是空壳，啥都没有，啥都要自己下...

这个需求的主要原因是因为想在WSL2进入Windows下的Go project目录运行`go build`，拿到二进制编译文件之后上传到线上服务器进行部署。结果发现WSL2连go都没给我装上...

## 安装Go 1.19

具体是参考这篇文章：https://blog.csdn.net/weixin_43268590/article/details/126408576

### 下载与安装

``` bash 
cd /usr/lib														#进入安装go的目录
wget https://golang.google.cn/dl/go1.19.linux-amd64.tar.gz		#下载go linux压缩包
tar -xzf go1.19.linux-amd64.tar.gz								#解压
rm -f go1.19.linux-amd64.tar.gz									#删除压缩包
```

### 环境变量配置

在环境变量配置文件`/etc/profile`中添加以下内容：

``` bash
# GO PATH
export PATH=$PATH:/usr/lib/go/bin
```

重新载入配置文件

```bash 
source /etc/profile
```

不要忘记打开go mod

```bash
go env -w GO111MODULE=on
```

### 验证

```bash
go version
```

如果出现```go version go1.19 linux/amd64```即为配置成功

### 解答

有人可能会问，为什么还要再装一边，直接`cd /mnt`把windows下的go export出来不就好了,emm主要一开始觉得go的linux版本与windows版本不大一样，可能会导致无法使用，所以就决定Linux自己也在装一份相同go版本的。

## Git拉取私有库

非常奇怪的是我明明都设置好了用户名跟密码，已经是跟gitlab对上了，但还是要我输入密码。最后百度发现可能是因为wsl2的git没有进行credential manager的初始化，参考链接：[Get started using Git on WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git)

`{YourGitDir}`填入你的git所在文件夹

如果git版本 >= v2.36.1

``` shell
git config --global credential.helper "/mnt/{YourGitDir}/Git/mingw64/bin/git-credential-manager-core.exe"
```

如果版本 < v2.36.1

```shell
git config --global credential.helper "/mnt/{YourGitDir}/Git/mingw64/libexec/git-core/git-credential-manager.exe"
```

## go build 与 go mod tidy

好现在我们可以运行`go build`指令了，但是运行的时候发现报错了，发现是因为忘记`go mod tidy`了，因为当前的环境是Linux，同时Go也重新装了，所以需要重新安装依赖包。

结果`go mod tidy`也报错了，信息如下：

```
fatal: could not read Username for 'https://github.com': terminal prompts disabled
```

通过[这篇博客](https://blog.csdn.net/weixin_41915314/article/details/111232622)我们知道原来是因为Linux下更建议使用SSH进行包的拉取，而我们现在采用的是HTTP方式，所以会需要验证Username。于是我们看[官网教程](https://x12-gitlab.diezhi.net/help/ssh/index#generate-an-ssh-key-pair)，跟着他进行操作。如果有私有库的话记得需要输入以下代码：

```bash
git config --global url."git@xxx:".insteadOf "https://xxx/"
```

但是很离谱的事，对于官网教程推荐的编码方式ed25519我怎么试也没有办法连接，改成rsa就好了...

(emm发现gitlab的教程居然是私有的？？感觉好像也没啥关系，就在这里顺便分享一下吧)

### 生成 SSH key对

如果你没有生成过 SSH key对的话，就可以参照一下步骤生成

1. 打开终端（terminal）.

2. 输入以下语句，如果是以`ed25519`加密方式生成就将`rsa`改为`ed25519`

   ```shell
   ssh-keygen -t rsa -C "youremail@xxx"
   ```

3. 敲下回车，之后会出现以下语句

   ```plaintext
   Generating public/private rsa key pair.
   Enter file in which to save the key (/home/user/.ssh/id_rsa):
   ```

4. 上面的语句的意思是生成的密钥文件将放在显示的文件夹，如果没有需要修改的需求就直接按下回车

5. 确认[passphrase](https://www.ssh.com/academy/ssh/passphrase):

   ```plaintext
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   ```

### 将生成的SSH Key对添加至Gitlab账户

你需要先获得刚生成的ssh key的公钥

1. 可以通过以下语句获取

   **macOS:**

   ```shell
   tr -d '\n' < ~/.ssh/id_rsa.pub | pbcopy
   ```

   **Linux** (requires the `xclip` package):

   ```shell
   xclip -sel clip < ~/.ssh/id_rsa.pub
   ```

   **!!!!** 关于这个命令我会出现一个很离谱的错误：[关于linux：错误：使用Xclip复制ssh公钥时无法打开显示：(空) ](https://www.codenong.com/18695934/)

   如果只是查看的话我建议使用：`cat ~/.ssh/id_rsa.pub ` 

   **Git Bash on Windows:**

   ```shell
   cat ~/.ssh/id_rsa.pub | clip
   ```

   如果是ED25519的话把 `id_rsa.pub` 换成 `id_ed25519.pub` 就行。

2. 登录Gitlab

3. 点击右上角的头像旁的三角

4. 选择 **Preferences**.

5. 选择左边侧边的选项**SSH Keys**.

6. 在Key的输入框中输入你刚得到的公钥，一般格式是 `加密方式 字符串 你之前输入的Comment`

7. 输入一个**Tittle**

8. 点击**Add key**.

### 校验你的公钥

1. 在终端输入

   ```shell
   ssh -T git@gitlab.example.com
   ```

2. 如果你是第一次使用SSH Key对，并且输入的公钥无误，会出下以下界面

   ```plaintext
   The authenticity of host 'gitlab.example.com (35.231.145.151)' can't be established.
   ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
   Are you sure you want to continue connecting (yes/no)? yes
   Warning: Permanently added 'gitlab.example.com' (ECDSA) to the list of known hosts.
   ```

   输入 `yes` 同时按下回车

3. 再次输入 `ssh -T git@gitlab.example.com` . 你将会看见 *Welcome to GitLab, `@username`!* 的信息.

### 回到go mod tidy

装完SSH Key Pair之后总算是不报之前的错误了，但结果还是有错误，如下：

``` shell
read: Connection reset by peer
```

参考这篇博客：[ssh连接失败，排错经验 ](https://www.cnblogs.com/starof/p/4709805.html)

发现可能是因为客户端IP被服务器禁掉了，所以需要去`/etc/hosts.allow`，往其中添加`sshd: ALL`

之后重启ssh，这里我是用`openssh-server` `openssh-client`来进行操作的

> 我以为ubuntu也是通过systemctl来重启。然后发现没有，之后就apt install systemctl了。结果这个下过来的还有bug。

下载`openssh-server` `openssh-client`语句：

``` bash
sudo apt-get install openssh-server openssh-client
```

然后重启ssh:

``` bash
service ssh start
```

结果报错了`sshd: no hostkeys available – exiting.`，使用以下语句解决：

``` bash
ssh-keygen -A
/etc/init.d/ssh start
```

参考[sshd: no hostkeys available -- exiting](https://blog.csdn.net/p1456230/article/details/120922515)，完美解决。

之后跑`go mod tidy`就一点问题都没有了

### 总算可以go build了

运行`go build -o server main.go`，成功编译！！