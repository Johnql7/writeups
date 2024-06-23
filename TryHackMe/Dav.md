<p align="center"><img width="200px" src="https://tryhackme-images.s3.amazonaws.com/room-icons/cb525f3e7944eb5eec637698f48b6844.jpeg"></p>
<h1 align="center">TryHackMe - Dav</h1>

<p align="center">Merhabalar, bu yazımda sizlere tryhackme platformunda bulunan "Dav" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![1](https://github.com/Johnql7/writeups/assets/165813191/2f335931-949d-4ef4-b2ab-470f2a25e0bd)

Sadece HTTP servisi açık görünüyor ve yine aynı şekilde ubuntunun varsayılan sayfası mevcut sitede.

![2](https://github.com/Johnql7/writeups/assets/165813191/a42064a4-7741-4652-84a3-4ca7a76306e6)

Kaynak kodunda herhangi bir bilgi bulamadım. Daha fazla bilgi edinmek için dosya/dizin taraması gerçekleştireceğim.

![3](https://github.com/Johnql7/writeups/assets/165813191/fc49fc8b-069d-48fb-a2d0-3697c4ecf543)

Tarama sonucunda webdav diye bir dizin keşfettim. 401 durum kodunu döndürmüş. Bu da bizden bir kullanıcı kimliği istediği anlamına geliyor. Webdav'ın dosya yükleme, değiştirme gibi işlemleri yapmak için kullanılan bir servis olduğunu öğrendim. Kullanıcı kimliği için ise internette "webdav default credentials" şeklinde bir arama yaptım ve çıkan ilk sayfada ki bilgiler (wampp:xampp) ile giriş yapabildim.

![4](https://github.com/Johnql7/writeups/assets/165813191/05393d53-b6d9-4743-a820-9862653b17b4)

Giriş yaptıktan sonra yüklenen dosyaları görebiliyoruz. Ayrıyeten davtest ve cadaver gibi araçlar ile içerisine dosya yükleyebiliriz. Ben davtest aracı ile içerisine shell kodumu ekleyeceğim.

![5](https://github.com/Johnql7/writeups/assets/165813191/a093a133-d923-4d60-8a94-b0f81d0ea6e6)

Dosyayı yükledikten sonra tarayıcı vasıtasıyla çalıştırdım ve dinlediğim port'a bağlantımı aldım. Daha sonra ise user.txt bayrağını almak için /home/merlin dizinine gittim.

![6](https://github.com/Johnql7/writeups/assets/165813191/b5186d93-851d-432d-84f6-70ec303b078a)

sudo -l komutunu kullandığımda ise root yetkisinde /bin/cat dosyasını çalıştırabileceğimi gördüm ve bu dosyayı root.txt dosyasını okumak için kullandım.

![8](https://github.com/Johnql7/writeups/assets/165813191/f09e1b53-e555-4669-88b9-0eee973d96d2)
