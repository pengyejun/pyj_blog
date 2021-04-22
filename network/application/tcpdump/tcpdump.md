### 前言

在学习tcpdump之前，抓包只停留在wireshark上，一次线上事故需要抓包时，由于没有wireshark软件，需要原生的tcpdump命令抓取数据包，这时候就触及到知识盲区了，因此写下此篇文章记录tcpdump命令常用语法。

### 参数解析

- -A：以ASCII格式打印每个数据包（不包括链路头）
- -b：以ASDOT表示法而不是ASPLAIN表示法在BGP数据包中打印AS号
- [-B size]：将操作系统捕获缓冲区大小设置为buffer_size，单位为KiB（1024字节）
- [-c count]：在捕获指定数量的数据包之后退出
- [-C file_size]：在将原始数据包写入保存文件之前，检查该文件当前是否大于file_size，如果是，关闭当前保存文件并打开一个新文件。第一个保存文件后的保存文件将具有用-w标志指定的名称，后面跟着一个数字，从1开始并继续向上。 file_size的单位是1,000,000字节(不是1,048,576字节)
- -d：将已编译的数据包匹配代码以人类可读形式转储到标准输出并停止。
- -dd：将数据包匹配代码转储为C程序片段
- -ddd：将数据包匹配代码转储为十进制数字（带计数）
- -D：等同于--list-interfaces, 打印系统上可用的网络接口列表以及tcpdump可以捕获数据包的列表。对于每个网络接口，都会打印一个编号和一个接口名称，可能后跟接口的文本描述。可以将接口名称或编号提供给-i标志以指定要捕获的接口。(对于linux系统可以直接用ifconfig -a)
- -e：打印链路层的头部, 例如Ethernet和IEEE 802.11协议中的MAC地址
- -E：与加密算法相关
- -f：用数字打印外部ip地址（不常用）
- [ -F file ]：使用文件作为过滤器表达式的输入. 在命令行上给出的附加表达式将被忽略.
- [-G rotate_seconds]：定时rotate保存的pcap文件，保存文件的名称由-w指定。
- -H：尝试检测802.11s草案网格标头
- [-i interface|--interface=interface]：指定监听的网卡
- -I|--monitor-mode：将接口置于“监视模式”，只支持某些操作系统上的IEEE 802.11 Wi-Fi接口. 可以通过iwconfig eth0 mode managed改回来。(不常用)
- --immediate-mode：在“立即模式”下捕包. 这个模式下, 数据包一到达就会被送给tcpdump. 当在terminal中打印包而不是在文件或者管道中时默认开启此模式.
- -j tstamp_type|--time-stamp-type=tstamp_type：将捕获的时间戳记类型设置为tstamp_type。 使用的名称时间戳类型在pcap-tstamp（7）中获取； 并非所有类型列出那里对于任何给定的接口都必须是有效的
- -J|--list-time-stamp-types：列出接口和出口支持的时间戳类型
- --time-stamp-precision=tstamp_precision：将捕获的时间戳记精度设置为tstamp_precision
- -K|-dont-verify-checksums：不验证IP，TCP或UDP校验和
- -l：缓冲行输出到标准输出，即不缓冲（eg：tcpdump -l | tee dat）
- -L|--list-data-link-types：列出指定接口下的已知数据链路类型
- -m module：从文件模块加载SMI MIB模块定义。 可以使用这个选项几次将几个MIB模块加载到tcpdump中
- -M secret：使用秘钥作为共享秘钥，以使用TCP-MD5选项（RFC 2385）验证在TCP段中找到的摘要
- -n：不要将主机地址转换为名称。 这可以用来避免DNS查找
- -nn：不要将协议和端口号等转换为名称
- -N：不要打印主机名的域名。
- --number：在行的开头打印可选的数据包编号
- -O|--no-optimize：关闭数据包匹配代码优化器。
- -p|--no-promiscuous-mode：不要将接口置于混杂模式
- -Q|-P direction|--direction=direction：选择应捕获数据包的发送/接收方向。 可能的值为“ in”，“ out”和“ inout”。
- -q：快速输出，打印较少的协议信息
- -r file：从文件（使用-w选项或其他写入pcap或pcap-ng文件的工具创建的文件）中读取数据包。 如果文件为``-''，则使用标准输入
- -S|--absolute-tcp-sequence-numbers：打印绝对的TCP序列号
- -s snaplen|--snapshot-length=snaplen：Snarf从每个数据包中取出数据字节，而不是默认的262144字节。在输出中用``[| proto]''指示由于快照有限而截断的数据包，其中proto是发生截断的协议级别的名称。 请注意，拍摄较大的快照既增加了处理数据包所需的时间，也有效地减少了数据包缓冲量。这可能会导致数据包丢失。还要注意，拍摄较小的快照将丢弃来自传输层以上协议的数据，这会丢失可能很重要的信息。例如，NFS和AFS请求和回复非常大，如果选择了过短的快照长度，则很多细节将不可用。 如果您需要将快照大小降至默认值以下，则应将snaplen限制为捕获您感兴趣的协议信息的最小数目。将snaplen设置为0会将其设置为默认值262144，以便与最近较旧的tcpdump的版本适配
- -T type：由“expression”选择的强制数据包被解释为指定的类型。
- -t：不打印时间
- -tt：打印时间戳(自 January 1, 1970, 00:00:00, UTC以来的秒数)
- -ttt：打印当前和上一个包之间的时间差（单位是微秒）
- -tttt：打印更详细的时间戳(有日期)
- -ttttt：打印当前和第一个包之间的时间差（单位是微秒）
- -u：打印未解码的NFS句柄
- -U|--packet-buffered：如果未指定-w选项，则使打印的数据包输出为`packet-buffered`; 即，在打印每个数据包内容的描述时，它将被写入标准输出，而不是在不写入终端时，仅在输出缓冲区已满时才写入。如果指定了-w选项，则将保存的原始数据包输出设置为`packet-buffered`; 即，在保存每个数据包时，它将被写入输出文件，而不是仅在输出缓冲区填满时才被写入。此参数在缺少pcap_dump_flush（）函数的libpcap版本不支持
- -v：解析和打印时，产生（稍微多一些）详细的输出。例如，打印IP包中的生存时间，标识，总长度和选项。还启用其他数据包完整性检查，例如验证IP和ICMP头校验和。 使用-w选项写入文件时，每秒报告一次捕获的数据包数量。
- -vv：更详细的输出。 例如，从NFS答复数据包中打印出其他字段，并对SMB数据包进行完全解码
- -vvv：更详细的输出。 例如，telnet SB ... SE选项将完整打印。 使用-X Telnet选项时，也以十六进制打印
- -V file：从文件中读取文件列表, 如果文件是'-'的话则使用标准输入
- -w file：将捕获到的数据包直接输出到文件, 如果文件是'-'的话则使用标准输入
- -W：与-C选项一起使用，这将限制文件数量，超出后会覆盖最开始保存的文件
- -x：解析和打印时，除了打印每个数据包的标题外，还要以十六进制格式打印每个数据包的数据（减去其链接层头部）。整个数据包或snaplen字节中的较小者将被打印。请注意，这是整个链路层数据包，因此对于填充的链接层（例如以太网），当高层数据包比所需的填充长度短时，填充字节也将被打印。
- -xx：解析和打印时，除了打印每个数据包的标题外，还要以十六进制格式打印每个数据包的数据，包括其链接层头部。
- -X：解析和打印时，除了打印每个数据包的标题外，还要以十六进制和ASCII格式打印每个数据包的数据（减去链接层头部）。这对分析新协议非常方便。
- -XX:解析和打印时，除了打印每个数据包的标题外，还要以十六进制和ASCII格式打印每个数据包的数据，包括其链接层头部。
- -y datalinktype|--linktype=datalinktype：设置在将数据包捕获到datalinktype时要使用的数据链路类型
- -z postrotate-command：与-C或-G选项一起使用，这将使tcpdump运行`postrotate-command file`，其中file是每次旋转后关闭的保存文件。 例如，指定`-z gzip`或`-z bzip2`将使用gzip或bzip2压缩每个保存文件。
- -Z user|--relinquish-privileges=user：如果tcpdump以root身份运行，请在打开捕获设备或输入保存文件之后，但在打开任何保存文件以进行输出之前，将用户ID更改为user，将用户组ID更改为主要的user组。
- [ expression ]：过滤表达式, 格式参见http://www.tcpdump.org/manpages/pcap-filter.7.html


### 过滤语法
- 指定接口(-i)
  - 捕获eth0网卡数据包
    - tcpdump -i eth0
- 指定IP地址(host)，可以加入逻辑符与方向符
  - 捕获192.168.0.1与192.168.0.2或192.168.0.3之间的数据包
    - tcpdump -i any host 192.168.0.1 and 192.168.0.2 or 192.168.0.3
  - 捕获源ip为192.168.0.1的数据包
    - tcpdump -i any src host 192.168.0.1
  - 捕获目的ip为192.168.0.1的数据包
    - tcpdump -i any dst host 192.168.0.1
  - 捕获除192.168.0.1主机之外的数据包
    - tcpdump -i any host! 192.168.0.1
  - 捕获从192.168.0.1发出到192.168.0.2的数据包
    - tcpdump -i any src host 192.168.0.1 and dst host 192.168.0.2
- 指定端口(port)，可以用host一样命令
  - 捕获目的端口为80的数据包
    - tcpdump -i any dst port 80
- 指定协议(ether，fddi，tr，wlan，ip，ip6，arp，rarp，decnet，tcp，udp)
  - 捕获TCP包
    - tcpdump -i any tcp

### 实战
- 根据TCP标志位抓取数据包
  - SYN包
    - tcpdump -i any 'tcp[tcpflags] & tcp-syn != 0'
  - ACK包
    - tcpdump -i any 'tcp[tcpflags] & tcp-ack != 0'
  - SYN与ACK包
    - tcpdump -i any 'tcp[tcpflags] & tcp-syn != 0 and tcp[tcpflags] & tcp-ack != 0'
  - FIN包
    - tcpdump -i any 'tcp[tcpflags] & tcp-fin != 0'

- HTTP协议
  - 抓取172.17.0.1与172.17.0.4之间的GET请求数据包
    - tcpdump -i any 'host 172.17.0.1 or host 172.17.0.4 and tcp[(tcp[12:1] >> 2):4] = 0x47455420' -A
      - `tcp[12:1] >> 2`由`tcp[12:1] >> 4 << 2`计算得出，`tcp[12:1] >> 4`为tcp头部长度，由于此长度并非字节数，因此我们需要乘上单位，参考[tcp头部说明](https://blog.csdn.net/u012206617/article/details/105621108)可以看出单位为32bit，换算成字节为4byte,因此需要乘上4，由此我们可以得出`tcp[12:1] >> 2`表示tcp头部的字节数。
      - `tcp[(tcp[12:1] >> 2):4]`表示在tcp头部长度后取4个字节，根据[http协议](https://juejin.cn/post/6844903593116434439#heading-3)可知，GET请求前4个字节的ascii码值分别为47,45,54,20，因此`tcp[(tcp[12:1] >> 2):4] = 0x47455420`表示数据段前4个字节为`GET `的数据包
  - 抓取172.17.0.1与172.17.0.4之间的GET请求以及请求路径为/abcd的数据包
    - tcpdump -i any 'host 172.17.0.1 or host 172.17.0.4 and tcp[(tcp[12:1] >> 2):4] = 0x47455420 and tcp[(tcp[12:1] >> 2)+4:4] = 0x2f616263 and tcp[(tcp[12:1] >> 2)+8:1] = 0x64' -A
      - `tcp[(tcp[12:1] >> 2)+4:4] = 0x2f616263`表示在tcp数据段5-8个字节为/abc
      - `cp[(tcp[12:1] >> 2)+8:1] = 0x64`表示tcp数据段第9个字节为d字符
  - 抓取172.17.0.1与172.17.0.4之间的POST请求以及响应
    - tcpdump -i any 'host 172.17.0.1 or host 172.17.0.4 and tcp[(tcp[12:1] >> 2):4] = 0x504f5354 or tcp[(tcp[12:1] >> 2):4] = 0x48545450' -A
  - 抓取172.17.0.1与172.17.0.4之间的User-Agent字段
    - tcpdump -i any 'host 172.17.0.1 or host 172.17.0.4' -A |egrep -i "User-Agent: *"
- 数据库
  - 抓取sql语句(mysql为例)
    - tcpdump -i any port 3306 -l -A|grep -ioP "\K(SELECT|UPDATE|DELETE|INSERT|SET|COMMIT|ROLLBACK|CREATE|DROP|ALTER|CALL) .*"

- SSH协议
  - tcpdump -i any 'tcp[(tcp[12] >> 2):4] = 0x5353482d' -A
- SMTP协议
  - tcpdump -i any 'tcp[(tcp[12] >> 2):4] = 0x4d41494c' -A

### 总结
  tcpdump的可选参数很多，只有不断在实战使用，才能熟悉每一个参数的具体使用场景，但tcpdump有一个重要的点在于正确使用分片偏移处理(例如: `tcp[20:4]`)，可以精确定位到某个bit位具体的状态，这对我们自定义抓取逻辑有重要的作用。