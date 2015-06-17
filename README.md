#tiny_linux

#Prepare source code#
Download the 4.0.y kernel
use this command:
git clone --branch linux-4.0.y --single-branch git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git

Download the AK's reduced Linux network stack system, AK's linux kernel source is based on linux-4.0.y
use this command:
git clone git://git.kernel.org/pub/scm/linux/kernel/git/ak/linux-misc net/debloat

place the source folder into the root dir of this project

#set configuration#
Use the directory 'obj' for obj files to keep kernel source clean
You can use 'make O=./obj/xxx' or run ./make.sh
Before you build the kernel, you can use the already optimized config file in folder obj 'config'
copy this file in to the folder where Makefile exists, than use make command you can build the kernel with small configuration.

#build linux kernel#
After configuration, or copy the 'config' and rename it to '.config' into the source folder.
just type make to compile linux kernel source code and build the kernel image file.

you can build a normal kernel with the official source code of linux 4.0.y 
and build a network stack optimized kernel with the source code of AK's system

#run and test#
you can find the image file of kernel at 'linux_src/arch/x86/boot/bzimage'
place this file into ./out 
then run the run.sh, to start the qemu and load the ramdisk

before using wget to test tcp, use 'free' command to see the current memory use in system.
type 'wget http://119.75.218.70' to download the baidu.com index page
then type 'rm index.html' 
then type 'free' to see the memory cost to gusee the tcp memory spend.
 
#test result#
normal kernel:
5148 - 5036 = 112kb 

AK's smalltcpstack kernel:
5132 - 5032 = 100kb

We can see the AK's kernel use less memory to run wget.

#What Source Code Modified of AK's kernel#
AK's kernel optimize the tcp network stack for size.
In the tiny linux with tcp network, the key folder of tcp network are:
net/core net/ipv4  net/netlink and the file net/socket.c net/sysctl_net.c

Use the diff command to compare the net folder of AK, and the offical kernel.
[net/ipv4]
diff -bBq ./linux-4.0.4/net/ipv4/ ./smalltcpstack/net/debloat/net/ipv4/

[net/core]
diff -bBq ./linux-4.0.4/net/core/ ./smalltcpstack/net/debloat/net/core/

[net/netlink]
diff -bBq ./linux-4.0.4/net/netlink/ ./smalltcpstack/net/debloat/net/netlink/

[net]
diff -bBq ./linux-4.0.4/net/netlink/ ./smalltcpstack/net/debloat/net/netlink/

We see many source file has modified, most of them are not useful.

changed files in ipv4:
Files ./linux-4.0.4/net/ipv4/fib_frontend.c and ./smalltcpstack/net/debloat/net/ipv4/fib_frontend.c differ
Files ./linux-4.0.4/net/ipv4/geneve.c and ./smalltcpstack/net/debloat/net/ipv4/geneve.c differ
Files ./linux-4.0.4/net/ipv4/inet_connection_sock.c and ./smalltcpstack/net/debloat/net/ipv4/inet_connection_sock.c differ
Files ./linux-4.0.4/net/ipv4/inet_diag.c and ./smalltcpstack/net/debloat/net/ipv4/inet_diag.c differ
Files ./linux-4.0.4/net/ipv4/ip_forward.c and ./smalltcpstack/net/debloat/net/ipv4/ip_forward.c differ
Files ./linux-4.0.4/net/ipv4/ipmr.c and ./smalltcpstack/net/debloat/net/ipv4/ipmr.c differ
Files ./linux-4.0.4/net/ipv4/ping.c and ./smalltcpstack/net/debloat/net/ipv4/ping.c differ
Files ./linux-4.0.4/net/ipv4/route.c and ./smalltcpstack/net/debloat/net/ipv4/route.c differ
Files ./linux-4.0.4/net/ipv4/tcp_cong.c and ./smalltcpstack/net/debloat/net/ipv4/tcp_cong.c differ
Files ./linux-4.0.4/net/ipv4/tcp_cubic.c and ./smalltcpstack/net/debloat/net/ipv4/tcp_cubic.c differ
Files ./linux-4.0.4/net/ipv4/tcp_input.c and ./smalltcpstack/net/debloat/net/ipv4/tcp_input.c differ
Files ./linux-4.0.4/net/ipv4/tcp_ipv4.c and ./smalltcpstack/net/debloat/net/ipv4/tcp_ipv4.c differ
Files ./linux-4.0.4/net/ipv4/tcp_output.c and ./smalltcpstack/net/debloat/net/ipv4/tcp_output.c differ
Files ./linux-4.0.4/net/ipv4/xfrm4_output.c and ./smalltcpstack/net/debloat/net/ipv4/xfrm4_output.c differ
Files ./linux-4.0.4/net/ipv4/netfilter/ip_tables.c and ./smalltcpstack/net/debloat/net/ipv4/netfilter/ip_tables.c differ

changed files in core:
Files ./linux-4.0.4/net/core/dev.c and ./smalltcpstack/net/debloat/net/core/dev.c differ
Files ./linux-4.0.4/net/core/fib_rules.c and ./smalltcpstack/net/debloat/net/core/fib_rules.c differ
Files ./linux-4.0.4/net/core/net_namespace.c and ./smalltcpstack/net/debloat/net/core/net_namespace.c differ
Files ./linux-4.0.4/net/core/rtnetlink.c and ./smalltcpstack/net/debloat/net/core/rtnetlink.c differ
Files ./linux-4.0.4/net/core/skbuff.c and ./smalltcpstack/net/debloat/net/core/skbuff.c differ
Files ./linux-4.0.4/net/core/sock.c and ./smalltcpstack/net/debloat/net/core/sock.c differ
Files ./linux-4.0.4/net/core/sysctl_net_core.c and ./smalltcpstack/net/debloat/net/core/sysctl_net_core.c differ

changed files in netlink:
Files ./linux-4.0.4/net/netlink/af_netlink.c and ./smalltcpstack/net/debloat/net/netlink/af_netlink.c differ

[net]
diff -bBq ./linux-4.0.4/net/ ./smalltcpstack/net/debloat/net/

#reference#
how to download and configure and compile linux kernel:
http://www.linux.org/threads/linux-kernel-reading-guide.5384/

A reduced Linux network stack for small systems:
https://lkml.org/lkml/2014/5/5/686

How to reduce the kernel size:
http://www.elinux.org/Kernel_Size_Tuning_Guide

Introduction of Linux Network Stack:
http://www.linux.org/threads/linux-network-stack.4620/

Linux Tune Network Stack (Buffers Size) To Increase Networking Performance:
http://www.cyberciti.biz/faq/linux-tcp-tuning/

