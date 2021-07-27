---
title: PYTHON环境管理工具[PYENV]
tags: []
---

1. 下载代码到: ~/.pyenv
```
mkdir ~/.pyenv
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

2. 添加环境变量
```
vim ~/.bash_profile

添加
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"

```
3. 启动shell时，初始化pyenv
```
vim ~/.zshrc

末尾追加

eval "$(pyenv init -)"
```
4. 重新加载 .zshrc
```
source ~/.zshrc
```
5. 安装依赖
- mac
```
brew install openssl readline sqlite3 xz zlib
```
- ubuntu
```
sudo apt-get update; sudo apt-get install --no-install-recommends make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```
- centos
```
yum install gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel tk-devel libffi-devel
```

6.  下载版本
```
pyenv install 3.8.5
```

7. 切换全局版本
```
pyenv global 3.8.5
```
