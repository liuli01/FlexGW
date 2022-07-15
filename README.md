Flex GateWay
============

介绍
-------

本程序提供了VPN、SNAT 基础服务。

主要提供以下几点功能：

1.  IPSec Site-to-Site 功能。可快速的帮助你将两个不同的VPC 私网以IPSec Site-to-Site 的方式连接起来。
2.  拨号VPN 功能。可让你通过拨号方式，接入VPC 私网，进行日常维护管理。
3.  SNAT 功能。可方便的设置Source NAT，以让VPC 私网内的VM 通过Gateway VM 访问外网。

更新内容
-------
更改适配操作系统7.x，测试编译环境为centos 7.3

依赖软件包可以直接使用系统epel源安装

yum install -y epel-release

yum install strongswan openvpn

附上打包好的rpm包[下载地址]()

自己编译
```
# yum install git
# git clone https://github.com/tomlinux/FlexGW
# cd FlexGW/packaging/rpm/
# sh mkrpm.sh
```

部署参照原文档 [Deploy.md](https://github.com/tomlinux/FlexGW/blob/master/packaging/rpm/Deploy.md)

软件组成
----------

Strongswan

* 版本：5.1.3 => 更新为 5.7.2
* Website：http://www.strongswan.org


OpenVPN

* 版本：2.3.2 => 更新为 2.4.6
* Website：https://openvpn.net/index.php/open-source.html

程序说明
-----------

ECS VPN（即本程序）

* 目录：/usr/local/flexgw
* 数据库文件：/usr/local/flexgw/instance/website.db
* 日志文件：/usr/local/flexgw/logs/website.log
* 启动脚本：/etc/init.d/flexgw 或/usr/local/flexgw/website_console
* 实用脚本：/usr/local/flexgw/scripts

保留原来的flexgw的启动方式 service flexgw start/stop/restart

「数据库文件」保存了我们所有的VPN 配置，建议定期备份。如果数据库损坏，可通过「实用脚本」目录下的initdb.py 脚本对数据库进行初始化，初始化之后所有的配置将清空。

Strongswan

* 目录：/etc/strongswan
* 日志文件：/var/log/strongswan.charon.log
* 启动脚本：/usr/sbin/strongswan

如果strongswan.conf 配置文件损坏，可使用备份文件/usr/local/flexgw/rc/strongswan.conf 进行覆盖恢复。

ipsec.conf 和ipsec.secrets 配置文件，由/usr/local/flexgw/website/vpn/sts/templates/sts 目录下的同名文件自动生成，请勿随便修改。

OpenVPN

* 目录：/etc/openvpn
* 日志文件：/etc/openvpn/openvpn.log
* 状态文件：/etc/openvpn/openvpn-status.log
* 原启动脚本：/etc/init.d/openvpn

OpenVPN启动方式

位置变更为 /usr/lib/systemd/system/openvpn-server@.service

启动命令为systemctl start openvpn-server@server.service(会读取/etc/openvpn/server/server.conf配置文件)

server.conf 配置文件，由/usr/local/flexgw/website/vpn/dial/templates/dial 目录下的同名文件自动生成，请勿随便修改。

Docker 镜像

```
docker run  --name flexgw6  -p 4443:443  -d   willclond/flexgw:v1
```
访问地址：https://ip:4443 
# 构建到阿里云
