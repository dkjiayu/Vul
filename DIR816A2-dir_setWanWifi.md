# Affected Version

Dlink DIR-816A2\_FWv1.10CNB05.

# Vulnerability Description

Dlink DIR-816A2\_FWv1.10CNB05 was discovered to contain a command injection via parameter wizardstep4\_ssid\_2.

# Firmware download address

Manufacturer's address: <https://www.dlink.com/>

Firmware download address: [D-Link Technical Support (dlink.com.cn)](http://www.dlink.com.cn/techsupport/AllPro.aspx)

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/1.png?raw=true)

# Vulnerability Details

Dlink DIR-816A2\_FWv1.10CNB05 is affected by a remote command injection vulnerability.  We can construct a parameter wizardstep4\_ssid\_2 that includes command execution, and when we request /goform/dir\_setWanWifi, it will be sub\_ 42DA54 function processing, ultimately leading to command execution.

We can perform command injection through parameter wizardstep4\_ssid\_2. The same effect can also be achieved through parameter wizardstep4\_ssid\_5:

Obtain parameters wizardstep4\_ssid\_2 to v19, ultimately write them into v25, and then execute them in doSystem.

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/2.png?raw=true)

# Recurring vulnerabilities and POC

We can directly enter in the address bar:

    http://192.168.1.25/goform/dir_setWanWifi?connecttype=DHCP&statuscheckpppoeuser=0&wizardstep4_ssid_2=123456;mkdir dingjiayu

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/3.png?raw=true)

The following is the parameter status before entering the doSystem function in gdb:

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/4.png?raw=true)

When entering doSystem, check the parameters passed in through register A0:

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/5.png?raw=true)

Now we continue to execute and find that the command has been executed, creating the dingjiayu folder:

![image.png](https://github.com/dkjiayu/Vul/blob/main/Pic/6.png?raw=true)

And you can write your own exp to get the root shell.
