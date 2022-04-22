#创建磁盘
qemu-img create -f qcow2 alpine-arm64.qcow2 10G

#打开虚拟机
qemu-system-aarch64 -m 1024 -cpu cortex-a57 -smp 2 -M virt -bios /data/data/com.termux/files/home/QEMU_EFI.fd -nographic -drive if=none,file=/data/data/com.termux/files/home/alpine-standard-3.15.4-aarch64.iso,id=cdrom,media=cdrom -device virtio-scsi-device -device scsi-cd,drive=cdrom -drive if=none,file=/data/data/com.termux/files/home/alpine-arm64.qcow2,format=qcow2,id=hd0 -device virtio-blk-device,drive=hd0

#安装系统
Asia/Shanghai
vi /etc/apk/repositories
http://mirrors.ustc.edu.cn/alpine/v3.15/main
http://mirrors.ustc.edu.cn/alpine/v3.15/community

#安装完成进入新系统
qemu-system-aarch64 -m 1024 -cpu cortex-a57 -smp 2 -M virt -bios /data/data/com.termux/files/home/QEMU_EFI.fd -nographic -drive if=virtio,file=/data/data/com.termux/files/home/alpine-arm64.qcow2,format=qcow2,id=hd0  -nic user,hostfwd=tcp:0.0.0.0:2222-:22

#安装docker
apk update
apk add docker
rc-update add docker boot   #添加到开机自启
service docker start

#alpine linux配置sshd
vi /etc/ssh/sshd_config
PermitRootLogin yes   #配置中添加下面代码即可
service sshd restart  #重启sshd服务
ssh root@127.0.0.1  #用root登录测试

-nic user,hostfwd=tcp:0.0.0.0:22-:22,hostfwd=udp:0.0.0.0:6513-:6513,hostfwd=tcp:0.0.0.0:80-:80
