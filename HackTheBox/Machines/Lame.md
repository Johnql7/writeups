<p align="center"><img width="250px" src="https://labs.hackthebox.com/storage/avatars/fb2d9f98400e3c802a0d7145e125c4ff.png"></p>
<h1 align="center">HackTheBox - Lame</h1>

<p align="center">Merhabalar, bu yazımda sizlere hackthebox.com platformunda bulunan "Lame" isimli makinenin çözümünü göstereceğim. İlk önce nmap aracını kullanarak makinede açık olan portları görüntüleyeceğim.</p>

![2](https://github.com/Johnql7/writeups/assets/165813191/432a04ae-6aeb-41a2-b80d-b49b3125533f)

Tarama sonucunda ftp, ssh ve smb servislerinin açık olduğunu öğrendik. Ayrıyeten ftp servisine anonymous kullanıcı olarak giriş yapabileceğimizin bilgisini vermiş. anonymous olarak ftp servisine giriş yaptığımda herhangi bir dosya ile karşılaşmadım. Bunun dışında sonradan vsftpd'nin 2.3.4 versiyonu kullandığını fark ettim. Bu versiyonuna ait bir exploiti metasploitable2 üzerinde denediğimden bir güvenlik açığı bulunduğunu biliyorum. msfconsole'u açtım ve exploiti seçtim. Ayarlamaları yaptıktan sonra çalıştırdım fakat zafiyeti sömüremediğinden bir shell elde edemedim.

SMB servisine gelecek olursak, /tmp paylaşımını görüntüleme ve yazma iznimiz var. Ancak smbclient ile bağlanamadığım için içeriğini görüntüleyemiyorum. Görüntülesem bile içinde önemli bir şey olabileceğini sanmıyorum.

![7](https://github.com/Johnql7/writeups/assets/165813191/d6f301ce-2b91-461a-85f2-db8dad84c3f2)

Versiyonundan yola çıkarsakta metasploitte usermap_script adında bir exploit ile karşılaşırız. 

![3](https://github.com/Johnql7/writeups/assets/165813191/fff4fa07-a84a-4136-8001-6972afa58f2e)

Gerekli verileri girdikten sonra exploiti çalıştırdım ve /home/makis ve /root dizini altından bayrakları aldım.

![6](https://github.com/Johnql7/writeups/assets/165813191/2bf9ba0c-3029-4ea2-a034-5079ae12bcb5)
