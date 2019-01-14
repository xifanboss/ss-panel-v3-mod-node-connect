

## 使用方法

先在面板中创建节点，并记住节点的id；不会的看下方链接，去看我写的原文

然后执行脚本

```shell
yum -y install wget &&
wget -N --no-check-certificate https://raw.githubusercontent.com/lizhongnian/ss-panel-v3-mod-node-connect/master/ss-panel-v3-mod-node-connect.sh &&
chmod +x ss-panel-v3-mod-node-connect.sh &&
bash ss-panel-v3-mod-node-connect.sh
```
后端与前端可以在同一个服务器，也可以在不同的服务器。后端是真正提供代理服务的地方。

安装后端
首先找一个文件夹来存放程序。这里直接用 root 了。先安装一下必须的工具。

cd /root
yum install python-setuptools && easy_install pip
yum install git
然后安装 libsodium。

libsodium 用于对 chacha20 加密方式提供支持。此加密适用于路由器、手机等性能欠佳的设备。可以提高速度减少耗电。

#安装所需的组件包
yum -y groupinstall "Development Tools"
#获取最新版本号
Libsodiumr_ver=$(wget -qO- "https://github.com/jedisct1/libsodium/tags"|grep "/jedisct1/libsodium/releases/tag/"|head -1|sed -r 's/.*tag\/(.+)\">.*/\1/') && echo "${Libsodiumr_ver}"
#下载编译安装
wget --no-check-certificate -N "https://github.com/jedisct1/libsodium/releases/download/${Libsodiumr_ver}/libsodium-${Libsodiumr_ver}.tar.gz"
tar -xzf libsodium-${Libsodiumr_ver}.tar.gz && cd libsodium-${Libsodiumr_ver}
./configure --disable-maintainer-mode && make -j2 && make install
echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
ldconfig
#完成后可以删除下载和解压的文件了
cd .. && rm -rf libsodium-${Libsodiumr_ver}.tar.gz && rm -rf libsodium-${Libsodiumr_ver}
其他系统的安装以及常见问题参见原文逗比根据地的教程。

然后下载真正的后端代码：

 git clone -b manyuser https://github.com/glzjin/shadowsocks.git
进入到源码目录安装依赖：

cd shadowsocks
yum -y install python-devel
yum -y install libffi-devel
yum -y install openssl-devel
pip install -r requirements.txt
安装依赖出错

如果出现 Can not uninstall 'requests'... 那么尝试强制重新安装 requests 为需要的版本：pip install -I requests==2.13.0
安装依赖出错

初始化配置文件：

cp apiconfig.py userapiconfig.py
cp config.json user-config.json
下面需要编辑一下 userapiconfig.py。你可以使用宝塔面板的文件管理功能编辑，或者使用vi，或者使用其他方式。

API_INTERFACE 改为 glzjinmod。
NODE_ID 改为 3。
MySQL_* 正确填写数据库连接信息。注意：此处应该填写前端所用的数据库而不是新建一个。MYSQL_SSL_* 一般无需填写。
NODE_ID 只有在首次安装 panel 时才是3。具体值与 panel 设置有关，下文会介绍。

最后执行 python server.py 就可以运行了。这样启动在退出 shell 后会自动停止。一般用于测试，看看是否正常启动。如果没有报错，按 ctrl+c 退出，然后执行 ./run.sh 即可后台启动并保持运行。

执行 ./stop.sh 可以停止运行。

到此我们后端就安装好了。下面就要在 panel 中添加这个节点。

添加节点
打开 panel 登录管理员账户。点击左下角的 管理面板-节点列表。点击右下角的 + 添加节点。

节点名称必须是 xxx - Shadowsocks 的格式（注意前后空格）。
节点地址就是 ss 服务器的地址，可以是域名。
节点IP留空就好。
流量比例必填，搞不清楚填1就行。也就是用多少计算多少。
其他配置看着填就好了。
添加后在节点列表就可以看见了。

注意： 这里生成一个 ID，这个 ID 就是之前配置后端的时候填写的。所以必须保证这两个 ID 相同才能成功通讯。

如果连不上注意看看是不是防火墙把端口封上了哦。
