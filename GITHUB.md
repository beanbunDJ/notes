# GITHUB

## 设置密钥

- 设置用户名和邮箱

  ``` javascript
  git config --global user.name "bing"
  git config --global user.email  "550392236@qq.com"
  ```

- 进入用户根目录，删除已有的ssh

  ```javascript
  cd ~
  rm -r .ssh
  ```

- 生成.ssh密钥

  ```javascript
  ssh-keygen -t rsa -C [自己的邮箱地址]
  //例如：ssh-keygen -t rsa -C 626130833@qq.com 
  ```

- 一直回撤直至结束

- 查看.ssh文件

  ```javascript
  cd .ssh
  ls
  //id_rsa:私钥；id_rsa.public:公钥
  ```

- 查看并复制公钥

  ```javascript
  cat id_rsa.pub
  ```

- github添加ssh，粘贴公钥即可

## 本地仓库推送至GITHUB

- 进入工作目录例如：`cd nodes`
- 初始化仓库：`git init`
- 添加所有文件：`git add .`
- 提交所有文件：`git commit -m '提交信息说明'`
- 与远程仓库建立关联：`git remote add origin [需要关联的仓库地址]`；例如：`git reomote add origin git@github.com:xxx/xxx.git`
- 推送分支：`git push origin master`