# THAY THẾ THƯ MỤC PROJECT BẰNG PROJECT MỚI
```bash
git clone https://github.com/RealaxER/allwiner
mv allwiner project
```

# CÁC THAY ĐỔI 
- Buildroot vẫn giữ nguyên của hãng 
- Kernel thay đổi usb serial (thêm id của simcom)
- Thay đổi script build trong project
- Thêm file code vào rootfs

# UPDATE KERNEL
- Kernel chưa hỗ trợ con AT7670c nên cần thêm id của nó 
- Copy file chỉnh sửa vào kernel tree

```bash
cp project/option.c kernel/drivers/usb/serial/option.c
```

# GIẢI THÍCH SCRIPT BUILD 
- Các thay đổi được viết ở trong 

```bash
project/image/configs/i2m/rootfs.mk
```
- Thay đổi chân gpio để enable simcom và usb serial

```bash 
if [ "$(PROJECT)" =  "2D07" ]; then \
    echo "echo 10 > /sys/class/gpio/export" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo out > /sys/class/gpio/gpio10/direction" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    # echo "echo 1 > /sys/class/gpio/gpio10/value" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo 85 > /sys/class/gpio/export" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo out > /sys/class/gpio/gpio85/direction" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    # echo "echo 0 > /sys/class/gpio/gpio85/value" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo 6 > /sys/class/gpio/export" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo out > /sys/class/gpio/gpio6/direction" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
    echo "echo 1 > /sys/class/gpio/gpio6/value" >> $(OUTPUTDIR)/rootfs/etc/init.d/rcS; \
fi;
```

- Tự động chạy script sau khi boot để connnect đến module sim
```bash
	# =============Define here ============
	echo "sleep 1" >> $(OUTPUTDIR)/customer/demo.sh
	echo "pppd call air-ppp &" >> $(OUTPUTDIR)/customer/demo.sh
	echo "sleep 8" >> $(OUTPUTDIR)/customer/demo.sh
	echo "route add default dev ppp0" >> $(OUTPUTDIR)/customer/demo.sh
	echo "echo "nameserver 8.8.8.8" >> /etc/resolv.conf" >> $(OUTPUTDIR)/customer/demo.sh
	echo "echo "nameserver 4.4.4.4" >> /etc/resolv.conf" >> $(OUTPUTDIR)/customer/demo.sh
	# echo "source /service/ctc2.sh" >> $(OUTPUTDIR)/customer/demo.sh
```

- Để chạy code bằng tay thì chạy câu lệnh sau
```bash
echo "source /service/ctc2.sh" >> $(OUTPUTDIR)/customer/demo.sh
```


# BUILD IMAGE 
```bash
./Release_to_customer.sh -f nand -p ssd202 -o 2D07
cd project
./make_sd_upgrade_sigmastar.sh 
```
