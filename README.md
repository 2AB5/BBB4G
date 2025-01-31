# BBB4G

### The aim of this repo is to help people interface their BBB with any[^1] 4G modules available in the market. 
You can connect your 4g Module with your BBB via UART or via an USB interface but for both the process is same you should only change a line of code to switch between them.  
### 1. Check your device connection 
Use the following command to list peripherals connected via serial or USB ports:
```console
sudo dmesg | grep tty
```
For USB-connected modules, on modern kernels, the output typically resembles: 
```
[   55.066972] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB0
[   55.082450] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB1
[   55.111827] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB2
```
here, <br> ```/dev/ttyUSB0``` is for diag port.
<br> ```/dev/ttyUSB1``` is AT ports you can use it with ```screen/dev/ttyUSB1``` or minicom to test with AT Commands.
<br> ```/dev/ttyUSB2``` is Modem port for ppp-dial.
<br> ```/dev/ttyUSB3``` appears for module with GSM/GNSS connectivity 

if you are using UART make sure to configure your UART pins accordingly, 
```
config-pin p9.11 uart
config-pin p9.13 uart 
```

### 2. PPP dialup connection setup 
in the sub-folder /etc-ppp-peers there are three different files
```console
cd /etc/ppp/peers
```
```console
nano connect
```
navigate to the sub-directory and use the ```nano``` command to create the files and copy the contents of them there, the naming doesnt matter as long as you have renamed them in the ```simcom-pppd``` file. 
### 3. Connect 
```console
pppd call simcom-pppd &
```
once the connection is setup successfully you can use 
```console
ifconfig ppp0  
```
returns ip-info
```console
cat/etc/resolv.conf 
```
returns DNS server information
```console
route -n 
```
info about route table
```console
ping google.com 
```
check connection 

### 4. Notes
The first time I tried this with UART, I forgot to configure the 4G module's RESET pin. As a result, when I tried to reconnect, it never worked. It was such a small detail that slipped by, so be sure to take care of that.

### 5. Auto-connect upon reboot 
To make things even simpler you can use ```crontab``` to automate the connection with 4G module whenever your BBB boots up, to do this you need to setup a cron job.
1. Install crontab if you dont have it <br>
 ```console
  sudo apt-get install crontab
 ```
2. Create a scrpit file (eg. connect.sh)
```console
nano /path/to/your/connect.sh
```
```console
#!/bin/bash
pppd call simcom-pppd &
```
if you are connecting it via UART make sure to add your ```config-pin``` command aswell.
<br>3. Make it executable
```console
chmod +x /path/to/your/connect.sh
```
4. Edit the cron tab file : add this job.
```console
crontab -e
```
```console
@reboot /path/to/your/connect.sh
```

### 6. References 
SIMCom Wireless Solutions, *SIMCom A7600 Linux USB User Guide*, Version 1.01.03, 2020. [Online]. Available: [https://www.cika.com/soporte/Information/GSMmodules/A76xx/SIMCOM_A7600_Linux_USB_User_Guide_V1.01.03.pdf](https://www.cika.com/soporte/Information/GSMmodules/A76xx/SIMCOM_A7600_Linux_USB_User_Guide_V1.01.03.pdf) & [^2]


[^1]: So far I have tested this with 4 different simcom 4g modules; [SIM A7672S - Parry tech](https://www.parrytech.net/pdfs/estore/4g-lte-cat-1-modem.pdf), [SIMA7670C - Adiy](https://adiy.in/shop/a7670c-4g-breakout-board/?srsltid=AfmBOoqGAMsl9KBrl2Gx2uHFHVRvCCSguIOWmV_AcQ0x4-Kk-XLMXUrF), [SIM A7672S - Ktron](https://www.ktron.in/product/sim-a7672s-4g-lte-2g-gnss-development-board/?utm_term&utm_campaign=smartShoppingAds4&utm_source=adwords&utm_medium=ppc&hsa_acc=6332829093&hsa_cam=18276269511&hsa_grp&hsa_ad&hsa_src=x&hsa_tgt&hsa_kw&hsa_mt&hsa_net=adwords&hsa_ver=3&gad_source=1&gclid=Cj0KCQiApNW6BhD5ARIsACmEbkVmTxbHn7FiiNSSl078muuBzfWbqwM-O2sGqrxkNcrQA1QrzrupDJkaAghKEALw_wcB&v=c86ee0d9d7ed), [SIM7600EI](https://www.rhydolabz.com/sim7600ei-4g-3g-2g-gsm-gprs-gps-uart-modem-rhydolabz?srsltid=AfmBOoordT3QPU8fbNfE2PBF830dnxGE7TgfpFVYpGTon-wg9q-JQuDa).
[^2]: https://community.element14.com/products/devtools/single-board-computers/next-genbeaglebone/b/blog/posts/bbb4g 
