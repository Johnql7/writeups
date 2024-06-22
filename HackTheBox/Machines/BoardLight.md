<p align="center"><img width="250px" src="https://labs.hackthebox.com/storage/avatars/7768afed979c9abe917b0c20df49ceb8.png"></p>
<h1 align="center">HackTheBox - BoardLight</h1>

<p align="center">Merhabalar, bu yazımda sizlere hackthebox platformunda bulunan "BoardLight" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![3](https://github.com/Johnql7/writeups/assets/165813191/2d1573fa-e2b5-40ad-b05f-3d9d41bd0dc5)

Sonuç olarak varsayılan portların açık olduğunu öğrenmiş olduk.

![4](https://github.com/Johnql7/writeups/assets/165813191/fd95b3cc-a9ae-4515-bf54-7165478b73dd)

Yaptığım dosya/dizin taramasıyla beraber sitede herhangi bir önemli bilgi bulamadım. Bir alt alan adı taraması gerçekleştirdiğimde ise crm adında bir alt alan adı keşfettim.

![5](https://github.com/Johnql7/writeups/assets/165813191/fddf0ece-106f-4722-8e2f-48845ccb0bd9)

Bunu da /etc/hosts dosyasına kayıt ettikten sonra etki alanını tarayıcı üzerinden açtım ve aşağıda göreceğiniz üzere dolibar yazılımının 17.0.0 versiyonunu kullandığını öğrendim.

![6](https://github.com/Johnql7/writeups/assets/165813191/e4c4d1d0-88ff-4a29-8a4f-860454e14af5)

Bu yazılımın versiyonuna uygun olarak aşağıdaki exploiti buldum.

https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253

PHP code enjeksiyonu üzerinden shell sağlayan bir exploit. İndirdikten sonra gerekli bilgileri doldurup shelli alıyorum.

![7](https://github.com/Johnql7/writeups/assets/165813191/b961e224-4e00-42fc-9da4-5a5ea700d6fb)

Daha sonra hazır bir yönetim sistemi kullandığı için config dosyasını aradım ve mysql bilgilerini buldum.

![8](https://github.com/Johnql7/writeups/assets/165813191/cbe9296d-3655-4b9e-930b-909c132385ec)

Mysql bilgilerinde bulunan şifre aynı zamanda larissa kullanıcısının da şifresiymiş. Kullanıcıya geçiş yaptıktan sonra /home dizini altından user.txt bayrağını aldım.

![9](https://github.com/Johnql7/writeups/assets/165813191/6f789a68-2831-4261-a12f-15fab92de73e)

Şimdi sıra root.txt dosyasını okumakta. Bu dosyayı okuyabilmek için root yetkisine ihtiyacımız var. Biraz önce mysql kullanıcı bilgilerini bulmuştuk. Bu bilgileri kullanarak mysql'e giriş yaptım. İçerisinde admin ve dolibarr olarak iki tane kullanıcı var. admin kullanıcısının şifresini biliyorduk zaten. dolibarr kullanıcısının şifresini de kıramadım.

find / -perm -4000 2>/dev/null komutunu kullanarak sistemde bulunan ayrıcalıklı dosyaları listelemesini istedim. Çıktıda ki dosyalardan biri ile ilgili google da aşağıda ki exploiti buldum. Sisteme yükleyip çalıştırdıktan sonra ise root yetkisine erişmiş oldum.

![10](https://github.com/Johnql7/writeups/assets/165813191/de98170a-5f73-4daa-a79c-8583f5174fe0)

https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit

![11](https://github.com/Johnql7/writeups/assets/165813191/c94177da-eb50-443b-8b14-158f1d2fc696)
