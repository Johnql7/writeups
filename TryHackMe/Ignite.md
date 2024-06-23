<p align="center"><img width="200px" src="https://tryhackme-images.s3.amazonaws.com/room-icons/676cb3273c613c9ba00688162efc0979.png"></p>
<h1 align="center">TryHackMe - Ignite</h1>

<p align="center">Merhabalar, bu yazımda sizlere tryhackme platformunda bulunan "Ignite" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![3](https://github.com/Johnql7/writeups/assets/165813191/03503189-64d7-4b41-b127-4ede9b0ffb26)

Görünüşe göre sadece http servisi açık ve üzerinde bir hazır içerik yönetim sistemi olan fuelcms çalışıyor.

![4](https://github.com/Johnql7/writeups/assets/165813191/6d27e67a-a9bc-41b7-9795-5e57e43acde3)

fuel cms'in 1.4 versiyonunu kullanıyormuş. Bununla ilgili internetten aşağıda ki exploiti buldum.

https://github.com/ice-wzl/Fuel-1.4.1-RCE-Updated

'+pi(print($a='system'))+$a('komut buraya')+'

Bu exploit kod enjeksiyonu zafiyetinden yararlanıyor gibi duruyor. Yukarıda ki kodu url encode edip /fuel/pages/select/?filter= uzantının sonuna ekliyor ve çalıştırıyor. Bu sayede istediği komutu yürütebiliyor. Yapacağı işi yapabilmek için siteye login olması gerekiyor öncelikle. Kaldı ki anasayfada kullanıcı ve şifresini default olarak admin admin olduğu belirtilmiş.

![5](https://github.com/Johnql7/writeups/assets/165813191/28b1a471-b3e9-421c-9f35-c028f593f0c9)

Bu kullanıcı bilgileri ile sisteme giriş yaptıktan sonra verilen kodun içerisine shell bağlantısı elde edebileceğim bir komut koydum, encode ettim ve verilen uzantının sonuna ekledim.

![6](https://github.com/Johnql7/writeups/assets/165813191/b78006d3-463e-4314-8be2-c0d1b5850e60)

Shell aldıktan sonra ilk önce /home/www-data dizini altından ilk bayrağı aldım. Daha sonra hazır bir içerik yönetim sisteminde bulunduğumuz için sitede ki config dosyasını aradım.

![7](https://github.com/Johnql7/writeups/assets/165813191/62b47c09-04db-44f4-8269-3df1195b9dfa)

Bu mysql şifresi root kullanıcısının şifresi olduğu için daha fazla uğraşmadan son bayrağı aldım.

![8](https://github.com/Johnql7/writeups/assets/165813191/0000d7bf-e694-43f1-aecf-876fe3e39949)
