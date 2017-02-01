# Network Attack - HSRP? More Like Easy Peasy

In learning about network penetration testing, HSRP is one of the more common vulnerabilities that is quick and easy to identify on a target network. Don't overlook HSRP on your network penetration tests, as more often than not, it is using the default configuration *(spoiler: the default password is 'cisco')* making it a quick win vulnerability.

## What is HSRP?

HRSP is a networking protocol known as the *Hot Standby Router Protocol*, which is implemented for redundancy purposes. It is known as a [first hop redundancy protocol (FHRP)](https://en.wikipedia.org/wiki/Category:First-hop_redundancy_protocols), which provides the ability to assign two or more routers as a backup to the default gateway on the network. So if the main gateway fails, instead of the network just falling over, the assigned backup router will take over.

HSRP isn't the only FHRP out there:

* [Virtual Router Redundancy Protocol (VRRP)](https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol)

* [Common Address Redundancy Protocol (CARP)](https://en.wikipedia.org/wiki/Common_Address_Redundancy_Protocol)

* [Extreme Standby Router Protocol (ESRP)](http://www.techopsguys.com/2009/12/02/extremely-simple-redundancy-protocol/)

* [Gateway Load Balancing Protocol (GLBP)](https://en.wikipedia.org/wiki/Gateway_Load_Balancing_Protocol)

* [Routed Split multi-link trunking (R-SMLT)](https://en.wikipedia.org/wiki/Multi-link_trunking#R-SMLT)

* [NetScreen Redundancy Protocol (NSRP)](http://help.juniper.net/help/english/6.3.0/nsrp_cluster_cnt.htm)

Some fun facts about HSRP:

* HSRP runs on the UDP port 1985, which is handy to know if you are writing your own script to automate targeting this protocol.

* Authentication is enabled by default, so if no authentication data is configured during setup, it defaults to using `cisco` as the password.

* This is a Cisco proprietary protocol, making it one of the more common protocols you can find on any network, as Cisco currently the [largest networking company in the world.](https://en.wikipedia.org/wiki/Cisco_Systems)

If you want to delve deeper into the protocol detail check out the [RFC 2281](https://tools.ietf.org/html/rfc2281) which details version 1 or the [cisco documentation](http://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipapp_fhrp/configuration/xe-3s/fhp-xe-3s-book/fhp-hsrp-v2.html) for version 2. Check out the [HSRP writeup on NetworkSorcery](http://www.networksorcery.com/enp/protocol/hsrp.htm) for more detail about HSRP from a networking perspective.

## Known Vulnerabilities

Overall HSRP is **not** [a secure protocol](http://www.securiteam.com/exploits/5CP051F4AK.html). There are 3 key vulnerabilities I am interested in exploring when I encounter HSRP on a penetration test, including:

* Man In The Middle (MITM)
* Denial of Service (DoS)
* Hashing Weakness

####Man In The Middle (MITM)

When two parties communicate with each other over a network, there is a trusted relationship where they believe they are communicating directly with each other. A MITM attack exploits this trust by relaying communications between the two parties through the attacking machine, allowing the attacker to eavesdrop on all communications.

First, an attacker would need access to the physical LAN to capture traffic to valdiate that HSRP is being used across the network. **By default, HSRP authentication is done via clear text over the network.** By intercepting the authentication credentials, an attacker would read the password and could then attempt to spoof a valid HSRP session. In addition to this, the default credentials used for HSRP is `cisco` making it trivial to guess.

> If the password is [very weak](http://www.computerworld.com/article/3024404/security/worst-most-common-passwords-for-the-last-5-years.html), you've also got the beginnings of a weak credentials vulnerability! Use the credentials that you find to support your bruteforcing and/or scanning of the further network. When password re-use is common throughout the network infrastructure, you will very quickly own all of the infrastructure that uses it.


####Denial Of Service (DoS)

> **Note: It should go without saying that exploiting a Denial Of Service (DoS) vulnerability during a penetration test should be avoided due to its destructive nature - exploit at your own risk.**

As part of normal operations, network services process requests recieved from infrastructure or other users. The processing required to service these requests is easily computed by the network device. However, a DoS attack exploits the input recieved as part of normal operations by flooding the device with a high number of requests, which overwhelms the device and stops the service from functioning.

Once an attacker has spoofed a valid HSRP session, they could change a variety of settings to cause havoc on the network, including:

* Change the priority level of devices, and can cause devices to ["fight" for priority.](http://seclists.org/bugtraq/2001/May/34)
* Route all traffic through a specific host. If the target host is not equipped to recieve such a high rate of traffic, you may not only bring it down, but potentiall damage the hardware as well.

####Hashing Weakness

In an attempt to improve authentication, Cisco implemented the ability to configure HRSP to hash passwords using MD5, which if you don't already know, [is totally super secure](http://security.stackexchange.com/questions/52461/how-weak-is-md5-as-a-password-hashing-function). The password cracking tool John The Ripper has added support to assist you in [cracking HSRP MD5 authentication](http://www.openwall.com/lists/john-users/2014/09/02/1) if you encounter it.

## Exploitation

Your primary toolkit for tackling HSRP should include:

* [Wireshark](https://www.wireshark.org/docs/dfref/h/hsrp.html)
* [Nmap](https://nmap.org/nsedoc/scripts/broadcast-listener.html)
* [Yersinia](http://tools.kali.org/vulnerability-analysis/yersinia)

While this writeup focuses on Wireshark and Nmap, check out [this](https://letusexplain.blogspot.com.au/2015/10/hacking-cisco-hsrp-with-kali.html) writeup on using Yersinia. While it is a bit dated, it will give you a feel for crafting your own HSRP attack. As Yersinia is a [Kali Linux framework](http://tools.kali.org/vulnerability-analysis/yersinia) for performing layer 2 attacks, there is much more I would like to cover than simply HSRP. Once i've had time to get to know Yersinia better, i'll post a full writeup.

To get familiar with the protocol before you head out to the network, you can practice going through these example packet captures [here](https://github.com/kholia/my-pcaps/tree/master/HSRP/packet-captures). I can't stress this repo enough - its an awesome resource to learn more about these vulnerabilities outside of a network penetration test.

> Capturing packets during a network penetration test is really your bread and butter. It assist you in identifying vulnerabilities over the wire and provides evidence of what traffic was sent from your machine during testing. View your packet captures during testing to validate that your scans are targeting the correct hosts or to help you in troubleshooting any network issues.

### Wireshark Step by Step:

1. For packet capture on a target network, Wireshark is my tool of choice. Plug into the LAN and start to capture packets. If you don't have Wireshark already, download it [here](https://www.wireshark.org/download.html).
2. You're going to get lots of noise from a straight capture of a network, so enter the `hsrp` filter into wireshark to show only the HSRP traffic.
3. Start inspecting packets
4. `Authentication Data: Default (cisco)` indicates that the [default authentication](http://www.packetu.com/2012/11/06/hsrp-default-authentication/) is being used, which is the password `cisco`.
5. `Authentication Data: Non-Default ()` indicates that a custom password has been configured.
6. `MD5 Authentication TLV: Type=4 Len=28` in wireshark, this will be an expandable option.
7. `MD5 Authentication Data: 482c811da5d5b4bc6d497ffa98491e38` Once you have exapnded that option, you will be able to view the MD5 hash. In this example this is an MD5 hash of the string is `password123`.

### Nmap Step by Step

Nmap comes with an inbuild script called ['broadcast-listener'](https://nmap.org/nsedoc/scripts/broadcast-listener.html) which automates sniffing and decoding of packets, including HSRP.

1. Run the command`nmap --script broadcast-listener -e eth0` to start listening for network broadcasts.
2. View the output for detail about the hosts that are using HSRP. An example output from this script would look like:
```
broadcast-listener:
|   udp
|       HSRP
|         ip             version  op     state   prio  group  secret  virtual ip
|         192.168.0.254  0        Hello  Active  110   1      cisco   192.168.0.253
```
3. Depending on the penetration test, using Nmap to as an additional validation to your wireshark capture to confirm your HSRP finding, may be as far as your client is comfortable to go.
4. However, if you do have permission to forge packets during your testing, [PacketLife](http://packetlife.net/blog/2008/oct/27/hijacking-hsrp/) has a detailed writeup on using Scapy to forge HSRP packets using the prebuilt libraries that come with it.

## Remediation

It is definately a quick win to secure your HSRP implementation, as it would make it more difficult for an attacker to successfully exfiltrate information or to move laterially throughout the network.

* Cisco has recommended [implementing HSRP with IPSec](http://www.securiteam.com/exploits/5CP051F4AK.html) to prevent protocol spoofing.

* Configure a strong, complex password and ensure that it is configured to use MD5 hashing (This is currently the best option for authentication that Cisco has made avaliable for HSRP at the time of writing). Unless an attacker is able to easily crack the MD5 string, they will get a `Bad authentication` error when attempting to spoof HSRP packets.

* It is also recommended to implement [Access Control Lists (ACLs)](http://hakipedia.com/index.php/HSRP_Manipulation#Mitigation) to provide additional protection against HSRP packet forgery.

## References

Here are the references i've linked throughout my post, happy reading.

*What is HSRP?*

1. https://en.wikipedia.org/wiki/Category:First-hop_redundancy_protocols
2. https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol)
3. https://en.wikipedia.org/wiki/Common_Address_Redundancy_Protocol)
4. http://www.techopsguys.com/2009/12/02/extremely-simple-redundancy-protocol/
5. https://en.wikipedia.org/wiki/Gateway_Load_Balancing_Protocol
6. https://en.wikipedia.org/wiki/Multi-link_trunking#R-SMLT
7. http://help.juniper.net/help/english/6.3.0/nsrp_cluster_cnt.htm
8. https://en.wikipedia.org/wiki/Cisco_Systems
9. https://tools.ietf.org/html/rfc2281
10. http://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipapp_fhrp/configuration/xe-3s/fhp-xe-3s-book/fhp-hsrp-v2.html
11. http://www.networksorcery.com/enp/protocol/hsrp.htm

*Known Vulnerabilities*
1. http://www.securiteam.com/exploits/5CP051F4AK.html
2. http://www.computerworld.com/article/3024404/security/worst-most-common-passwords-for-the-last-5-years.html
3. http://security.stackexchange.com/questions/52461/how-weak-is-md5-as-a-password-hashing-function
4. http://www.openwall.com/lists/john-users/2014/09/02/1

*Exploitation*
1. https://www.wireshark.org/docs/dfref/h/hsrp.html
2. https://nmap.org/nsedoc/scripts/broadcast-listener.html
3. http://tools.kali.org/vulnerability-analysis/yersinia
4. https://letusexplain.blogspot.com.au/2015/10/hacking-cisco-hsrp-with-kali.html)
5. http://tools.kali.org/vulnerability-analysis/yersinia
6. https://github.com/kholia/my-pcaps/tree/master/HSRP/packet-captures
7. https://www.wireshark.org/download.html
8. http://www.packetu.com/2012/11/06/hsrp-default-authentication/
9. https://nmap.org/nsedoc/scripts/broadcast-listener.html
10. http://packetlife.net/blog/2008/oct/27/hijacking-hsrp/

*Remediation*
1. http://www.securiteam.com/exploits/5CP051F4AK.html
2. http://hakipedia.com/index.php/HSRP_Manipulation#Mitigation










