
### 配置git环境

git config --global user.email "dirsky@126.com"
git config --global user.name "frank"
git config --global push.default simple
把RSA.public填写到git远程服务器上

查看配置
git config --global -l

### 测试
ssh -T git@gitee.com
ssh -T git@github.com

### git init

git remote add origin git@github.com:dirsky/sublime-lab.git
git pull origin master

1. 删除
git remote rm origin

2. 添加
git add .
git commit -m "init commit"
git remote add origin git@github.com:dirsky/express-test.git

3. 建立本地与远程的关联
git push -u origin master

git config --global alias.acp '!f() { git add -A && git commit -m "$@" && git push; }; f'
使用时只需要 git acp "内容"

find . -name '*.DS_Store' -type f -delete
删除项目下的 ds_Store
