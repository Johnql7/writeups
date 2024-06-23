<p align="center"><img width="200px" src="https://tryhackme-images.s3.amazonaws.com/room-icons/8116d1d52d3a63dd1e7c2e7ddce8a0d5.png"></p>
<h1 align="center">TryHackMe - Wgel CTF</h1>

<p align="center">Merhabalar, bu yazımda sizlere tryhackme platformunda bulunan "Wgel CTF" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![3](https://github.com/Johnql7/writeups/assets/165813191/834d0330-a791-4926-8461-44440f26e9c2)

Tarama sonucunda 22 (SSH) ve 80 (HTTP) porlarının açık olduğunu öğrendik. Versiyonlarına uygun exploit olmadığını bildiğimden direkt HTTP servisine yöneliyorum.

![4](https://github.com/Johnql7/writeups/assets/165813191/b2273373-0bf2-4930-a1e3-a7d615882c8c)

Gördüğünüz üzere bizi varsayılan ubuntu sayfası karşılıyor. Kaynak kodunda "Jessie don't forget to udate the webiste" şeklinde bir açıklama satırı bulunuyor. Daha fazla bilgi edinmek için bir dosya/dizin taraması yapmaya karar verdim.

![5](https://github.com/Johnql7/writeups/assets/165813191/4e72d36a-edf4-478c-9bf1-5355af241da1)

Tarama sonucunda sadece /sitemap adında bir sayfa olduğunu öğrendim. Bu sayfada çok güzel bir site şablonu barınıyor.

![6](https://github.com/Johnql7/writeups/assets/165813191/4129a089-4689-4f9b-a2af-ff62a5480685)

Sitede 5 farklı isim geçiyor, önemli olabileceğinden isimleri kayıt ettim. Ayrıca görünürde olan sayfalarda ve formlarda herhangi bir sorun olmadığı için bir dosya/dizin taraması daha gerçekleştirdim.

![7](https://github.com/Johnql7/writeups/assets/165813191/a28fc69c-1ee6-4d3a-88f6-f9ef673f606b)

/sitemap/.ssh/ dizininde id_rsa dosyası bulunuyor. Bunu kullanarak sisteme giriş yapabiliriz ama bir kullanıcı adına ihtiyacımız var. Biraz önce bulduğum isimleri kullanarak giriş yapmayı deneyeceğim.

![8](https://github.com/Johnql7/writeups/assets/165813191/d45223aa-43c4-4f6d-8b41-7f8375780a46)

jessie adında bir kullanıcı varmış. İlk bayrağı Documents dizininde buldum. İkinci bayrağa erişebilmek için ise root olmak gerek. sudo -l komutunu kullandığımda /usr/bin/wget dosyasını root haklarıyla çalıştırabildiğimi öğrendim.

![9](https://github.com/Johnql7/writeups/assets/165813191/aa573851-f22c-468c-a82e-bc116828fddd)

GTFOBins sitesinde wget'i arattım ve çıkan sonuçtan anladığım kadarıyla -i parametresi ile istediğim dosyanın içeriğini görüntüleyebiliyorum.

![10](https://github.com/Johnql7/writeups/assets/165813191/25d1bedd-408a-4500-b71c-7afb26a84502)
