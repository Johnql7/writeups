<p align="center"><img width="250px" src="https://tryhackme-images.s3.amazonaws.com/room-icons/5198d5eb4284a2eeffe5eea07094d45d.png"></p>
<h1 align="center">TryHackMe - mKingdom</h1>

<p align="center">Merhabalar, bu yazımda sizlere tryhackme platformunda bulunan "mKingdom" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![3](https://github.com/Johnql7/writeups/assets/165813191/b9804085-6274-4693-a244-61be18917704)

Sadece 85 portunu açık olarak buldu. Üzerinde apache httpd 2.4.7 isimli bir http servisi çalışıyor.

![4](https://github.com/Johnql7/writeups/assets/165813191/f4be7080-325a-49d7-a862-e9dcd3dd734d)

Ana sayfada ve kaynak kodunda önemli bir bilgi bulunmuyor. Sayfada ki resmi wget yardımıyla indirip, exiftool ile meta verilerine baktığımda "b0w53r" kullanıcı adını buldum.

![5](https://github.com/Johnql7/writeups/assets/165813191/4dfca3f0-855d-49b0-8f4c-08ea183e9836)

Bu bilgi şimdilik burada dursun, belki ilerle işimize yarayabilir. Dirsearch ile bir dosya/dizin taraması gerçekleştirdikten sonra sitede /app/ diye bir dizin olduğunu öğrendim.

![7](https://github.com/Johnql7/writeups/assets/165813191/60adfdad-5d3f-43d0-992b-1fbc6501d5e1)

Ekranda ki butona tıkladığımda "Make yourself confortable and enjoy my place." şeklinde bir çıktı veriyor ve /app dizinin altında ki castle sayfasını açıyor.

![8](https://github.com/Johnql7/writeups/assets/165813191/0017a750-b33d-484d-9d55-86c83a5f3d55)

Web sayfasında sadece bir blog ve iletişim sayfası bulunuyor. Blogda sadece admin adlı bir kullanıcının yaptığı bir gönderi var. Bu gönderinin altına yorum ve ek olarak bir resim bırakabiliyorum. Resim yükleme sırasında uzantı kontrolü yapıldığı için doğrudan shell yükleyemiyorum. Web sitesinin Concrete CMS adında bir hazır içerik sistemi kullandığını öğrendim ayrıca dirsearch ile gerçekleştirdiğim bir başka taramada da bazı dizinlerin listelendiğini gördüm ama listelenen dizinlerde önemli bir dosya bulamadım. Admin panelini bulduğumda ise varsayılan kullanıcı bilgilerini girdiğimde admin paneline erişebildim. Sistemde bulunan tek kullanıcının yani adminin profil fotoğrafını shell ile değiştirmek istediğimde aşağıda ki hata ile karşılaştım.

![9](https://github.com/Johnql7/writeups/assets/165813191/b4661207-95a1-4c7a-a963-82d6504b9826)

Bu hata dışında genel olarak dosya yükleme yerlerine her resim yüklemeye çalıştığımda uzantını php, phar ve türevleri olduğundan kabul etmiyordu. Admin panelini biraz gezdikten sonra aşağıda göreceğiniz üzere kabul edilebilir dosya uzantılarının bulunduğu bir form ile karşılaştım ve içerisine php uzantısını ekledim.

![10](https://github.com/Johnql7/writeups/assets/165813191/b3aa3f4b-4aab-4822-ad1c-6e46e3fdb726)

files kategorisine gittikten sonra "upload file" butonuna tıkladım ve oraya php shelli yükledim.

![11](https://github.com/Johnql7/writeups/assets/165813191/4e2c3ebc-7c53-44f0-9cc5-3b9b6ca8dcf2)

Linke gittiğimde dinlediğim port üzerinden bir shell elde edebildim. Sistemde toad ve mario olmak üzere iki kullanıcı bulunuyor.

![12](https://github.com/Johnql7/writeups/assets/165813191/01a7e618-7220-4c9f-826b-564c739336a5)

Web sitesi hazır içerik yönetim sistemi kullandığı için /var/www/html dizine gidip config dosyasının içinde ki kullanıcı adını ve şifresini aldım.

![13](https://github.com/Johnql7/writeups/assets/165813191/722096ae-47cc-46a5-a660-816cc9369fb6)

Burada ki şifreyi öncelikle toad kullanıcısına geçiş yapmak için kullandım ve /home/toad dizinine yöneldim. Dizinde önemli bir dosya yokmuş. Ayrıca sudo -l komutunu kullanamıyorum. Elimde ki kullanıcı bilgilerini kullanarak veri tabanını yokladım ama admin dışında bir kullanıcı veya önemli bir veri bulunmuyor. env komutunu kullanarak değişkenleri görüntülediğimde içlerinden biri dikkatimi çekti. base64 kodlamasına benzediği için decode ettim ve elde ettiğim verinin mario kullanıcısının şifresi olduğunu öğrendim.

![14](https://github.com/Johnql7/writeups/assets/165813191/b6d91be2-1739-45f5-b3c1-e7bafae37e77)

Anlaşılan user.txt dosyasını okuyabilmemiz için yetki yükseltmemiz gerekiyor. sudo -l komutunu kullandığımda /usr/bin/id dosyası üzerinden istediğim kişiye bürünebileceğimi öğrendim. İnternette yaptığım aramalardan ve dosyanın yardım sayfasından bir şey çıkaramadım. 

Daha fazla bilgi edinebilmek için sisteme linpeas.sh dosyasını indirdim ve çalıştırdım. Sonucunda mario grubunda ki kullanıcıların /etc/hosts dosyasını düzenleyebileceğini gördüm. bende mario kullanıcısında ve grubunda olduğumdan bu dosyayı düzenleyebileceğim.

![15](https://github.com/Johnql7/writeups/assets/165813191/159bdf96-da7a-4484-8e7d-c610813dc059)

Bu dosyayı düzenleyebileceğimizi ve içerisinde mkingdom.thm domaininin olduğunu düşünürsek, mantıken bir dosyanın arkaplanda çalışarak bu domaine request gönderdiğini düşünmek çokta saçma olmaz. Bu dosyayı bulabilmek için pspy64 aracını kullanacağım.

![16](https://github.com/Johnql7/writeups/assets/165813191/badc83a9-5a64-40d5-9db6-498dd3cb09c8)

Gördüğünüz üzere bu domain üzerinden counter.sh adında bir dosyayı indirip çalıştırıyor. Öncelikle ip adresimizi /etc/hosts dizininde ki domainin altına ekleyelim daha sonra ise app/castle/application şeklinde iç içe dizinler oluşturup içerisine shell kodumuzun bulunduğu counter.sh dosyasını ekleyelim. Daha sonra ise 85 numaralı http servisini ve shell alacağımız portu dinleyelim.

![17](https://github.com/Johnql7/writeups/assets/165813191/0f0b6498-bf8d-4a36-b685-c106f53ae2fc)

cat komutunu kullanamadığımızdan ötürü less veya tail gibi komutlardan yararlanabilirsiniz.
