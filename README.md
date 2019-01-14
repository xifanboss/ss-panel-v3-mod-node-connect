# ss-panel-v3-mod-node-connect
用于ss-panel-v3-mod面板的节点对接一键脚本，支持webapi和数据库对接

## 使用方法

先在面板中创建节点，并记住节点的id；不会的看下方链接，去看我写的原文

然后执行脚本

```shell
yum -y install wget &&
wget -N --no-check-certificate https://raw.githubusercontent.com/lizhongnian/ss-panel-v3-mod-node-connect/master/ss-panel-v3-mod-node-connect.sh &&
chmod +x ss-panel-v3-mod-node-connect.sh &&
bash ss-panel-v3-mod-node-connect.sh
```
