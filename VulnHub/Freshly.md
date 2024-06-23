<h1 align="center">VulnHub - Freshly</h1>

<p align="center">Merhabalar, bu yazımda sizlere vulnhub platformunda bulunan "Freshly" isimli makinenin çözümünü göstereceğim. Bu makine linux tabanlı kolay seviye bir makinedir. Makinede çalışan servisleri öğrenmek adına nmap ile bir port taraması yaparak başlıyorum.</p>

![1](https://github.com/Johnql7/writeups/assets/165813191/bf621990-4363-409a-9cc1-8da39eeef953)

Tarama sonucunda 3 adet http servisinin açık olduğunu görüyoruz. Teker teker hepsini inceleyelim. 80 portunda sadece bir gif bulunuyor.

![2](https://github.com/Johnql7/writeups/assets/165813191/e55e6eeb-6e98-4b05-ae03-e2cc573c8cb6)

443 numaralı portta ise /wordpress dizinine yönelten bir link var.

![3](https://github.com/Johnql7/writeups/assets/165813191/f6db6e98-92e7-4d19-9659-82277dbc2cf9)

Aynı wordpress site 8080 numaralı http servisinde de var. 443 ve 8080 numaralı http servisleri arasında bir fark yok gibi. 80 numaralı serviste ise dizin taraması sonucunda phpmyadmin ve login.php sayfalarını keşfettim.

![5](https://github.com/Johnql7/writeups/assets/165813191/5c798d5d-f491-47bf-9b59-a49d868ce814)

Giriş sayfasında girdiğim bilgilerin doğruluğuna göre 0 ve 1 şeklinde dönüş yapan bir kod var. sql injection zafiyeti kendini belli etti.

![6](https://github.com/Johnql7/writeups/assets/165813191/de4613b4-52f0-4192-9c27-361797f2b208)

Sqlmap aracını kullanarak aşağıdaki kullanıcı adı ve şifrelerini çıkardım.

candyshop:password
Sir:PopRocks
admin:SuperSecretPassword

Wordpress sitelere sadece admin kullanıcısı olarak giriş yapabiliyoruz. Daha önce yaptığım bir araç ile site üzerinden shell alacağım.

![7](https://github.com/Johnql7/writeups/assets/165813191/e79f1057-a02e-4370-a9e5-7dcc32c93327)

Herhalde root yetkisine erişmemiz bizim için yeterli olacaktır. /etc/passwd dosyasını okuduğumda aşağıda ki yorum satırını gördüm. 

![8](https://github.com/Johnql7/writeups/assets/165813191/0d7c6f19-631f-46a7-859c-ce69622d2fd4)

Ne anlama geldiğini anlamadım. root yetkisine de erişmek çok zor olmadı. admin kullanıcısının şifresini root kullanıcısı da kullanıyormuş.

![9](https://github.com/Johnql7/writeups/assets/165813191/dd40cac1-ae99-448e-86b9-9ee738521013)
