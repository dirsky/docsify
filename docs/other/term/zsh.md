
> 各种补全：路径补全、命令补全，命令参数补全，插件内容补全等等。
>
> 语法高亮、显示Git仓库状态等功能
>
> Author: gzxu@vip.qq.com

##  1. Install Oh-my-zsh

```shell
# 可能需要配置
vim /etc/hosts
151.101.108.133 raw.githubusercontent.com
```

CentOS

```shell
yum install -y zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

chsh -s /bin/zsh
```

MAC

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## 2. 配置 .zshrc

### 插件

```shell
# 自动建议，后面灰色提醒
# centos (感觉应该也参照mac 放到$ZSH_CUSTOM)
git clone --depth=1 git://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/plugins/zsh-autosuggestions

# mac 克隆仓库到本地 ~/.oh-my-zsh/custom/plugins 路径下
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

# 调亮度
cd ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
用 vim 编辑 zsh-autosuggestions.zsh 文件，修改ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10'

# 色彩高亮度
# 白色代表普通命令或者程序，红色代表错误命令。下面青色的代表内建命令或者 alias （echo 和 ls ）。

# centos(感觉应该也参照mac 放到$ZSH_CUSTOM)
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/plugins/zsh-syntax-highlighting

# mac（感觉应该参照zsh-autosuggestions而不用brew）
brew install zsh-syntax-highlighting
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

```

```shell
# 配置插件
vim ~/.zshrc

plugins=(
   git
   extract
   kubectl
   zsh-syntax-highlighting
   zsh-autosuggestions
   z
)
exit with :x!
# :wq 强制性写入文件并退出。即使文件没有被修改也强制写入，并更新文件的修改时间。
# :x 写入文件并退出。仅当文件被修改时才写入，并更新文件修改时间，否则不会更新文件修改时间。

source ~/.zshrc
```

### 主题

When the installation is done, edit `~/.zshrc` and set `ZSH_THEME="agnoster"` for the default look. Or better yet, go for Powerlevel10k.

### 别名(设置完毕需要source  .zshrc)

```shell
alias cls='clear'
alias ll='ls -l'
alias la='ls -a'
alias vi='vim'
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto"
alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
alias -s js=vi
alias -s c=vi
alias -s java=vi
alias -s txt=vi
alias -s gz='tar -xzvf'
alias -s tgz='tar -xzvf'
alias -s zip='unzip'
alias -s bz2='tar -xjvf'

# zsh 的牛粪之处在于不仅可以设置通用别名，还能针对文件类型设置对应的打开程序，比如：
alias -s html=mate，意思就是你在命令行输入 hello.html，zsh会为你自动打开 TextMat 并读取 hello.html； alias -s gz='tar -xzvf'，表示自动解压后缀为 gz 的压缩包。
```



## 3. Use

### 快捷跳转目录

1. 输入d回车,就会列出你在这个回话中访问的目录，输入前面的序号，就可以直接跳转

2. 可以忽略cd命令, 输入..或者...和当前目录名都可以跳转 cd /d/d/a 然后tab模糊匹配

3. z 目录回车查看所有，z 智能跳转(需要在plutins下启用)

4. cd 空格后，上下键也是可以看历史地址

### 其他快捷操作

1. 强大的历史纪录功能，输入 grep 然后用上下箭头可以翻阅你执行的所有 grep 命令。

2. 触发补全只需要按一下或两下 tab 键，补全项可以使用 ctrl+n/p/f/b上下左右切换。

3. 比如你想杀掉 java 的进程，只需要输入 kill java + tab键，如果只有一个 java 进程，zsh 会自动替换为进程的 pid，如果有多个则会出现选择项供你选择。

4. ssh + 空格 + 两个tab键，zsh会列出所有访问过的主机和用户名进行补全

## 其他问题

```shell
# 登录报错
[oh-my-zsh] Insecure completion-dependent directories detected:
drwxrwxr-x 7 root root 4096 7月  25 16:23 /root/.oh-my-zsh/plugins/zsh-autosuggestions

[oh-my-zsh] For safety, we will not load completions from these directories until
[oh-my-zsh] you fix their permissions and ownership and restart zsh.
# 解决方案
chmod 755 /root/.oh-my-zsh/plugins/zsh-autosuggestions
```

