# [CTF BugKu] telnet

题目信息
 
题目名称：telnet

题型：Misc 流量分析

考点：Telnet协议明文传输、Wireshark流量包分析

题目描述：下载压缩包，解压得到 networking.pcap 流量抓包文件，分析流量获取flag。
 
原理
 
Telnet协议最大特点：所有传输内容明文发送，没有加密。登录账号、密码全部直接在网络数据包里可见，抓包后可以直接读取交互内容。
提示词“随波逐流”对应的解题方法就是Wireshark里的追踪TCP流，顺着TCP数据流，把零散的数据包合并成完整会话，这就是“随波逐流”的含义。
 
手动解题步骤（使用随波逐流方法）
 
1. 下载题目压缩包，解压，得到  networking.pcap  文件。

2. 打开Wireshark软件，将pcap流量包拖入软件。

3. 在Wireshark上方过滤栏输入  telnet ，按下回车，只筛选telnet相关数据包。

4. 在筛选结果里，任意选中一条Telnet数据包，鼠标右键。

5. 选择【追踪】→【TCP流】，弹出TCP流窗口。这一步就是题目提示的随波逐流：不再单独看每一个零散数据包，跟随整条TCP数据流，还原完整交互。

6. 在TCP流窗口内，直接查看Telnet登录交互记录。

可以看到登录账号，以及Password字段后面的flag。
 
补充备选方法（不使用追踪TCP流）
 
直接在Wireshark查找字符串：在查找功能搜索  flag{ ，定位flag所在数据包。
也可以使用strings命令读取pcap文件，匹配flag关键词。
 
题目交互内容（真实）
 
plaintext
  
Ubuntu 12.04 LTS

hockeyinjune-virtual-machine login: ccssaaw

Password: flag{d316759c281bf925d600be698a4973d5}
 
 
Flag
 
flag{d316759c281bf925d600be698a4973d5}
 
踩坑记录
 
1. Telnet是逐个字符发包，一个数据包只传输一个字母，不要逐个包手动拼接。题目提示“随波逐流”就是提醒我们使用追踪TCP流功能，一次性合并全部会话。
2. 不要混淆源IP和目的IP，只看客户端发送的输入字符。
3. 对比SSH：SSH流量加密，无法直接读取内容；Telnet明文，这是本题核心考点。
 
总结
 
本题考察Telnet明文传输的特性。题目给出提示“随波逐流”，对应的解题方法就是追踪TCP流。打开流量包，筛选telnet数据包，追踪TCP流，跟随整条数据流查看完整会话，直接读取登录会话中的密码，密码就是flag。
