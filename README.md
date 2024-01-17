# Affected Version

Dlink DIR-816A2\_FWv1.10CNB05.

# Vulnerability Description

Dlink DIR-816A2\_FWv1.10CNB05 was discovered to contain a command injection via parameter wizardstep4\_ssid\_2.

# Firmware download address

Manufacturer's address: <https://www.dlink.com/>

Firmware download address: [D-Link Technical Support (dlink.com.cn)](http://www.dlink.com.cn/techsupport/AllPro.aspx)

![image.png](https://note.youdao.com/yws/res/18434/WEBRESOURCEd07e56b41165a99f331603b5488ec036)

# Vulnerability Details

Dlink DIR-816A2\_FWv1.10CNB05 is affected by a remote command injection vulnerability.  We can construct a parameter wizardstep4\_ssid\_2 that includes command execution, and when we request /goform/dir\_setWanWifi, it will be sub\_ 42DA54 function processing, ultimately leading to command execution.

We can perform command injection through parameter wizardstep4\_ssid\_2:

Obtain parameters wizardstep4\_ssid\_2 to v19, ultimately write them into v25, and then execute them in doSystem.

![image.png](https://note.youdao.com/yws/res/18440/WEBRESOURCEfd83f70b9c4786bf46aea64181650715)

# Recurring vulnerabilities and POC

We can directly enter in the address bar:

    http://192.168.1.25/goform/dir_setWanWifi?connecttype=DHCP&statuscheckpppoeuser=0&wizardstep4_ssid_2=123456;mkdir dingjiayu

![image.png](https://note.youdao.com/yws/res/18464/WEBRESOURCEaa29f130030b276853294d03dc37d039)

The following is the parameter status before entering the doSystem function in gdb:

![image.png](https://note.youdao.com/yws/res/18442/WEBRESOURCE6c2fa7cb9accd25e61ca9e61281d31ac)

When entering doSystem, check the parameters passed in through register A0:

![image.png](https://note.youdao.com/yws/res/18452/WEBRESOURCE7ca6786df0a8297cccd96b34c23d840e)

Now we continue to execute and find that the command has been executed, creating the dingjiayu folder:

![image.png](https://note.youdao.com/yws/res/18456/WEBRESOURCE626b80432a3ecf5b89d01b377f3ebc3c)

And you can write your own exp to get the root shell.
