# CrickeyConCTF 2017

While I wasn't able to attend the conference itself, [@bull](https://twitter.com/robertwinkel?lang=en) and [@dooktwit](https://twitter.com/dooktwit?lang=en) were kind enough to keep the CTF up and running post conference. There were limited challenges avaliable to complete, however, there were still some awesome challenges on offer. Check out my writeups below.

This CTF was good practice for being tenacious with flag submission. Remember, if it doesn't submit the first time, try playing around with the formatting.

# Challenges

## Misc

### Twit Twoo! - 100pts
```
"I assume you are following @CrikeyCon?"
```
CRTL + F On the [CrickeyCon Twitter](https://twitter.com/crikeycon?lang=en) page for "flag" and first hit - there is a tweet from Feb 24 2017!
```
"flag{CrikeyConCTF_2017}"
```

### Ducktype - 100pts
```
"What is the ONLY ducktype to be?"
```
"There's 2 types of ducks. The legendary and amazing 'flat' ducks. And the lowly scummy 'upright' ducks." - @dook

[The Ducksec Party (@ducksecparty)](https://twitter.com/ducksecparty) is to blame for this one ;)

```
flag{flat}
```
## Crikeyconctf - 200pts

Get the flag from crikeyconctf.dook.biz

And it's NOT on the scoreboard!!! (gx). or this server

dig deeper

Hmm why would they call out dig deeper specifically... well there is the dig tool which allows you to run a DNS query on a host, that seems like a great place to start.

I used this resource to explore the different dig options: https://www.cyberciti.biz/faq/linux-unix-dig-command-examples-usage-syntax/

I tried a few DNS dig queries, but this one worked out:
```
dig crikeyconctf.dook.biz TXT

; <<>> DiG 9.10.3-P4-Ubuntu <<>> crikeyconctf.dook.biz TXT
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33443
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;crikeyconctf.dook.biz.		IN	TXT

;; ANSWER SECTION:
crikeyconctf.dook.biz.	3598	IN	TXT	"flag{h1dd3n_r3c0rds}"

;; Query time: 411 msec
;; SERVER: 127.0.1.1#53(127.0.1.1)
;; WHEN: Thu Mar 09 08:43:22 AEDT 2017
;; MSG SIZE  rcvd: 83
```
### Dot Dash - 500pts

Well...you can't find it...But i made a video for you :)

https://dook.biz/dotdash.mp4

(Short greens are spaces, long green is end of string)

So with the title... already i'm taking a guess that this *may* be morse code.

Not gonna lie, this was the most challenging to transpose from video into morse code! Certainly helped to plug this into VLC media player and drop the play back speed.

I was seriously hung up on this one thinking the flag was:  flag{sos_crikey_con_ftw_sez_droppy}

But alas it was: flag{sos_crikeyctf_ftw_sez_droppy}

## Craptolocker

### Craptolocker 1 - 100pts

The following web page was left by a hacker, who is using ransomware to hold my web site hostage. I am a poor student and can't affort to pay the ransom. Can you help?

http://ctf.crikeycon.com:1234
The 1st step of this challenge is to identify the hacker.
Submit the flag as: flag{hacker_handle}

Viewing the source code gives us:

```
<!-- Page made by dr0ppyb3@r_h@ck3r ->
```
Pop that into flag format and we have a winrar.
```
flag{dr0ppyb3@r_h@ck3r}
```
### Craptolocker 2 - 200pts

The 2nd step is to find the source code of Craptolocker.
The source code contains the flag. (Look on the Internet)

Cheeky google of the handle 'dr0ppyb3@r_h@ck3r' reveals some interesting pastebin links!

http://103.1.172.112/archieves/index.php?q=aHR0cDovL3Bhc3RlYmluLmNvbS9aZHU0aWJnZQ%3D%3D

Looking at the source code:
```
import string
import random
# written by dr0ppyb3@r_h@ck3r
# v0.3 - The release version will be much better
# Updated flag: flag{h1d3_0n_p@steb1n}
```
flag{h1d3_0n_p@steb1n}

### Craptolocker 3 - 400pts

The 3rd step is to create a decryption program by reversing the encryption routines in the source code. I have attached one of the encrypted files for you to test your decryption.
The flag is contained in the decrypted file.

### Craptolocker 4 - 500pts

Thanks for helping me decrypt my files! After I decrypted them, I found an extra file amongest them that is not one of mine. It looks like the hacker's calling card. I'm hoping his OPSEC is as bad as his morals. Can you find out his real name?

Submit the flag as: flag{Firstname Lastname}

Alas, I wasn't able to solve this one, I definately went too far down the rabbit hole. Popping the image into an EXIF data viewer shows the GPS information of the photo as located in noosa. Looking up the location on google maps, reveals a travel agency called "Drop bear 4x4 tours".

After scouring their website and trying a few different names, no luck~! I even looked through their social media, performed a reverse image lookup and combined that with the location which did not reveal much in terms of results.

Would be keen to find out how this one was solved!

## Trivia

### Aussie Cons 1 - 100pts

What con has now branched out into skin care?

Unrestcon

They didn't renew their domain name, and someone else within the skincare business purchased it!


### Aussie Cons 2 - 100pts

What Aussie hacker con is next after CrikeyCon?

Bsides

### Aussie Cons 3 - 100pts

Which con uses this logo?

Playtapus con

### Aussie Cons 4 - 100pts

Which con loves their pyro?

Kiwicon

### Aussie Cons 5 - 100pts

Which Aussie con is a secret underground hacker con and invite only?

Wahckon

### Aussie Cons 6 - 100pts

What is Australia's longest running hacker con?

Ruxcon

### Aussie Cons 7 - 100pts

What is the last name of the speaker who was on at 3:30 at CrikeyCon 2

https://infocon.org/cons/CrikeyCon/CrikeyCon%202%20-%202015/

Some googling leads me to a great resource for people who can't make it to the conference (for whatever reason!)

Looks like it was Michael Gianarakis presenting his 'iOS Runtime Hacking Crash Course' @ 3:30.

Gianarakis

### Captain Crunch

Which iconic hacker was recently in Brisbane (but did not attend the SecTalks social meetup while here?)

Kevin Mitnick

The hint here is in the title. John Draper, a legendary figure in the hacking community, who received his nickname after using a toy whistle that came in a box of cereal to [perform phone phreaking](https://www.youtube.com/watch?v=ZlJRbtCXbSM).

Kevin Mitnick was a fan of John, and followed in his footsteps performing phone freaking, as well as social engineering and hacking company systems (which ultimately becoming one the of FBI's most wanted hackers).

Kevin used Captain Crunch's real name, John Draper, as a pseudonym on a guest sign-in sheet when he was breaking into Pacific Bell's Los Angeles phone center. He was also recently travelling around Australia as part of his conference ["Mitnick Live"](www.mitnicklive.com/).

### Aussie Cons 8 - 100pts

What lock picking focused con is making its debut in Melbourne this year?

ozlockcon

### Elliot - 100pts

What sort of 'town' does Elliot Alderson live next to?

bo-hai-dumpling

> If you have not yet watched the Mr.Robot TV series I highly recommend it!

### Try Harder - 100pts

What country is the artist who sings the Offensive Security 'Try Harder' song originally from?

You can literally google "artist who sings the Offensive Security" and your answer is the first hit ;)

https://en.wikipedia.org/wiki/Uzimon

"Uzimon aka Daniel Frith is a Dancehall Reggae artist originally from Bermuda"

flag{bermuda} - would only accept flag in this format

## Hacker 101

### Hash 1 - 100pts

Crack Me! - flag{nyy_zvkrq_hc}

http://www.richkni.co.uk/php/crypta/caesar.php

Processed text - shift 13
ALL_M IXED_ UP

all_mixed_up - would only accept lowercase text

### Hash 2 - 100pts

Crack Me! - flag{538428724535beb35f2b1e58d1b20366}

This looks like an MD5 hash, due to the length and general mix of numbers and lower case letters. Once you have seen a few MD5 hashes you will become used to spotting them.

Chuck the hash into google and ohhhh yep we have our flag. Seems like is is an MD5 hash of the word 'crikey'

MD5 (crikey):, 538428724535beb35f2b1e58d1b20366

crikey

### Hash 3 - 100pts

Crack Me! - flag{YWNlX29mX2Jhc2U2NA==}

Now this definitely a BASE64 string due to the "==" padding at the end of the string. If you thing "Why does a base 64 string have == at the end?" then check out this stackoverflow link to learn more about it. [stackoverflow](http://stackoverflow.com/questions/6916805/why-does-a-base64-encoded-string-have-an-sign-at-the-end). It is important to note that base64 is a form of encoding, not encryption, and should not be used to hide sensitive information within applications as it is easily reversed.

Jump onto https://www.base64decode.org/ and pop in our string to reveal the flag.

ace_of_base64

### Hash 4 - 100pts

Crack Me! - flag{6061863623f806d7db7ac8fa8f9e370bbbe9c095}

Chuck this into google annnnd we have a hit, looks like our SHA1 was a hash of the word "nsa".

SHA-1 (nsa):, 6061863623f806d7db7ac8fa8f9e370bbbe9c095

nsa

### Hash 5 - 100pts

How come Mimikatz didn't find this one? Crack Me! - flag{5ac75b186b060b343ef4cf6467667e8f}

Popped this into https://hashes.org/search.php which gave me the answer

password123456
However I was still interested to find out what type of hash this was. If you pop this into https://crackstation.net/ it tells you  that is was an NTLM hash.

Hash	Type	Result
5ac75b186b060b343ef4cf6467667e8f	NTLM	password123456

### Hash 6 - 100pts

Crack Me! - See attached files

We are provided with a passwd and shadow file

So if you open these files in notepad you should see the following:

passwd

```

user1:x:1001:1002:,,,:/home/user1:/bin/bash

```

shadow

```

user1:$6$40TDWfsr$lZAEHfqu2d9TnZZPLixtxCJM1dj3cSf26hkFr/PbjdCuTWuVuNwCxYSaIfHUSwAsBfFHYUPCIpbOv47K96lj/.:17187:0:99999:7:::

```

Pentesterlab has a great blog post on this explaining the passwd file:

> the /etc/passwd file which contains all the accounts of the remote system.The next image is showing the list of the local accounts of the machine that we have compromised.Lets analyse the information that we can obtain from the first account which is root.The first field indicates the username,the field x means that the password is encrypted and it is stored on the /etc/shadow file.

https://pentestlab.blog/2012/07/23/dumping-and-cracking-unix-password-hashes/

> in unix systems the password hashes are stored in the /etc/shadow location

So power up Kali and go ahead and pop your passwd and shadow files on the desktop.

```
root@kali:~/Desktop# cat passwd > passwords.txt
root@kali:~/Desktop# cat shadow > shadow.txt
root@kali:~/Desktop# unshadow
Usage: unshadow PASSWORD-FILE SHADOW-FILE
root@kali:~/Desktop# unshadow passwords.txt shadow.txt
user1:$6$40TDWfsr$lZAEHfqu2d9TnZZPLixtxCJM1dj3cSf26hkFr/PbjdCuTWuVuNwCxYSaIfHUSwAsBfFHYUPCIpbOv47K96lj/.:1001:1002:,,,:/home/user1:/bin/bash
root@kali:~/Desktop# unshadow passwords.txt shadow.txt >cracked.txt
root@kali:~/Desktop# ls
cracked.txt  passwd  passwords.txt  shadow  shadow.txt
root@kali:~/Desktop# cat cracked.txt
user1:$6$40TDWfsr$lZAEHfqu2d9TnZZPLixtxCJM1dj3cSf26hkFr/PbjdCuTWuVuNwCxYSaIfHUSwAsBfFHYUPCIpbOv47K96lj/.:1001:1002:,,,:/home/user1:/bin/bash
root@kali:~/Desktop# john /root/Desktop/cracked.txt
Warning: detected hash type "sha512crypt", but the string is also recognized as "crypt"
Use the "--format=crypt" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (user1)
1g 0:00:00:01 DONE 2/3 (2017-03-13 20:14) 0.7936g/s 759.5p/s 759.5c/s 759.5C/s helpme..john
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```
Once complete, we can see that user1 had the password "football".

flag{football}


Barcodes

Foo Bar - 300pts

Provided with a jpg image

But it is torn into pieces. I popped this into photoshop and pieced the image back together.

After heading over to https://images.google.com and searching with the newly created image, we see there are many images created with this style. Thats because it is a feature used within Facebook's messenger app. Circular patterns around a user's profile photo can be used to start a chat with that person.

I am not a facebook user, so this was something new for me. I downloaded the facebook messenger app and used my partner's account to scan this image and  start a chat.

After scanning the barcode we are provided with a chap called "Fabio Mandap" who lives in Canberra, ACT and also Studied at university of Canberra. Go ahead and add them on messenger.

So at first I tried messenging this mysterious (confirmed fake) person, to no avail. However looking at [Fabio's facebook page](facebook.com/fabio.mandap) revealed the flag posted on his wall.

flag{g0anna_and_yams}
