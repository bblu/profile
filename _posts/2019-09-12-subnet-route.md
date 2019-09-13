---
title: Route Between Subet子网路由配置
date: 2019-09-12 7:10
categories: blog
tags: [network]
---
### 1.子网 subnet
Since ip addresses are made up of 8 bit numbers, the largest number you will see in an ip address is 255. The smallest number you could see in an ip would be a 0. So if you think of an ip address as a series of four eight bit numbers separated by periods, you get the following.

10010011.10110101.10110111.01010110 = 147.181.183.86

A subnet of 192.168.200.x means that all devices on the network will have IP addresses that start with 192.168.200. It also means that the network can not contain more than 255 devices.   

The highest and lowest IP addresses often have special meanings, so I would limit this subnet to 192.168.200.1 (avoiding zero) through 192.168.200.253 (skipping 254). Thus, a max of 253 concurrent devices

### 2.子网掩码 subnet mask

The subnet mask specifies the range of the ip addresses in a group. The subnet mask looks a lot like an ip address. It is made up of four eight bit numbers separated by periods. These numbers once again range from 0 to 255. A typical subnet mask is 255.255.255.0.

There are a couple intresting things about subnet masks. They don't really behave like you would initially expect them to. The numbers of a subnet mask count ip addresses that are not there. This means higher the numbers of a subnet mask are the less ip addresses belong to it.
For example:

255.255.255.255	= There are no ip addresses in this range.
0.0.0.0	= This is the range of all ip addresses.
Lets take one of the most basic subnets the 255.255.255.0 one, and see how many addresses are in it's range. The first step is to put the subnet into binary. Let go ahead and do that now. If you don't know how to put something into binary read Binary Numbers for more information.

11111111.11111111.11111111.00000000 = 255.255.255.0

掩码越短子网容量越大

So how do we find out how many ip addresses are in this group? Well its rather simple actually. Just count the number of zeros, and then take 2 to the number of zeros power. In this case it would be 2^8 = 256. Another way to do it is to multiply 2 times itself 7 times. 2*2*2*2*2*2*2*2=256 So we have two hundred and fifty six ip addresses in that range! Another thing that is intresting to know. Subnets will always be all ones on one side, and all zeros on the other. I mean that they will always look like 111111000000 and never like 1010101101.

Lets take a closer look at what a group of ip addresses looks like. Using the 255.255.255.0 subnet from above let me make a table.
#### 2.1常规掩码
```
192.168.1.0 - Subnet Address
192.168.1.1 - usually the gateway
192.168.1.2 - 可分配的地址池
192.168.1.3
...
192.168.1.254
192.168.1.255 - Broadcast Address
```
Every group of ip addresses, has a Subnet Address, Broadcast Address, and Gateway. Both the Subnet Address ip address and the Broadcast Address ip address are used to send information to every ip address in the group. The Gateway acts sort of like the group's controller. For instance, let's say that your computer is on the ip address 192.168.1.3. When you send send information to the internet, your computer sends data to the gateway. Then the gateway sends that data on to the internet. The same thing is true when you get data from the internet. The internet sends data to the gateway, and then the gateway passes that information on to your computer. The gateway can be on any ip address in the range. Usually it is on the second ip address in the range, or the second ip address from the end of the range. The Subnet Address is always on the first ip address in the range, and the Broadcast Address is always on the last one in the range.
#### 2.2非常掩码
Okay I'm going to change our subnet from 255.255.255.0 to 255.255.255.240. Lets say the ip address of our computer is 192.168.1.132. How many ip addresses do we have? Well lets convert the subnet to binary. You should get the following binary subnet.

11111111.11111111.11111111.11110000

Okay, we have four zeros. So take 2^4 which equals 16. Alternatively 2*2*2*2 equals 16 as well. We have 16 ip addresses in our range. Well lets draw out our table. We can't draw our ip address table without knowing where the range starts. This is how you figure that out. We take our subnet and AND it to our ip address converted into binary. Go ahead and conver our ip address into binary. You should get the following.

11000000.10101000.00000001.10000100 = 192.168.1.132

Now we AND that with our subnet. It is easy to think of ANDing as finding the truth of two numbers. Every 1 is true and every 0 is false.

True and true is true! 1 and 1 is 1.
True and false is false. 1 and 0 is 0.
False and true is false. 0 and 1 is 0.
False and false is false. 0 and 0 is 0.

So lets line up our subnet and our ip address.

11000000.10101000.00000001.10000100 = 192.168.1.132  
11111111.11111111.11111111.11110000 = 255.255.255.240
-----------------------------------
11000000.10101000.00000001.10000000

So look at the first column. We have a 1 and a 1. 1(true) and 1(true) = 1(true) The next column is the same thing. The thrid column is a 0 and a 1. 0(false) and 1(true) = 0(false). Continue doing that for the whole number. Now that we have our result lets take it and convert it back to base 10, so we can get the first ip address in our range.

11000000.10101000.00000001.10000000 = 192.168.1.128

Great! Now that we have our first ip address and we know that we have 16 ip addresses, we can make our table.
```
192.168.1.128 - Subnet Address
192.168.1.129 - Gateway
192.168.1.130 -
192.168.1.131 -
192.168.1.132 - Our computer!
192.168.1.133 - IP Range
192.168.1.134 -
192.168.1.135 -
192.168.1.136 -
192.168.1.137 -
192.168.1.138 -
192.168.1.139 -
192.168.1.140 -
192.168.1.141 -
192.168.1.142 -
192.168.1.143 - Broadcast Address
```
### 3.网络地址转换 NAT

### 4.路由 traceroute
 在linux主机系统下：traceroute hostname  
 而在Windows系统下：tracert hostname
 
 ```
 macbook:profile bblu$ traceroute www.163.com
traceroute: Warning: www.163.com has multiple addresses; using 116.199.2.84
traceroute to z163ipv6.v.bsgslb.cn (116.199.2.84), 64 hops max, 52 byte packets
 1  rt-n14u.lan (192.168.123.1)  5.652 ms  1.799 ms  1.566 ms
 2  192.168.31.1 (192.168.31.1)  1.361 ms  2.182 ms  2.214 ms
 3  192.168.100.1 (192.168.100.1)  1.627 ms  2.269 ms  2.205 ms
 4  172.31.8.26 (172.31.8.26)  13.596 ms  11.704 ms  12.205 ms
 5  172.31.8.25 (172.31.8.25)  13.253 ms  11.399 ms  12.864 ms
 6  172.22.22.141 (172.22.22.141)  15.295 ms  12.823 ms  14.356 ms
 7  172.22.22.142 (172.22.22.142)  10.642 ms  11.057 ms  18.639 ms
 8  172.31.33.42 (172.31.33.42)  28.256 ms  19.149 ms  21.317 ms
 9  116.199.2.84 (116.199.2.84)  10.590 ms !Z  18.942 ms !Z  8.317 ms !Z
macbook:profile bblu$ 
 ```
 
 Reference [A Comprehensive Look at
Subnetting, Gateways, and IP Ranges](https://portforward.com/networking/subnetting.htm)