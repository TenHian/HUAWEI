# 数据帧

## Ethernet_II帧格式

![image-20210925132324597](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925132324597.png)

<font size=4>以太网帧type字段的作用：<br>当type为0x0800，将帧传给网络层IP协议；当为0x0806，传给网络层ARP协议。起到了识别上层协议的作用<br>以太网帧长度最大为1518字：可变长度字段为DATA<br>FCS(校验和)字段为检验数据完整性</font>

## IEE802.3帧格式

![image-20210925132931722](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925132931722.png)

<font size=4>LLC（逻辑链路控制）是由目的服务访问点D.SAP源服务访问点S.SAP和控制字段Control组成<br>SNAP（次级网络访问协议？）是由Org Code（机构代码）和Type（类型）组成，type与Ethernet_II相同</font>

## 数据帧的传输

<font size=4>数据链路层基于MAC地址进行帧的传输</font>

## 以太网的MAC地址

![image-20210925134603489](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925134603489.png)

<font size=4>MAC地址长度为48bits=6字节（B）<br>前24比特代表供应商代码，后24为厂商自己分配<br>MAC地址唯一，不可变(待议？)</font>

### 单播

![image-20210925143516604](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925143516604.png)

<font size=4>单播MAC地址前8比特，第8位为0，从单一源端发送到单一目的端</font>

### 广播

![image-20210925143735466](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925143735466.png)

<font size=4>48位全为F，广播域内所有设备都要接收</font>

### 组播

![image-20210925144009730](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925144009730.png)

<font size=4>组播转发可以理解为选择性的广播，主机侦听特定组播地址，接收并处理目的MAC地址为该组播MAC地址的帧</font>

# IP编址

<font size=4>网络层位于数据链路层和传输层之间，包含很多协议，最重要的是IP协议</font>

## IP报文头部

![image-20210925150330341](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925150330341.png)

<font size=4>版本号（Version）：长度4比特。标识目前采用的IP协议的版本号。一般的值为0100（IPv4），0110（IPv6）<br><br>IP包头长度（Header Length）：长度4比特。这个字段的作用是为了描述IP包头的长度，因为在IP包头中有变长的可选部分。该部分占4个bit位，单位为32bit（4个字节），即本区域值= IP头部长度（单位为bit）/(8*4)，因此，一个IP包头的长度最长为“1111”，即15*4＝60个字节。IP包头最小长度为20字节。<br><br>DS Feild(用于QoS拥塞管理)<br>服务类型（Type of Service）：长度8比特。8位 按位被如下定义 PPP DTRC0<br>	 PPP：定义包的优先级，取值越大数据越重要<br>	 000 普通 (Routine)<br>	 001 优先的 (Priority)<br>	   010 立即的发送 (Immediate)<br>	   011 闪电式的 (Flash)<br>	   100 比闪电还闪电式的 (Flash Override)<br>	   101 CRI/TIC/ECP(找不到这个词的翻译)<br>	   110 网间控制 (Internetwork Control)<br>	   111 网络控制 (Network Control)<br><br>	D 时延: 0:普通 1:延迟尽量小<br>	T 吞吐量: 0:普通 1:流量尽量大<br>	R 可靠性: 0:普通 1:可靠性尽量大<br>	M 传输成本: 0:普通 1:成本尽量小<br>	0 最后一位被保留，恒定为0<br><br>IP包总长（Total Length）：长度16比特。 以字节为单位计算的IP包的长度 (包括头部和数据)，所以IP包最大长度65535字节。<br><br>标识符（Identifier）:长度16比特。该字段和Flags和Fragment Offest字段联合使用，对较大的上层数据包进行分段（fragment）操作。路由器将一个包拆分后，所有拆分开的小包被标记相同的值，以便目的端设备能够区分哪个包属于被拆分开的包的一部分。<br><br>标记（Flags）：长度3比特。该字段第一位不使用。第二位是DF（Don't Fragment）位，DF位设为1时表明路由器不能对该上层数据包分段。如果一个上层数据包无法在不分段的情况下进行转发，则路由器会丢弃该上层数据包并返回一个错误信息。第三位是MF（More Fragments）位，当路由器对一个上层数据包分段，则路由器会在除了最后一个分段的IP包的包头中将MF位设为1。<br><br>片偏移（Fragment Offset）：长度13比特。表示该IP包在该组分片包中位置，接收端靠此来组装还原IP包。<br><br>生存时间（TTL）：长度8比特。当IP包进行传送时，先会对该字段赋予某个特定的值。当IP包经过每一个沿途的路由器（三层设备）的时候，每个沿途的路由器会将IP包的TTL值减少1。如果TTL减少为0，则该IP包会被丢弃（不可达，丢包）。这个字段可以防止由于路由环路而导致IP包在网络中不停被转发。<br><br>协议号（Protocol）：长度8比特。标识了上层所使用的协议。<br>以下是比较常用的协议号：<br>	    1    ICMP<br>	    2    IGMP<br>	    6    TCP<br>	   17    UDP<br>	   88    IGRP<br>	   89    OSPF<br><br>头部校验（Header Checksum）：长度16位。用来做IP头部的正确性检测，但不包含数据部分。 因为每个路由器要改变TTL的值,所以路由器会为每个通过的数据包重新计算这个值。<br>起源和目标地址（Source and Destination Addresses）：这两个地段都是32比特。标识了这个IP包的起源和目标地址。要注意除非使用NAT，否则整个传输的过程中，这两个地址不会改变。<br>至此，IP包头基本的20字节已介绍完毕，此后部分属于可选项，不是必须的部分。<br><br>可选项（Options）：这是一个可变长的字段。该字段属于可选项，主要用于测试，由起源设备根据需要改写。可选项目包含以下内容：<br>	    松散源路由（Loose source routing）：给出一连串路由器接口的IP地址。IP包必须沿着这些IP地址传送，但是允许在相继的两个IP地址之间跳过多个路由器。<br>	    严格源路由（Strict source routing）：给出一连串路由器接口的IP地址。IP包必须沿着这些IP地址传送，如果下一跳不在IP地址表中则表示发生错误。<br>	    路由记录（Record route）：当IP包离开每个路由器的时候记录路 由器的出站接口的IP地址。<br>	    时间戳（Timestamps）：当IP包离开每个路由器的时候记录时间。<br><br>填充（Padding）：因为IP包头长度（Header Length）部分的单位为32bit，所以IP包头的长度必须为32bit的整数倍。因此，在可选项后面，IP协议会填充若干个0，以达到32bit的整数倍。</font>

## IP编址
<font size=4>IP地址分为网络位+主机位</font>
## IP地址分类

![image-20210925202746281](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925202746281.png)

<font size=4>IPv4地址被划分为A、B、C、D、E五类，每类地址的网络号包含不同的字节数。A类，B类 和C类地址为可分配IP地址，每类地址支持的网络数和主机数不同。比如，A类地址可支持 126个网络，每个网络支持224 （16,777,216 )个主机地址，另外每个网段中的网络地址和 广播地址不能分配给主机。C类地址支持200多万个网络，每个网络支持256个主机地址， 其中254个地址可以分配给主机使用</font>

<font size=4>D类地址为组播地址。主机收到以D类地址为目的地址的报文后，且该主机是该组播组成员， 就会接收并处理该报文。各类IP地址可以通过第一个字节中的比特位进行区分。如A类地址 第一字节的最高位固定为0，B类地址第一字节的高两位固定为10，C类地址第一字节的高 三位固定为110，D类地址第一字节的高四位固定为1110，E类地址第一字节的高四位固定 为1111</font>

## IP地址类型

### 私有地址范围

<font size=4>10.0.0.0~10.255.255.255<br>172.16.0.0~172.31.255.255<br>192.168.0.0~192.168.255.255</font>

### 特殊地址

<font size=4>127.0.0.0~127.255.255.255	用于测试<br>0.0.0.0	表示所有网络<br>255.255.255.255	广播地址</font>


## 默认子网掩码
<font size=4>IP地址分为网络位+主机位，子网掩码的作用为标识网络位<br>对于一个IP地址，将其网络位置1，主机位置0，就得到该IP子网掩码</font>

## 地址规划
<font size=4>子网掩码还能用来计算可用主机数</font>

![image-20210926112430228](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210926112430228.png)

<font size=4>8比特可用</font>


### 子网掩码分类

![image-20210925190845268](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925190845268.png)

#### 可变长子网掩码VLSM

<font size=4>节省IP地址，防止浪费</font>

![image-20210926112829165](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210926112829165.png)

<font size=4>借主机位作为网络位</font>

#### CIDR 无类域间路由

<font size=4>增强了网络的可扩展性</font>

## 网关

<font size=4>转发不同网段之间的数据包，通常为离主机最近的三层网络设备</font>

![image-20210926113435227](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210926113435227.png)

# 传输层

<font size=4>传输层最常见的两种协议TCP（传输控制协议）和 UDP（用户数据包协议）</font>

## TCP

<font size=4>面向连接的传输层协议，可提供可靠的传输服务</font>

### TCP端口号

<font size=4>传输层通过端口，来将数据包传递给应用层</font>

### TCP头部

![image-20210925192857908](https://gitee.com/teng-huaian/picupload1/raw/master/kali/image-20210925192857908.png)

<blockquote> 
 <ul><li>16位端口号：包括了源端口号和目的端口号。进行TCP通信时，客户端通常使用系统自动选择的临时端口号(一般都很大)，而服务器则使用知服务端口号或服务器管理员自定义的端口号。</li><li>32位序号：一次TCP通信过程中对一个传输方向上的字节流的每个字节的编号(从这个方向第一个报文段依次排列)。假设主机A和主机B进行TCP通信，A发送给B的第一个TCP报文段中的序号值是系统初始化的一个随机值ISN(初始序号值)。那么之后在A到B的方向上发送的TCP报文段中的序号值将会被系统设置为ISN加上该报文段所携带数据的第一个字节在整个数据字节流中的偏移。</li><li>32位确认号：用作对另一方发送来的TCP报文段做出相应。其值是收到对方的报文段的序号值加1。</li><li>4位头部长度：标识该TCP头部有多少个32bit字(4字节)。一共有4位，所以最大能表示TCP头部大小为60字节。</li><li>6位标志位包含如下几项：</li></ul>
 <ol><li>URG标志，表示紧急指针是否有效。</li><li>ACK标志，表示确认号是否有效。一般称携带ACK标志的报文段是确认报文段。</li><li>PSH标志，提示接收端应用程序立即从TCP接受缓冲区读走数据。</li><li>RST标志，表示要求对方重新建立连接。称携带RST标志的TCP报文段为复位报文段。</li><li>SYN标志，表示请求建立一个连接。称携带SYN标志的TCP报文段为同步报文段。</li><li>FIN标志，表示通知对方本端将关闭连接。称携带FIN标志的TCP报文段为结束报文段。</li></ol>
 <ul><li>16位窗口大小：是TCP流量控制的一个手段。这里说的窗口指的是接收通告窗口(RWND)。它告诉对方本端TCP接收缓冲区还能容纳多少字节的数据，以让对方控制发送数据的速度。</li><li>16位校验和：由发送端填充，接收端对TCP报文段执行CRC算法以效验TCP报文段在传输过程中是否损坏(包括TCP头部和数据部分)。这也是TCP可靠传输的一个重要保障。</li><li>16位紧急指针：是一个正的偏移量。它和序号字段的值相加表示最后一个紧急数据的下一字节的序号。即这个字段是紧急指针相对当前序号的偏移，为紧急偏移。以让接收端迅速接受到紧急数据。TCP的紧急指针是发送端向接收端发送紧急数据的方法。</li></ul>
</blockquote>
