# robotcar-v3plus-teensy4.1
Porting Elegoo UNO R3 to Teensy 4.1

**This is work in progress, so might not work correctly.**

First lets test if it can work and be seen by Arduino/Elegoo UNO R3.
After suucessfull test we will move to Teensy 4.1, so you can skip that part if you want only Teensy 4.1 test.

Connect Arduino UNO R3 via USB to your host computer, in my case Ubuntu 20.04.
```
usb-devices
```

You should see something similar to below:
```
T:  Bus=03 Lev=01 Prnt=01 Port=06 Cnt=03 Dev#=  5 Spd=12  MxCh= 0
D:  Ver= 2.00 Cls=ef(misc ) Sub=02 Prot=01 MxPS= 8 #Cfgs=  1
P:  Vendor=04d9 ProdID=b534 Rev=01.10
S:  Manufacturer=HOLTEK
S:  Product=USB TO UART BRIDGE
S:  SerialNumber=0000
C:  #Ifs= 3 Cfg#= 1 Atr=a0 MxPwr=100mA
I:  If#=0x0 Alt= 0 #EPs= 1 Cls=02(commc) Sub=02 Prot=01 Driver=cdc_acm
I:  If#=0x1 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=00 Prot=00 Driver=cdc_acm
I:  If#=0x2 Alt= 0 #EPs= 0 Cls=03(HID  ) Sub=00 Prot=00 Driver=(none)
```

We can see that this is ACM device.
Now we will see what ACM:
```
ls -l /dev | grep ttyACM
```

We can see that this is 0 (for you might be 1, 2, etc.), user is in **dialout** group and we can see that only root can control the device:
```
crw-rw----   1 root    dialout   166,     0 Mar 24  2023 ttyACM0
```

So we will change the permission to 666, in some cases you might want to try 777.
You will need sudo password for that.
```
sudo chmod 666 /dev/ttyACM0
```

Now we should check if all went OK:
```
ls -l /dev | grep ttyACM
```

and output should be like this:
```
crw-rw-rw-   1 root    dialout   166,     0 Mar 24  2023 ttyACM0
```

Now we need to add current user to dialout group. 
```
sudo adduser $USER dialout
```

Now we will add the rules to udev rules:
```
sudo touch /etc/udev/rules.d/carrobot.rules
sudo nano /etc/udev/rules.d/carrobot.rules
```
Add below lines to the file:
```
# set the udev rule , make the device_port be fixed by carrobot
#
KERNEL=="ttyACM*", ATTRS{idVendor}=="04d9", ATTRS{idProduct}=="b534", MODE:="0777", SYMLINK+="carrobot"

# Please don't forget later to restart services, so you don't need to reboot.
# sudo service udev reload
# sudo service udev restart
```
And save CTRL-S / CTRL-X

// Reboot computer. Important part as it seems.

Please download and install Arduino IDE from:
https://support.arduino.cc/hc/en-us/articles/360019833020-Download-and-install-Arduino-IDE

Now we can open Arduino IDE.
Choose Arduino Uno as board.
Paste below code to your sketch:
```
#include <car.h>
String data="Hello From Arduino!";

void setup() {
// put your setup code here, to run once:
Serial.begin(9600);

}

void loop() {
// put your main code here, to run repeatedly:
Serial.println(data);//data that is being Sent
delay(200);
}
```
SEEMS MY Arduino UNO R3 died, seems like oscylator is dead, but Teensy 4.1 arrived, so will continue with Teensy 4.1

Follow ... I will later make this from scratch, now testing if all works.
```
https://github.com/micro-ROS/micro_ros_platformio
```
After git clone in the ~/Documents/...
```
platformio project init # Initialize project
```
# Setting up environment:

pio lib install # Install dependencies
