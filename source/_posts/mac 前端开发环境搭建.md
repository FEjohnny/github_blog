---
title: mac 前端开发环境搭建
date: 2018-08-19 16:29:15
tags: [mac]
---

&emsp;&emsp; 最近，公司为我们前端配置了超级高配的IMAC，so，又需要重新搭建开发环境啦。。。之前在用自己的mac pro的时候，搭建开发环境都是各种百度，各种google的，没有成体系一步到位的东西。所有自己东拼西凑，搞出了如下的东西，对于我个人来说，足够l

<!--more-->

&emsp;&emsp;MacOS下web开发环境的搭建相对来说是比较简单的。本文除了搭建常见的web开发环境（Nginx + PHP + Node.js）外，还将简单介绍一些MacOS的基本设置以及基础设施。

# 系统设置

&emsp;&emsp;MacOS的系统设置统一在左上角的苹果菜单，“系统偏好设置”中。下文提及的系统设置项均以此作为入口。

![](/images/32.png)

&emsp;&emsp;这个界面上方的左右按钮可以类比为浏览器的“后退”“前进”按钮。当进入某个设置项后，可以通过按钮返回主界面。

# 始终显示扩展名

+ 在Dock上点击Finder（访达）
+ 屏幕左上角苹果菜单旁边，点击Finder（访达），进入“偏好设置”
+ 在“高级”中勾上“显示所有文件扩展名”

# 安装homebrew

&emsp;&emsp;homebrew是MacOS的第三方包管理软件，它可以直接从命令行安装很多软件，避免自己下源码、找依赖、编译的尴尬和麻烦。homebrew还有cask插件，可以安装图形界面软件，常用的软件都能直接下载安装到。

## 安装xcode cli tools

&emsp;&emsp;xcode command line tools是苹果出的一套命令行工具，它包含了在MacOS进行开发工作时必要的基础工具，包括git/make/clang等等。因此在安装其它工具之前，需要最先将它安装好。

&emsp;&emsp;首先打开terminal，方法有三种：

+ 在dock（也就是屏幕最下方那一条）中点击Launchpad（启动台），然后点击“其他”，点击“terminal（终端）”打开
+ 在屏幕右上角点击放大镜图标，或者按cmd + 空格打开spotlight，然后输入“终端”或者“terminal”打开
+ 在dock中点击Finder（访达），然后点击左侧“应用程序”，点击“实用工具”，找到“终端”，双击打开

&emsp;&emsp;接下来在终端中输入：

```
xcode-select --install
```

&emsp;&emsp;接着会弹出一个窗口，在窗口中点击安装，静待安装完成即可。

## 安装homebrew

&emsp;&emsp;在命令行中输入以下命令，会自动下载并安装homebrew

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

&emsp;&emsp;安装完成后可以使用brew命令来完成后续的环境安装。

# 安装命令行软件

## NVM & Node.js

&emsp;&emsp;NVM是一个用来安装/卸载/切换不同版本Node.js的工具，我们使用homebrew来安装它。

```
brew install nvm
```

&emsp;&emsp;光安装完之后，如果不做其它的处理，下次当你打开命令行的时候，nvm其实是无法正常运行的，因此还需要将一些命令放入终端初始化文件~/.bash_profile中。

        ~的意思就是home目录，即当前用户的个人目录，它的真实路径位于/Users/{用户名}。

```
# 在命令行执行以下代码，将三行命令写入~/.bash_profile
echo "export NVM_DIR=\"\$HOME/.nvm\"">>~/.bash_profile && \
echo ". \"/usr/local/opt/nvm/nvm.sh\"">>~/.bash_profile && \
echo "export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node">>~/.bash_profile && \
# 在当前命令行执行该文件
. ~/.bash_profile
```

接下来使用nvm安装Node.js，目前使用8.x即可。

```
# 安装Node
nvm install 8
```

然后我们设置一下镜像为内部npm源，同时安装一下gulp。

```
# 设置npm镜像
npm set registry=http://registry.npm.oa.com && \
# 安装Gulp
npm install -g gulp@3
```

## PHP 7.1

        MacOS自带一个PHP，但我们不用那个，而是自己安装一个版本。

```
brew install php@7.1
```

由于依赖比较多，安装会花一定的时间，请待心等待。安装完成后，需要做一些设置：

+ 为php.ini添加两段配置
+ 将php 7.1所在的路径加到环境变量PATH中去，以便访问php/php-fpm/phpize时不会访问到系统自带的php
+ 启用php的服务，以便后续可以开机自启动

```
sudo sh -c "echo \"site_location=\\\"ml\\\"\">>/usr/local/etc/php/7.1/php.ini" && \
sudo sh -c "echo \"site_idc=\\\"local\\\"\">>/usr/local/etc/php/7.1/php.ini" && \
echo 'export PATH="/usr/local/opt/php@7.1/bin:$PATH"' >>~/.bash_profile && \
echo 'export PATH="/usr/local/opt/php@7.1/sbin:$PATH"' >>~/.bash_profile && \
brew services start php@7.1
. ~/.bash_profile
```

## PHP插件protobuf & redis

由于homebrew不再提供PHP插件的安装，因此需要自己下载编译，直接运行以下脚本即可：

```
# php71-redis插件
mkdir -p ~/work/conf/php-ext/php71-redis/src-4.0.2 && \
mkdir ~/work/conf/php-ext/php71-redis/4.0.2 && \
cd ~/work/conf/php-ext/php71-redis/src-4.0.2/ && \
curl -o redis-4.0.2.tgz http://pecl.php.net/get/redis-4.0.2.tgz && \
tar xvf redis-4.0.2.tgz && \
cd redis-4.0.2 && \
phpize && \
./configure && make && \
cp modules/redis.so ~/work/conf/php-ext/php71-redis/4.0.2/ && \
echo "[redis]">>/usr/local/etc/php/7.1/conf.d/ext-redis.ini && \
echo "extension = /Users/$(whoami)/work/conf/php-ext/php71-redis/4.0.2/redis.so">>/usr/local/etc/php/7.1/conf.d/ext-redis.ini
```

```
# php71-protobuf插件
mkdir -p ~/work/conf/php-ext/php71-protobuf/src-0.12.3 && \
mkdir ~/work/conf/php-ext/php71-protobuf/0.12.3 && \
cd ~/work/conf/php-ext/php71-protobuf/0.12.3/ && \
git clone https://github.com/allegro/php-protobuf && \
cd php-protobuf/ && \
phpize && \
./configure && make && \
cp modules/protobuf.so ~/work/conf/php-ext/php71-protobuf/0.12.3/ && \
echo "[protobuf]">>/usr/local/etc/php/7.1/conf.d/ext-protobuf.ini && \
echo "extension = /Users/$(whoami)/work/conf/php-ext/php71-protobuf/0.12.3/protobuf.so">>/usr/local/etc/php/7.1/conf.d/ext-protobuf.ini
```

最后，重启php服务

```
brew services restart php@7.1
```

可以通过php -m来验证插件是否安装成功，如果看到有protobuf和redis，表示安装成功。

后续PHP（其实是php-fpm）的启动/停止/重启，既可以使用homebrew的service，也可以直接操作php-fpm

使用homebrew：

```
# 启动
brew services start php@7.1
# 停止
brew services stop php@7.1
# 重启
brew services restart php@7.1
```

直接操作进程：

```
# 启动
php-fpm
# 停止
killall php-fpm
# 重启
killall php-fpm && php-fpm
```

## nginx

直接使用homebrew安装nginx

```
brew install nginx
```

后续nginx的启动/停止/重启等：

```
# 测试配置文件是否有误
sudo nginx -t
# 启动
sudo nginx
# 停止
sudo nginx -s stop
# 重新加载配置文件
sudo nginx -s reload
# 重启
sudo nginx -s stop && nginx
```

值得注意的点：
+ sudo nginx -t会检查配置文件是否有错误，请仔细查看输出，确保配置文件没有错误
+ nginx在启动时会生成一个pid文件，同时（一般情况下）监听80和443端口
+ 如果提示80/443端口已经被使用（nginx: [emerg] bind() to 0.0.0.0:80 failed (48: Address already in use)），则很可能你已经启动了nginx，无须重新启动
+ 如果提示nginx: [error] open() "/usr/local/var/run/nginx.pid" failed (2: No such file or directory)，则说明这个命令是要求nginx处于启动状态，而目前nginx并没有启动（例如sudo nginx -s reload）
+ 如果没有任何回显，说明命令执行成功了

        因为nginx需要监听80和443端口，而在unix类（linux/MacOS都属于这一类）系统中，1024号以下的端口都属于特权端口，因此需要root权限或者sudo权限才能操作。


# 配置

## 命令行基本配置

首先建议添加一些命令别名，例如ll：

```
# 设置ll别名
echo "alias ll=\"ls -al\"">>~/.bash_profile && \
. ~/.bash_profile
```

## 创建/软链项目目录

为了后续配置方便，我们将PHP & nginx的配置文件软链到工作目录~/work/conf

```
mkdir -p ~/work/conf && \
ln -s /usr/local/etc/nginx/nginx.conf ~/work/conf/nginx.conf && \
ln -s /usr/local/etc/nginx/servers ~/work/conf/nginx_servers && \
ln -s /usr/local/etc/php/7.1/php.ini ~/work/conf/php.ini && \
ln -s /usr/local/etc/php/7.1/conf.d ~/work/conf/php_conf.d
```

提前建立好nginx日志目录

```
mkdir ~/work/conf/logs
```

## 配置SSH私钥/公钥

Git默认支持两种协议：HTTP(S)和SSH。在条件允许的情况下，我们都使用SSH协议。

首先我们需要生成一个私钥/公钥对。该操作在整个iMac使用过程中只需要进行一次（除非你手动把私钥/公钥删除或者破坏了）。

```
ssh-keygen -t rsa
```

然后一路回车即可。

运行完后会在~/.ssh目录下生成两个文件：

+ id_rsa 私钥，不要泄漏给任何人
+ id_rsa.pub 公钥，可以到处分发、张贴

## 搭建nginx + php站点

由于我们刚刚已经配置了PHP & nginx的软链，所以现在我们又新的项目需要启动时，只需要在～/work/conf/nginx_servers目录下新建我们项目的nginx配置即可。也可以直接执行命令行：

```
cat <> ~/work/conf/nginx_servers/xxx.conf
server
{
    listen      80;
    listen       443 ssl;
    server_name  你的访问域名
    index index.html index.htm index.php;
    root  你的项目路径

    #ssl             on;
    ssl_certificate  /Users/$(whoami)/work/conf/https/xx.com.crt;
    ssl_certificate_key  /Users/$(whoami)/work/conf/https/xxx.com.key;

    error_log /Users/$(whoami)/work/conf/logs/error_xxx.com.log;
    access_log /Users/$(whoami)/work/conf/logs/access_xxx.com.log combined;

    if (\$host ~* ^xxx\.com$)
    {
        rewrite ^/(.*)$ https://www.\$host\$request_uri  permanent;
        break;
    }



    location /
    {
        if (-f \$request_filename)
        {
            expires 7200;
            break;
        }
        if (!-f \$request_filename)
        {
            rewrite ^(.*)$ /index.php last;
            break;
        }

    }
    set \$allowOrigin "";
    if (\$host ~* ^cdn\.xxx\.com$) {
       set \$allowOrigin "*";
    }
    add_header Access-Control-Allow-Origin \$allowOrigin;
    location ~.*\.php$
    {

        if (!-f \$request_filename)
        {
            rewrite ^(.*)$ /index.php last;
            break;
        }

        include fastcgi.conf;
        fastcgi_param  HTTPS on;
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_index index.php;

        break;
    }

}

EOT
```

        你也可以手工复制创建这个配置文件。记得前面说的，使用nginx -t来确保配置文件没有问题。

        请仔细检查所有的路径，包括root，证书路径，`日志路径等等。

然后启动nginx（之前没启动），或者重新加载nginx配置（之前已启动）。

```
# 启动
sudo nginx
# 重新加载配置
sudo nginx -s reload
```

至此，你的站点已经可以跑起来了。 

## 如何配置hosts

我们访问网站时都会使用域名，例如xxx.xxx.com，但是计算机通讯时使用的是IP地址，因此需要有一个域名解析的过程，即查找xxx.xxx.com对应哪个IP。又因为我们的开发环境搭建在本机，因此会希望让xxx.xxx.com指向本机IP（127.0.0.1），hosts就是用来干这个的。

Mac系统的hosts文件路径位于/etc/hosts，这是一个没有后缀的文件。而默认情况下，我们是没有权限去修改hosts文件的，因此首先我们要修改一下它的权限：

```
# 为hosts加上可写权限，否则改不了hosts文件
sudo chmod g+w /etc/hosts
```

接下来我们就可以用任意编辑器打开/etc/hosts了，它的格式是这样：

```
# 一行一个，井号开头的为注释
# IP在前 域名在后，使用空格/tab分隔，域名可以有多个
127.0.0.1    xxx.xxx.com my.xxx.com cdn.xxx.com
```

但是手工修改hosts很不方便，因此我们使用一款名为switchhosts!的软件来修改。首先使用homebrew cask来安装：

```
# hosts切换工具
brew cask install switchhosts
```

安装完成后打开，即可看到界面，左侧最上方的“系统hosts”是所有方案组合下来最终的hosts，是只读的，不可以修改。在下方可以自己建很多方案（例如以系统为单位来设），每个方案可以独立开关。

值得注意的是，localhost这个域名在MacOS中并不是天然存在的，也需要配置hosts，所以在切方案的时候一定别把这个域名弄丢了，否则可能会有奇怪的事情发生：

```
127.0.0.1    localhost
255.255.255.255    broadcasthost
::1             localhost 
fe80::1%lo0    localhost
```

## 配置Node.js环境

Node.js其实本身没有什么可配置的，只需要在package.json所在的目录安装好依赖，然后执行对应的脚本即可。

但是如果需要本机域名访问的话，同样需要在nginx中进行反向代理，一般情况下nginx配置如下。注意这一段是nginx配置文件，直接复制，然后在~/work/nginx_servers下新建一个文件，例如xxx.conf，然后复制下方配置：

```
server
{
    listen 80;
    # listen 443 ssl;
    # listen 443 ssl http2;
    server_name  xxx.xxx.com;

    error_log /Users/chenjun/work/logs/error_xxx.xxx.com.log;
    access_log /Users/chenjun/work/logs/access_xxx.xxx.com.log combined;

    location /
    {
        # 这里是重点，反向代理到Node.js监听的端口
        proxy_pass http://localhost:14001/;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $remote_addr;
    }
}
```

具体配置项请灵活调整，如果需要HTTPS的，可以将listen 443 ssl http2的注释掉，然后加上HTTPS证书相关的配置。

# 其它软件安装

其它软件的安装，可以使用homebrew的cask插件，也可以自己下载dmg包，然后如下操作：

+ 打开.dmg文件，弹出一个框
+ 在框中将应用程序拖到Applications中

下面列一些homebrew安装常见的软件的命令，可根据需要自己选用：

```
# 通用软件
# 微信
brew cask install wechat
# qq
brew cask install qq
# 企业微信
brew cask install wxwork
# 压缩/解压工具，用它压缩发给windows不乱码
brew cask install keka
# Chrome
brew cask install google-chrome

# 开发工具
# vscode
brew cask install visual-studio-code
# sublime text
brew cask install sublime-text
# Git Gui工具sourcetree
brew cask install sourcetree
# 屏幕取色工具sip
brew cask install sip
# 抓包工具wireshark
brew cask install wireshark
# Mysql图形化客户端
brew cask install sequel-pro
# 图片压缩工具
brew cask install imageoptim

# 收费软件
# diff/merge工具
brew cask install beyond-compare
# 窗口大小管理工具（收费）
brew cask install moom
# sketch设计软件（收费）
brew cask install sketch
```

## iTerm2

iTerm2是一个终端工具，可以替代系统自带的终端，并支持使用sz/rz直接从服务器上上传下载文件。如果经常操作服务器的话，推荐安装。

```
# 终端工具iTerm2，支持sz/rz
brew cask install iterm2 && \
brew install lrzsz && \
sudo curl -o /usr/local/bin/iterm2-send-zmodem.sh https://raw.githubusercontent.com/mmastrac/iterm2-zmodem/master/iterm2-send-zmodem.sh && \
sudo chmod +x /usr/local/bin/iterm2-send-zmodem.sh && \
sudo curl -o /usr/local/bin/iterm2-recv-zmodem.sh https://raw.githubusercontent.com/mmastrac/iterm2-zmodem/master/iterm2-recv-zmodem.sh && \
sudo chmod +x /usr/local/bin/iterm2-recv-zmodem.sh
```

还需要在iTerm2中做一下配置，才能支持zmodem。

+ 打开iTerm2，点击屏幕最左上角，苹果菜单右侧“iTerm2”菜单，进入“Preferences”
+ 点击 Profile ，Advanced ，Triggers下面的Edit（见下方图片）
+ 在弹窗中添加两行

|Regular Expression|Action|Parameters|Instant|
|:-:|:-:|:-:|:-:|
|rz waiting to receive.\*\*B0100|Run Silent Coprocess|/usr/local/bin/iterm2-send-zmodem.sh|false|
|\*\*B00000000000000|Run Silent Coprocess|/usr/local/bin/iterm2-recv-zmodem.sh|false|

# 其它及注意事项

## 编辑器插件

一定要安装的插件：

+ EditorConfig
+ ESLint

如果使用vscode，直接安装启用即可。如果使用sublime text，需要先安装package control：

+ 打开sublime text，按ctrl + `（ESC下方，数字1左边）键，打开console
+ 在console中输入以下代码，安装package control
+ 按cmd + shift + p，找到package control:install package
+ 依次安装好EditorConfig / SublimeLinter / SublimeLinter-EsLint
+ 然后需要在SublimeLinter的配置中指定Node的路径：点击苹果菜单旁的Sublime Text，Preferences，Package Settings，SublimeLinter，Settings

参与以下配置内容：

```
// SublimeLinter Settings - User
{
    "debug": false,
    "paths": {
        "osx": ["/Users/cynthiading/.nvm/versions/node/v8.11.3/bin/"]
    },
    "linters": {
        "eslint": {
            "env": {
                "PATH": "/Users/cynthiading/.nvm/versions/node/v8.11.3/bin/"
            }
        }
    },
    "show_panel_on_save":"view"
}
```

## 设置默认浏览器

+ 进入系统偏好设置，“通用”设置
+ 选择默认浏览器

## 关于破解软件

本教程不传播破解软件，有需要请自行寻找。给一条提示，如果软件被重新打包过（一般破解会重新打包），则系统会不信任该软件，提示损坏，无法运行。可以通过系统设置改变这一行为：

首先关掉系统的保护：

```
sudo spctl --master-disable
```

+ 然后打开“系统偏好设置”，“安全性与隐私”设置
+ 点左下角的锁图标，进行解锁
+ 将“允许从以下位置下载的应用”改为“任何来源”

    有部分地方下载的软件会提示不安全无法打开，此时可以通过右键打开，此时会弹一个框确认，确认过之后就能打开。

## 彩蛋

很多教程会推荐将默认Shell改为zsh，如果你想切换的话可以这么做：

```
chsh -s /bin/zsh
echo "source ~/.bash_profile">>~/.zshrc
```

zsh还有一些插件，比如oh my zsh：

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

安装完后可以在~/.zsh中自己配置主题、插件。（推荐git插件，可以补全Git命令，包括当前的分支名之类的）

autojump是一个可以根据命令行访问的文件夹历史进行快速跳转的工具。例如你经常访问xxx的目录，下次再访问的时候直接j xxx就可以了，不用再输入完整路径，推荐使用。

```
brew install autojump
echo "[ -f /usr/local/etc/profile.d/autojump.sh ] && . /usr/local/etc/profile.d/autojump.sh">>~/.zshrc
. /usr/local/etc/profile.d/autojump.sh
```

# 完
Enjoy working with your Mac.