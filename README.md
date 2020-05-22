# WeChat-Data-Analysis

### 介绍
### 使用工具
- MacBook 10.15 Catalina (预先安装电脑版微信)
- iPhone
- Python 3.8 [(下载)](https://www.python.org/downloads/)
- DB Browser [(下载)](https://sqlitebrowser.org/dl/)

## 微信聊天记录备份与导出
MAC更新Catalina后取消了iTunes, 同步iphone数据需要用数据线连接iPhone和MacBook。打开访达，选择位置/iPhone，立即备份，具体操作如下图所示。

<div align=center><img width="800" height="400" src="https://github.com/allen1881996/WeChat-Data-Analysis/blob/master/pics/iphone%E5%90%8C%E6%AD%A5.png"/></div>

成功完成同步后，微信数据实际被储存在了加密的*.db文件中，也就是SQLite数据库文件。打开Terminal运行以下代码可以查看这些*.db文件。

`ls -alh ~/Library/Containers/com.tencent.xinWeChat/Data/Library/Application\ Support/com.tencent.xinWeChat/*/*/Message/*.db`

以其中一个文件地址为例，其格式如下:

`/Users/<User Name>/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/16671fd108bc2258e3dad6d83f7e75fb/Message/msg_0.db`

### 获取微信数据库密钥

1. 打开电脑端微信（不要登陆）
2. 在Terminal输入命令`lldb -p $(pgrep WeChat)`
3. 输入`br set -n sqlite3_key`，回车
4. 输入`c`，回车
5. 手机扫码登陆电脑端微信
6. 这时候电脑端微信是会卡在登陆界面的，不需要担心，回到Terminal
7. 输入`memory read --size 1 --format x --count 32 $rsi`，回车
8. 将返回的原始key粘贴到下面的字符串中，用这段Python代码获得密钥

```python
ori_key = """
0x60000241e920: 0xc2 0xf9 0x13 0xbe 0xda 0xe8 0x45 0x82
0x60000241e928: 0x93 0x94 0xsb 0xbf 0x61 0x86 0xd9 0xzf
0x60000241e930: 0xab 0xd3 0x0e 0xf0 0x39 0xcf 0x4c 0xba
0x60000241e938: 0x99 0x3a 0x01 0x05 0x2f 0xz5 0x2d 0xcd
"""

key = '0x' + ''.join(i.partition(':')[2].replace('0x', '').replace(' ', '') for i in ori_key.split('\n')[1:5])
print(key)
```

通过运行上面的程序可以得到一串微信数据库密钥如下：

`0xc2f913bedae845829394sbbf6186d9zfabd30ef039cf4cba993a01052fz52dcd`

这串密钥对于后续所有*.db文件是通用的。

### 查看数据库

打开DB Brower，按照下图所示选择raw key和加密设置，将上个部分得到的数据库密钥粘贴到密码位置。

<div align=center><img width="800" height="460" src="https://github.com/allen1881996/WeChat-Data-Analysis/blob/master/pics/%E6%89%93%E5%BC%80%E6%95%B0%E6%8D%AE%E5%BA%93.png"/></div>

打开数据库文件后，数据库结构如下图所示。

<div align=center><img width="800" height="460" src="https://github.com/allen1881996/WeChat-Data-Analysis/blob/master/pics/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%BB%93%E6%9E%84.png"/></div>

#### 
每一个Table代表你与一个人/群的聊天记录。

- mesLocalID：primary key，
- mesMesSvrID：服务端消息ID，
- msgCreateTime：消息创建时间（UTC+0）
- msgContent：消息内容（格式为普通文本或XML）
- msgStatus：消息状态（3表示发送出去的消息，4表示收到的消息）
- msgImgStatus：图片状态
- messgaeType：消息类型（1表示普通文本，3表示图片，34表示语音，43表示视频，47表示表情包，48表示位置，49是分享消息）
- msgSource：消息来源（仅针对收到的消息）



### 参考资源
1. [土办法导出 Mac 版微信聊天记录](https://www.v2ex.com/t/466053)
2. [iOS 微信的本地存储结构简析](https://daily.zhihu.com/story/8807166)
