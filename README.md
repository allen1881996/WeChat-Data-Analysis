# WeChat-Data-Analysis

### 介绍
### 使用工具
- MacBook 10.15 Catalina (预先安装电脑版微信)
- iPhone
- Python 3.8 [(下载)](https://www.python.org/downloads/)
- DB Browser  [(下载)](https://sqlitebrowser.org/dl/)

## 微信聊天记录备份与导出
MAC更新Catalina后取消了iTunes, 同步iphone数据需要用数据线连接iPhone和MacBook。打开访达，选择位置/iPhone，立即备份，具体操作如下图所示。
<div align=center><img width="800" height="400" src="https://github.com/allen1881996/WeChat-Data-Analysis/blob/master/pics/iphone%E5%90%8C%E6%AD%A5.png"/></div>
成功完成同步后，微信数据实际被储存在了加密的*.db文件中，也就是SQLite数据库文件。打开Terminal运行以下代码可以查看这些*.db文件。

`ls -alh ~/Library/Containers/com.tencent.xinWeChat/Data/Library/Application\ Support/com.tencent.xinWeChat/*/*/Message/*.db`

以其中一个文件地址为例，其格式如下:

`/Users/<User Name>/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/16671fd108bc2258e3dad6d83f7e75fb/Message/msg_0.db`

### 获取密钥

```python
ori_key = """
0x60000241e920: 0xc8 0xf9 0x93 0xbe 0xda 0xe8 0x45 0x82
0x60000241e928: 0x93 0x94 0xfb 0xbf 0x61 0x86 0xd9 0xff
0x60000241e930: 0xab 0xd3 0x0e 0xf0 0x39 0xcf 0x4c 0xba
0x60000241e938: 0x99 0x3a 0x01 0x05 0x8f 0xf5 0x4d 0xcd
"""

key = '0x' + ''.join(i.partition(':')[2].replace('0x', '').replace(' ', '') for i in ori_key.split('\n')[1:5])
print(key)
```
1. [土办法导出 Mac 版微信聊天记录](https://www.v2ex.com/t/466053)
