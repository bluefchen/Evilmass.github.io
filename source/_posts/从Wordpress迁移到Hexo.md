---
title: 从Wordpress迁移到Hexo
date: 2017-02-06 00:34:48
tags: Linux
---

### 前言
之前有一个cc域名是Wordpress的，而me域名则是GithubPage + Hexo，可惜me域名过期没钱续费了，两边更新文章也挺麻烦，遂切换到VPS + Hexo + Webhooks

<!--more-->

一时手贱注册N多一年免费域名，前排出（赠）售（送）以下域名，还有个Namecheap的SSL证书
> [evil0mass.tk][1]
  [evil0mass.me][2]
  [evilmass.xyz----这个拿来做MHP Tunnel的服务器了][3]
  [evilmass.tk][5]
  [evilmass.ml][6]
  [evilmass.cn----这个没有实名认证白送了一块钱给腾讯][7]
  
<br>
### VPS基本参数
对，搬砖，最便宜的那个，现在出了CHINA DIRECT ROUTE，内存也提升到了512M，性价比极高，可惜不是KVM架构。。。

> SPECIAL 10G PROMO V3 - LOS ANGELES - CHINA DIRECT ROUTE 
  SSD: 10 GB RAID-10
  RAM: 512 MB
  CPU: 1x Intel Xeon
  Transfer: 1000 GB/mo
  Link speed: 1 Gigabit
  Location: Los Angeles (no other locations available on this plan)
  Direct route via China Telecom and China Unicom

我的推荐链接：https://bandwagonhost.com/aff.php?aff=13364
China-Direct-Route：在地址栏最后添加`/cart.php`
<br>
### Shadowsocks
    yum update -y
    yum install python-setuptools m2crypto libtool gcc && easy_install pip
    pip install shadowsocks
<br>
#### libsodium依赖
    curl -O -L https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
    tar zxf LATEST.tar.gz
    cd libsodium*
    ./configure
    make && make install
    # 修复关联
    echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
    ldconfig
    rm -rfv ../LATEST.tar.gz ../libsodium* && cd ~
<br>   
#### 多端口配置
    {
        "server":"0.0.0.0",
        "local_address":"127.0.0.1",
        "local_port":1080,
        "port_password":{
            "9000":"password0",
            "9001":"password1",
            "9002":"password2",
            "9003":"password3"
        },
        "timeout":600,
        "method":"chacha20",
        "fast_open": true
    }
<br>
#### 配置自启动
新建启动脚本文件/etc/systemd/system/shadowsocks.service，内容如下：

    [Unit]
    Description=Shadowsocks

    [Service]
    TimeoutStartSec=0
    ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json
    
    [Install]
    WantedBy=multi-user.target
    
<br>
#### 启动 shadowsocks

    systemctl enable shadowsocks  #开机启动
    systemctl start shadowsocks  #开启服务

为了检查 shadowsocks服务是否已成功启动，可以执行以下命令查看服务的状态：

    systemctl status shadowsocks -l
<br>   
#### Shadowsocks优化
**Openvz用户可以不用看这部分的优化**

[shadowsocks参数优化][8]
    
**KVM架构的VPS建议安装** [Google_BBR][9] 
目前看来是BBR比锐速强势

> [Kcptun--Openvz的救星][10]
  [FinalSpeed--同上][11]


<br>
### [Git][13]
建议手动编译安装较新版本的Git，以便配置ngrok
<br>
### [SSH-Keygen][14]

<br>
### [Nginx][15]
 
    yum install nginx
    systemctl start nginx
    systemctl enable nginx
    
**喜欢手动编译安装Nginx是病，得治（雾**

手动编译安装参考
> Zlib：http://zlib.net/
  pcre：https://ftp.pcre.org/pub/pcre/
  Open-SSL：https://www.openssl.org/source/
 
<br>
### [NodeJS][16]
建议yum直接安装，否则在后面部署pm2（forever）过程中会出现如下错误：
> /usr/bin/env: node: No such file or directory

#### 直接安装
    yum install nodejs
<br>
#### 二进制包安装
    cd /home
    wget https://nodejs.org/dist/v7.5.0/node-v7.5.0-linux-x64.tar.xz
    tar -xf node-v7.5.0-linux-x64.tar.xz
    ./node-v7.5.0-linux-x64/bin/node -v
输出`v7.5.0`即可
<br>
##### 软连接
    ln -s /home/node-v7.5.0-linux-x64/bin/node /usr/local/bin/node
    ln -s /home/node-v7.5.0-linux-x64/bin/npm /usr/local/bin/npm
<br>
##### 添加到PATH
    vim /etc/profile
    
    #添加到最后
    PATH=$PATH:/home/node-v7.5.0-linux-x64/bin
    
    #即刻生效
    source /etc/profile 
<br>
#### 源码编译安装（你这样是要被电的。。。

    yum install gcc-c++ screen  #在耗时较多的任务又怕shell断开连接，可以开启screen
    wget https://nodejs.org/dist/v7.5.0/node-v7.5.0.tar.gz
    tar -zxvf node-v7.5.0.tar.gz
    cd node-v7.5.0
    ./configure
    make && make install  #我试过用树莓派编译，6个多小时
    
<br>
### [Hexo][17]

    npm install hexo-cli -g
    hexo init blog
    cd blog
    npm install
<br>
#### [Next主题][18]
<br>
#### 让Hexo在后台运行

    npm install pm2 -g  #全局安装pm2
当然，用forever也是可以的，只是pm2更强大更好用而已

创建一个`app.js`写入以下内容

    var spawn = require('child_process').spawn;
        free = spawn('hexo', ['server']);

    free.stdout.on('data', function (data) {
        console.log('standard output:\n' + data);
    });

    free.stderr.on('data', function (data) {
        console.log('standard error output:\n' + data);
    });

    free.on('exit', function (code, signal) {
        console.log('child process exit, exit: ' + code);
    });
    
使用

    pm2 list              # 查看已运行的服务
    pm2 show <id or name> # 查看启动服务的详细信息
    pm2 monit             # 查看pm2在服务器上的占用
    pm2 start app.js      # 启动hexo
    pm2 kill app.js       # 停止hexo   
    pm2 save              # 保存当前设置
    pm2 startup           # 开机启动hexo服务
    
<br>
#### crontab注意事项
crond是Centos系统的 一个服务，也就也就意味着：
**crontab -e之后Command不执行的原因之一是系统没有开启crond服务**

    systemctl start crond
    systemctl enable crond #加入开机启动
<br>
### [Let's Encrypt][19]证书自动续期

    vim /home/ssl_renew.sh
    
    #!/bin/bash
    /home/letsencrypt/certbot-auto renew
    
    #添加到crontab每月运行一次
    crontab -e
    
    * * 1 * * sh /home/ssl_renew.sh

<br>
#### 配置 Nginx 代理
作为一个对外公开的网站，使用 4000 端口显然是不合适的。可以直接改成 80 端口，但是这样直接把 Hexo 服务暴露给用户，并不恰当。更好的办法是使用 Nginx 做代理。
    
    vim /etc/nginx/nginx.conf

    server {
    location / {
        proxy_pass http://localhost:4000;
    }
    access_log  /var/log/nginx/blog.access.log;
    error_log /var/log/nginx/blog.error.log;
    }
重启 Nginx：

    service nginx restart

<br>
### Webhooks
**觉得Webhook太复杂的可以直接看后面UPDATE的内容**
**觉得Webhook太复杂的可以直接看后面UPDATE的内容**
**觉得Webhook太复杂的可以直接看后面UPDATE的内容**

#### 简单说下Webhooks原理
> **Webhook**，也就是人们常说的钩子，是一个很有用的工具。你可以通过定制 Webhook 来监测你在 Github.com 上的各种事件，最常见的莫过于**push**事件。如果你设置了一个监测 push 事件的 Webhook（**`deploy.js`**），那么每当你的这个项目有了任何提交，这个 Webhook 都会被触发，这时 Github 就会发送一个 HTTP POST 请求到你配置好的地址（Payload URL），然后执行我们VPS上面同步更新文章的脚本（**`deploy.sh`**）

<br>
**自动部署流程**：本地执行`sync.sh`推送文章到Github -> Github产生push事件 -> 服务器上的`deploy.js`监听到该事件 -> deploy.js调用`deploy.sh` -> VPS同步文章完毕-> 展示
<br>
#### 注意事项
* **设置好Wenhooks之后更新文章都在本地进行，最好不要在vps上面执行`git push`或者`hexo d`之类的操作，容易产生conflict**

* **若已有hexo环境请勿执行`hexo init`，否则会覆盖本地的.git文件夹**
 
* **如果之前是Github-Page + Hexo的方式的话，以后用脚本更新文章，弃用`hexo g -d`的方式**

<br>
### 本地配置
创建本地推送脚本`vim sync.sh`

#### sync.sh

    #!/bin/bash
    #\033是控制台代码行的输出颜色，这里是绿色
    echo -e "\033[32m [AUTO DEPLOY] deploy hexo start \033[0m"
    echo -e "\033[32m [AUTO DEPLOY] hexo generate...  \033[0m"
    hexo g
    echo -e "\033[32m [AUTO DEPLOY] git commit...  \033[0m"
    d=`date +%x-%T`
    git add .
    git commit -m "auto deploy at "${d}
    echo -e "\033[32m [AUTO DEPLOY] git push...  \033[0m"
    git push origin master
    echo -e "\033[32m [AUTO DEPLOY] deploy hexo finish \033[0m"

说Windows没有运行脚本环境的，你们似乎忘了**Git-Shell**和**Git-Bash**这个东西。。。
![Git-Bash][Git-Bash]
<br>
**deploy.js包含Webhook的密码，绝对不可以推送到仓库去**
**deploy.js包含Webhook的密码，绝对不可以推送到仓库去**
**deploy.js包含Webhook的密码，绝对不可以推送到仓库去**
这一点很重要，包含密码的私有文件或者ssh密匙不能推送到公共仓库，会造成隐私泄漏等安全问题
#### .gitignore写入以下内容
    .DS_Store
    Thumbs.db
    *.log
    *./           #隐藏文件夹，这里为了防止推送node_modules/.bin目录
    sync.sh       #本地推送文章的脚本
    node_modules/ #Hexo的运行环境
    public/ #hexo generate产生的静态页面
    .deploy_git/  #之前hexo-deploy-git方式产生的文件夹
Hexo目录包含hexo的运行环境，我们并不需要把这些文件都推送上去，这样整个仓库会变得很大，不利于其他服务器部署
#### 多机器配置（需要NodeJS环境)
    git clone 仓库
    npm install
<br>
### 服务器端配置
需要用到这个模块**github-webhook-handler**：

    npm install github-webhook-handler -g
<br>
#### 监听push事件的`deploy.js`脚本

    var http = require('http')
    var createHandler = require('github-webhook-handler')
    var handler = createHandler({ path: '/', secret: 'your_secret' }) //path : '\'代表当前目录，secret一定要和github-webhooks上配置的一致
    
    function run_cmd(cmd, args, callback) {
      var spawn = require('child_process').spawn;
      var child = spawn(cmd, args);
      var resp = "";
    
      child.stdout.on('data', function(buffer) { resp += buffer.toString(); });
      child.stdout.on('end', function() { callback (resp) });
    }
    
    http.createServer(function (req, res) {
      handler(req, res, function (err) {
        res.statusCode = 404
        res.end('no such location')
      })
    }).listen(7777) //监听的端口, 在github上配置的Payload Url形式为 http://youtr_vps_ip:port
    
    handler.on('error', function (err) {
      console.error('Error:', err.message)
    })
    
    handler.on('push', function (event) {
      console.log('Received a push event for %s to %s',
        event.payload.repository.name,
        event.payload.ref);
      run_cmd('/bin/sh', ['/home/Evilmass.github.io/deploy.sh'], function(text){ console.log(text) }); //上调用所在目录下的deploy.sh脚本
        })
<br>
#### 创建同步脚本`deploy.sh`, 该脚本在VPS的主要操作如下 
> 等待被deploy.js调用
  kill heox-pid 关闭当前正在运行的hexo进程
  git-pull 得到仓库的更新文章
  hexo clean
  hexo generate #生成更新文章的页面
  hexo server & 重新启动hexo并在后台运行

<br>
##### deploy.sh
    #!/bin/bash
    PORT=4000
    WEB_PATH='/home/Evilmass.github.io' #Hexo目录
    WEB_USER='root' #用户
    WEB_USERGROUP='root' #用户组
    
    echo "Start deployment"
    cd $WEB_PATH
    echo "pulling source code..."
    git reset --hard origin/master
    #git clean -f 这一句请注释掉，否则服务器端的deploy.sh deploy.js在执行git pull操作时被删除
    git pull
    git checkout master
    echo "changing permissions..."
    chown -R $WEB_USER:$WEB_USERGROUP $WEB_PATH
    NUM=`ps -ef | grep 'hexo' | head -n1 | awk '{print$2}'` #请验证该行代码能否取出hexo进程的pid，若不能，则需要根据环境修改
    if [ -n "$NUM" ];then
        echo "kill hexo process pid: $NUM"
        kill -9 $NUM
    else
        echo "hexo process not found"
    fi
    HEXO_BASH=`which hexo`
    HEXO_CLEAN=${HEXO_BASH}" clean" 
    HEXO_GENERATE=${HEXO_BASH}" generate" #执行hexo generate命令
    HEXO_START_SERVER=${HEXO_BASH}" server -p $PORT &" #在后台启动hexo服务
    echo "HEXO_CLEAN: $HEXO_CLEAN"
    eval $HEXO_CLEAN
    echo "HEXO_GENERATE: $HEXO_GENERATE"
    eval $HEXO_GENERATE
    echo "HEXO_START_SERVER: $HEXO_START_SERVER"
    eval $HEXO_START_SERVER 
    echo "Finished."
    
启动  

    pm2 start deploy.js #启动服务
<br>
##### blog_run.sh
    #!/bin/bash
    NUM=`ps -ef | grep '/usr/bin/node /usr/lib/node_modules/forever/bin/monitor /home/Evilmass.github.io/deploy.js' | head -n 1 | awk '{print $2}'`
    if [ -n "$NUM" ];then
        echo "kill running_deploy process pid: $NUM"
        kill -9 $NUM #forever start多次会产生多个进程，需要kill pid
    else
        echo "running_deploy process not found"
    fi
    pm2 start /home/Evilmass.github.io/deploy.js 
    cd /home/Evilmass.github.io && hexo s &

##### 赋予脚本可执行的权限  

    chmod +x blog_run.sh
<br>

##### hexo_run.service

    [Unit]
    Description=Hexo Run
  
    [Service]
    Type=forking
    ExecStart=/bin/sh /home/blog_run.sh

    [Install]
    WantedBy=multi-user.target
    
使用

    systemctl start hexo_run  #启动服务  
    systemctl enable hexo_run #开机启动
如果多次修改hexo_run.service，那么启动服务的话可能出现如下内容
> Warning: hexo_run.service changed on disk. Run '**systemctl daemon-reload**' to reload units.

执行`systemctl daemon-reload`即可

<br>
##### crontab定时重启
`crontab -e`

    * 22 * * * systemctl restart hexo_run #每天晚上22点重启一次

**看到这里估计会很懵逼：这3个脚本一个套一个的，到底怎么工作**
1. `systemctl enable hexo_run`，VPS开机执行`blog_run.sh`
2. forever会守护`deploy.js`进程，监听push事件来调用`deploy.sh`
3. crontab每天重启一次`hexo_run`这个服务，再次执行`blog_run.sh`

<br>
### Github上的Webhooks设置
![webhook设置][webhook设置]

<br>
### 验证是否生效

#### 服务端启动脚本

    sh blog_run.sh
<br>
##### 本地执行

    hexo new Auto-Push
    sh sync.sh

<br>
####  查看Webhook Response和站点是否更新文章
![webhook设置成功][webhook设置成功]
<br>
![文章更新成功][文章更新成功]
<br>

**Done!**（Webhooks配置这里没搞懂工作原理所以浪费超多时间(╯' - ')╯（┻━━┻

<br>
### GZip

`vim /etc/nginx/nginx.conf`
    
    http {
        gzip on; #开启Gzip
        gzip_min_length  1k;  #当返回内容大于此值时才会使用gzip进行压缩,以K为单位,当值为0时，所有页面都进行压缩
        gzip_buffers     4 16k;  #gzip文件缓存大小
        gzip_http_version 1.0;
        gzip_comp_level 6;  #gzip压缩等级，数值越高压缩得越狠，也越占资源
        gzip_types  text/plain application/x-javascript text/css application/xml;  #gzip压缩文件格式，以下涵盖了一般所需的类型
        gzip_vary on;  #跟Squid等缓存服务有关，on的话会在Header里增加"Vary: Accept-Encoding"
        gzip_types         text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript;
    }
  
<br>
###  [**在Centos配置ngrok**][20]

<br>
### [**服务器监控 UptimeRobot 简明使用手册**][12]

<br>
### 备份

> Hexo根目录下的`_config.yml`

> 要使用的主题目录下的`_config.yml`
  
> 保存文章的`md`文件
  
> 本地推送的脚本`sync.sh`
  
> 服务器端的`deploy.sh` `deploy.js` `blog_run.sh`

#### 七牛qshell
创建一个私有空间（服务器在国外建议选择北美的空间，已经有空间的，先创建再设置成私有即可），在个人面板 -> 密钥管理找到  `AccessKey`和`SecretKey`

##### 下载服务器可用的[qshell][qshell下载地址]并初始化

    ./qshell_linux_amd64 init AK SK

##### 在当前目录下创建`config.txt`并写入如下内容

    {
        "src_dir"            :   "待同步的目录",
        "access_key"         :   "AK",
        "secret_key"         :   "SK",
        "bucket"             :   "私有空间名",
        "zone"               :   "na0",
        "rescan_local"       :   true,
        "skip_path_prefixes" :   ".qshell"
    }
各机房对应的zone值

    华东      nb
    华北      bc
    华南      hn
    北美      na0
##### 上传备份文件到私有空间
一般选择source/_posts里面的文章做备份，配置文件_config.yml不建议上传

    ./qshell_linux_amd64 qupload config.txt

##### 写成脚本最后加入到crontab一天更新一次
    * 22 * * * sh ~/hexo-backup/backup2qiniu.sh
    
##### 更多细节请参考[qshell官方文档][qshell官方文档]

 <br>
#### Github + Webhooks
这个简单，创建**私有仓库**，设置好webhooks，一旦github有push evevt就执行备份操作

代码？**自己动手操作一次吧**（记得用pm2）～

<br>
### **UPDATE**
既然wenhook关键是要调用那个deploy.sh，为何不在本地`sync.sh`写入一行
> ssh root@host -p port 'sh ./deploy.sh'  

-p 代表端口，默认22， 引号内代表连接上服务器后执行的命令，**需要用ssh-keygen实现免密码登录**

那么整个工作流就很简单了： 开启启动hexo服务`pm2 startup`，crontab定时执行`pm2 restart app`，本地`sync.sh`推送，服务器端实时更新文章～
#### 在Shell中执行Ctrl - C

    CTRL-A \001   十进制1
    CTRL-B \002   十进制2
    ....
    CTRL-Z \032   十进制26
    那么`echo -e "\003"` 代表在Shell中输入Ctrl - C

添加`echo -e "\003"`到deploy.sh最后就可以达到本地推送完成之后自动退出VPS的Shell连接

<br>
> **这个打赏二维码好像有什么不对**

**支付宝** 
![alipay][99]

**微信**  
![wechat][100]


  [1]: http://evil0mass.tk
  [2]: http://evil0mass.me
  [3]: http://evilmass.xyz
  [4]: http://evilmass.cc
  [5]: http://evilmass.tk
  [6]: http://evilmass.ml
  [7]: http://evilmass.cn
  [8]: https://github.com/iMeiji/shadowsocks_install/wiki/shadowsocks-optimize
  [9]: https://github.com/iMeiji/shadowsocks_install/wiki/%E5%BC%80%E5%90%AFTCP-BBR%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6%E7%AE%97%E6%B3%95
  [10]: https://blog.kuoruan.com/110.html
  [11]: https://www.91yun.org/archives/2775
  [12]: https://liyuans.com/archives/uptimerobot.html
  [13]:  https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git
  [14]: https://help.github.com/articles/connecting-to-github-with-ssh/
  [15]: http://nginx.org/en/docs/install.html
  [16]: https://nodejs.org/en/download/current/
  [17]: https://hexo.io
  [18]: http://theme-next.iissnan.com/getting-started.html
  [19]: https://ksmx.me/letsencrypt-ssl-https/
  [20]: https://evilmass.cc/2017/01/25/%E5%9C%A8CentOS%E4%B8%8B%E9%85%8D%E7%BD%AEngrok/
  [Git-Bash]: https://of4jd0bcc.qnssl.com/Hexo/Git-Bash.png
  [webhook设置]: https://of4jd0bcc.qnssl.com/Hexo/webhook%E8%AE%BE%E7%BD%AE.png
  [webhook设置成功]: https://of4jd0bcc.qnssl.com/Hexo/webhook%E8%AE%BE%E7%BD%AE%E6%88%90%E5%8A%9F.png
  [文章更新成功]: https://of4jd0bcc.qnssl.com/Hexo/%E6%96%87%E7%AB%A0%E6%9B%B4%E6%96%B0%E6%88%90%E5%8A%9F.png
 [qshell下载地址]: https://developer.qiniu.com/kodo/tools/qshell#download
 [qshell官方文档]: https://github.com/qiniu/qshell/blob/master/docs/qupload.md

  [99]: https://of4jd0bcc.qnssl.com/Blog/%E6%89%93%E8%B5%8F/alipay/%E7%86%8A%E6%9C%AC%E7%86%8A%E8%B6%85%E5%B8%85_alipay.gif?imageView2/1/w/200/h/200
  [100]: https://of4jd0bcc.qnssl.com/Blog/%E6%89%93%E8%B5%8F/wechat/girl_wechat.gif?imageView2/1/w/200/h/200