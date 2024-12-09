# BBB4G

### The aim of this repo is to help people interface their BBB with any[^1] 4G modules available in the market. 
You can connect your 4g Module with your BBB via UART or via an USB interface but for both the process is same you should only change a line of code to switch between them.  
#### 1. Check your device connection 
```console
sudo dmesg | grep tty
```
this command will list out all the peripherals connected with all the serial ports and USB ports, if you are connecting your module via USB in the latest kernel you will get the following output. 
```
[   55.066972] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB0
[   55.082450] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB1
[   55.111827] usb 1-1: GSM modem (1-port) converter now attached to ttyUSB2
```
here, <br> /dev/ttyUSB0 is for diag port.
<br> /dev/ttyUSB1 is AT ports you can use it with screen/dev/ttyUSB1 or minicom to test with AT Commands.
<br> /dev/ttyUSB2 is Modem port for ppp-dial.

### 2. PPP dialup connection setup 
in the sub-folder /etc-ppp-peers there are three different files
```console
cd /etc/ppp/peers
```
```console
nano connect
```
navigate to the sub-directory and use the ```nano``` command to create the files and copy the contents of them there, the naming doesnt matter as long as you have renamed them in the ```simcom-pppd``` file. 
### 3. connect 
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

[^1]: So far I have tested this with 4 different simcom 4g modules; [SIM A7672S - Parry tech](https://www.parrytech.net/pdfs/estore/4g-lte-cat-1-modem.pdf), [SIMA7670C - Adiy](https://adiy.in/shop/a7670c-4g-breakout-board/?srsltid=AfmBOoqGAMsl9KBrl2Gx2uHFHVRvCCSguIOWmV_AcQ0x4-Kk-XLMXUrF), [SIM A7672S - Ktron](https://www.ktron.in/product/sim-a7672s-4g-lte-2g-gnss-development-board/?utm_term&utm_campaign=smartShoppingAds4&utm_source=adwords&utm_medium=ppc&hsa_acc=6332829093&hsa_cam=18276269511&hsa_grp&hsa_ad&hsa_src=x&hsa_tgt&hsa_kw&hsa_mt&hsa_net=adwords&hsa_ver=3&gad_source=1&gclid=Cj0KCQiApNW6BhD5ARIsACmEbkVmTxbHn7FiiNSSl078muuBzfWbqwM-O2sGqrxkNcrQA1QrzrupDJkaAghKEALw_wcB&v=c86ee0d9d7ed), [SIM7600EI](https://www.rhydolabz.com/sim7600ei-4g-3g-2g-gsm-gprs-gps-uart-modem-rhydolabz?srsltid=AfmBOoordT3QPU8fbNfE2PBF830dnxGE7TgfpFVYpGTon-wg9q-JQuDa).
