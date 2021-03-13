##### gitlab 安装

###### 1. 安装依赖

```shell
#安装依赖
sudo yum install -y curl policycoreutils-python openssh-server

#启动ssh服务&设置为开机启动
sudo systemctl enable sshd
sudo systemctl start sshd
```

###### 2. 安装Postfix

Postfix是一个邮件服务器，GitLab发送邮件需要用到

```shell
#安装postfix
sudo yum install -y postfix

#启动postfix并设置为开机启动
sudo systemctl enable postfix
sudo systemctl start postfix
```

###### 3. Yum安装GitLab

安装 `gitlab 社区版`

```shell
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

sudo yum install -y gitlab-ce
```

###### 4. 配置

GitLab默认的配置文件路径是`/etc/gitlab/gitlab.rb`

默认的站点 Url 配置项是：`external_url 'http://gitlab.example.com'`

```shell
#修改配置文件
sudo vi /etc/gitlab/gitlab.rb

#配置首页地址
external_url 'http://81.70.241.186'

#重新配置并启动
sudo gitlab-ctl reconfigure
#重启服务
sudo gitlab-ctl restart
```



###### 可能遇到的问题

gitlab自带 nginx 与原  nginx 冲突，通过修改 gitlab 端口解决冲突。

```shell
vim /var/opt/gitlab/nginx/conf/gitlab-http.conf
```

``` yml
upstream gitlab-workhorse {
  server unix:/var/opt/gitlab/gitlab-workhorse/socket;
}

server {
  listen *:80;  --修改端口


  server_name localhost;
  server_tokens off; ## Don't show the nginx version number, a security best practice
  ......
}
```

将其中的 80 改为其它端口即可，执行`gitlab-ctl restart` 重启  gitlab。



##### gitlab runner 安装

添加gitlab官方库:

```shell
# For Debian/Ubuntu/Mint
 curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash

 # For RHEL/CentOS/Fedora
 curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.rpm.sh | sudo bash
```

命令行安装：

```shell
# MacOS
sudo brew install gitlab-ci-multi-runner
# For Debian/Ubuntu/Mint
sudo apt-get install gitlab-ci-multi-runner
# For RHEL/CentOS/Fedora
sudo yum install gitlab-ci-multi-runner
```



##### gitlab 全自动测试打包

###### token

首先要先获取 `gitlab-ci` 的 token

> 1. 项目主页 -> Sttings -> CI/CD -> Runners Expand
> 2. root 用户可以配置全局的 runner: Admin Area -> Runners -> registration token

###### 注册 runner

```shell
sudo gitlab-runner register
```

```shell
[root@localhost ~]#  gitlab-runner register
Running in system-mode.                            
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://81.70.241.186:8888
Please enter the gitlab-ci token for this runner:
Y6E4nHvyx9hgU3ty_Jko
# 输入描述
Please enter the gitlab-ci description for this runner:
[localhost.localdomain]: ihappy ci runner
# 输入tag，一个项目可能有多个runner，是根据tag来区别runner的
Please enter the gitlab-ci tags for this runner (comma separated):
ihappy-ci
# 你输入executor，这个是要用什么方式来执行脚本，图方便输入 shell 就好了
Please enter the executor: shell, ssh, docker+machine, docker, docker-ssh, parallels, virtualbox, docker-ssh+machine, kubernetes:
shell
Runner registered successfully. 
```



######  配置文件 .gitlab-ci.yml

简单的 demo 配置文件：

```yml
stages:
  - build
  - test
  - deploy

before_script:
  - echo "before script..."

build_job:
  stage: build
  tags:
    - hello-ci
  only:
    - master
  script:
    - echo "build start..."
    - ./mvnw clean
    - ./mvnw install
    - ./mvnw compile

test_job:
  stage: test
  tags:
    - hello-ci
  script:
    - echo "tests start..."
    - ./mvnw test

deploy_job:
  stage: deploy
  tags:
    - hello-ci
  only:
    - master
  script:
    - echo "deploy start..."
    - ./mvnw package -Dmaven.test.skip=true
    - echo "J********" | sudo -S mv ./target/hello-1.0.war ./target/hello.war
    - echo "J********" | sudo -S cp ./target/hello.war /usr/tomcat/tomcat8/webapps
```

- stages: 用于定义`job`可以使用的阶段，不同阶段按顺序执行
- before_script: 该命令在每个`job`之前运行
- fter_script: 用于定义将在每个`job`（包括失败的）之后运行的命令
- tags: 指定可以使用的 `runner`, 比如`ihappy-ci`、`hello-ci`
- only: 指定运行的分支
- script:  shell 命令

> gitlab仓库地址： /home/gitlab-runner/builds/xxx

在线验证语法：验证` .gitlab-ci.yml`配置文件语法是否正确

[CI Lint](http://81.70.241.186:8888/happy/ihappy/-/ci/lint)

待优化：

- cache: 加快打包速度

```yml
cache:
  key: build
  paths:
    - target/
```

- include: 可以引入外部YAML文件，优化打包脚本。

###### 可能遇到的问题

1. 执行脚本的用户为`gitlab-runner`，有些命令需要`sudo`权限。

```shell
# 修改 gitlab-runner 密码
passwd gitlab-runner
# 把 gitlab-runner 添加进去
sudo vi /etc/sudoers
```



参考资料：

http://www.idevops.site/gitlabci/

[gitlab 模板](https://github.com/zeyangli/gitlabci-templates)

