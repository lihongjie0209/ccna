# CDP

1. 什么是CDP?

   思科私有的二层网络设备发现协议, 用于在二层网络中共享设备信息.

2. 如何开启CDP?

   默认开启, 或者在全局模式下`cdp run` 或者在接口模式下`cdp enable`

3. 如何查看发现的邻居设备?

   `show cdp neighbors`

4. CDP 邻居如何用于网络排错?

   CDP 中的邻居如果存在就证明二者之间的二层网络是正常的.



   ## LAB

   ![1539608457002](assets\1539608457002.png)

   两台路由器开机, 互连. 思科路由器默认开始CDP, 可以直接查看CDP 邻居信息.

   ```
   R1#show cdp neighbors
   Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                     S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                     D - Remote, C - CVTA, M - Two-port Mac Relay
   
   Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
   
   Total cdp entries displayed : 0
   
   ```

   查看邻居表发现没有一条记录, 那么可以确定二层链路没有连通, 查看接口状态.

   ```
   R1#show ip inter bri
   Interface                  IP-Address      OK? Method Status                Protocol
   Ethernet0/0                unassigned      YES NVRAM  administratively down down
   Ethernet0/1                unassigned      YES NVRAM  administratively down down
   Ethernet0/2                unassigned      YES NVRAM  administratively down down
   Ethernet0/3                unassigned      YES NVRAM  administratively down down
   Ethernet1/0                unassigned      YES NVRAM  administratively down down
   
   ```

   路由器的接口默认处于关闭状态, 如果是交换机的话不会有这个问题. 那么开启接口之后再次查看CDP邻居.

   ```
   R1#show cdp neighbors
   Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                     S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone,
                     D - Remote, C - CVTA, M - Two-port Mac Relay
   
   Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
   R2               Eth 0/0           133              R B   Linux Uni Eth 0/0
   
   Total cdp entries displayed : 1
   
   ```

   然后我们可以查看全局的CDP的配置信息

   ```
   R1#show cdp
   Global CDP information:
           Sending CDP packets every 60 seconds  // cdp 更新发送周期
           Sending a holdtime value of 180 seconds
           Sending CDPv2 advertisements is  enabled
   
   ```

   查看接口级别的CDP配置信息

   ```
   R1#show cdp
   Global CDP information:
           Sending CDP packets every 60 seconds
           Sending a holdtime value of 180 seconds
           Sending CDPv2 advertisements is  enabled
   R1#show cdp inter
   R1#show cdp interface
   Ethernet0/0 is up, line protocol is up
     Encapsulation ARPA
     Sending CDP packets every 60 seconds
     Holdtime is 180 seconds
   Ethernet0/1 is administratively down, line protocol is down
     Encapsulation ARPA
     Sending CDP packets every 60 seconds
     Holdtime is 180 seconds
   Ethernet0/2 is administratively down, line protocol is down
     Encapsulation ARPA
     Sending CDP packets every 60 seconds
     Holdtime is 180 seconds
   Ethernet0/3 is administratively down, line protocol is down
     Encapsulation ARPA
     Sending CDP packets every 60 seconds
     Holdtime is 180 seconds
    cdp enabled interfaces : 16
    interfaces up          : 1
    interfaces down        : 15
   
   ```

   查看邻居的具体信息

   ```
   R2#show cdp entry R1
   -------------------------
   Device ID: R1
   Entry address(es):
   Platform: Linux Unix,  Capabilities: Router Source-Route-Bridge
   Interface: Ethernet0/0,  Port ID (outgoing port): Ethernet0/0
   Holdtime : 178 sec
   
   Version :
   Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.5(2)T, DEVELOPMENT TEST SOFTWARE
   Technical Support: http://www.cisco.com/techsupport
   Copyright (c) 1986-2015 by Cisco Systems, Inc.
   Compiled Thu 26-Mar-15 07:36 by prod_rel_team
   
   advertisement version: 2
   Duplex: half
   ```

   查看CDP流量统计

   ```
   R2#show cdp traffic
   CDP counters :
           Total packets output: 9, Input: 6
           Hdr syntax: 0, Chksum error: 0, Encaps failed: 0
           No memory: 0, Invalid packet: 0,
           CDP version 1 advertisements output: 0, Input: 0
           CDP version 2 advertisements output: 9, Input: 6
   
   ```


# LLDP

1. 什么是LLDP?

   类似于CDP, 但是属于开源协议, 可以在异构网络中使用.

## LAB

![1539609202707](assets\1539609202707.png)

### 开启LLDP

全局开启LLDP进程 `lldp run`

接口开启LLDP发送 `lldp transmit`

接口开启LLDP接收 `lldp receive`

### 查看LLDP 邻居

```
R2#show lldp neighbors
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other

Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Et0/0          120        R               Et0/0

Total entries displayed: 1

```



# 总结

鉴于思科在国内的市场占有率下降, 在现网中碰到全思科的同构网络情况比较少, 所有LLDP协议的实用性更高, 可以运行在不同厂商的设备上.