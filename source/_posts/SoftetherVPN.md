---
title: ���� Softether VPN �ƹ�У԰����¼��֤
date: 2017-03-04 20:54:53
tags: Hack
---

### Say Something
ԭ��DNS Tunnel�ⶫ����Ϊ��Hack���߲�С�ķ���ĳ���˿ڵ�ʱ�������ƹ�����ǽ�ģ�֮ǰ��iodine�о��Ƚ��鷳

<!--more-->

iodine Kali���ã�Debian�Ļ�`apt-get`�ͺ��ˣ�Ȼ�����Լ����������ø�ns��¼��a��¼������53udp���������Ϳͻ��˷ֱ�����iodined��iodine�����ͨ��tunnel�����ϱ��ص�ss���ؼ�������ȫ�ִ�������
Docker�÷�����

    docker run -d --cap-add NET_ADMIN -p 53:53/udp -p  -p 5555:5555/tcp -e USERNAME xxx -e PASSWORD xxx -e SPW ��������� -e HPW ����HUB���� siomiz/softethervpn

Softether VPN��ֱ�������������˵ĸ���
<br>

### Require
* ����
* ������ӳٵ͵ķ�����������������Ѷ��1Ԫѧ���ƣ��������200Mbps��
* Softether VPN

[��ȡ���룺6pwTLX]

[�ٷ�����ҳ��]
<br>

### Server
���ط��������õİ汾

    wget http://www.softether-download.com/files/softether/v4.22-9634-beta-2016.11.27-tree/Linux/SoftEther_VPN_Server/64bit_-_Intel_x64_or_AMD64/softether-vpnserver-v4.22-9634-beta-2016.11.27-linux-x64-64bit.tar.gz
    
    tar -xzf softether-vpnserver-v4.22-9634-beta-2016.11.27-linux-x64-64bit.tar.gz
    
    cd vpnserver
    
    ./vpnserver start


����VPN������������ˣ�����Windows��Server������������

![����server][����server]
<br>

![��װ������][��װ������]
<br>

![������][������]
<br>

![��ӷ�����][��ӷ�����]
<br>

![��������HUB][��������HUB]
<br>

![����û�][����û�]
<br>

![DHCP][DHCP]
<br>

![����DHCP][����DHCP]
<br>

![ICMP][ICMP]
<br>

![����][����]
<br>

![53][53]
<br>

### Client
����Windows��Client
![��װ���ӹ���][��װ���ӹ���]
<br>

![�½���������][�½���������]
<br>

![���ӷ�����][���ӷ�����]
<br>

Ȼ����**δ��¼�����������VPN**���Ե�һ��ͻ����ӳɹ�������IP��Ȼ���������ˣ��~
<br>
![δ��¼][δ��¼]
<br>
![�ٶȲ���][�ٶȲ���]


<br><br>
> **������Ͷ�ά�������ʲô����**

**֧����** 
![֧����][֧����]

**΢��**  
![΢��][΢��]

[֧����]: https://of4jd0bcc.qnssl.com/Blog/%E6%89%93%E8%B5%8F/alipay/girl_ailipay.gif?imageView2/1/w/200/h/200

[΢��]: https://of4jd0bcc.qnssl.com/Blog/%E6%89%93%E8%B5%8F/wechat/patapon_wechat.gif?imageView2/1/w/200/h/200


[��ȡ���룺6pwTLX]: https://www.jianguoyun.com/p/DezKzhwQv96jBhjQoSc%20
[�ٷ�����ҳ��]: http://www.softether-download.com/cn.aspx
[����server]: https://of4jd0bcc.qnssl.com/VPN/%E4%B8%8B%E8%BD%BDserver.png
[��װ������]: https://of4jd0bcc.qnssl.com/VPN/%E5%AE%89%E8%A3%85%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7.png
[������]: https://of4jd0bcc.qnssl.com/VPN/%E6%96%B0%E9%85%8D%E7%BD%AE.jpg
[��ӷ�����]: https://of4jd0bcc.qnssl.com/VPN/%E6%B7%BB%E5%8A%A0%E6%9C%8D%E5%8A%A1%E5%99%A8.jpg
[��������HUB]: https://of4jd0bcc.qnssl.com/VPN/%E7%AE%A1%E7%90%86%E8%99%9A%E6%8B%9FHUB.jpg
[����û�]: https://of4jd0bcc.qnssl.com/VPN/%E6%B7%BB%E5%8A%A0%E7%94%A8%E6%88%B7.png
[DHCP]: https://of4jd0bcc.qnssl.com/VPN/DHCP.jpg
[����DHCP]: https://of4jd0bcc.qnssl.com/VPN/%E5%90%AF%E7%94%A8DHCP.jpg
[ICMP]: https://of4jd0bcc.qnssl.com/VPN/ICMP.jpg
[����]: https://of4jd0bcc.qnssl.com/VPN/%E5%8A%A0%E5%AF%86.jpg
[53]: https://of4jd0bcc.qnssl.com/VPN/53.png
[��װ���ӹ���]: https://of4jd0bcc.qnssl.com/VPN/%E5%AE%89%E8%A3%85%E8%BF%9E%E6%8E%A5%E5%B7%A5%E5%85%B7.png
[�½���������]: https://of4jd0bcc.qnssl.com/VPN/%E6%96%B0%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%BD%91%E5%8D%A1.png
[���ӷ�����]: https://of4jd0bcc.qnssl.com/VPN/%E8%BF%9E%E6%8E%A5%E6%9C%8D%E5%8A%A1%E5%99%A8.png
[δ��¼]: https://of4jd0bcc.qnssl.com/VPN/%E6%9C%AA%E7%99%BB%E5%BD%95.png
[�ٶȲ���]: https://of4jd0bcc.qnssl.com/VPN/%E9%80%9F%E5%BA%A6%E6%B5%8B%E8%AF%95.png