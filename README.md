# TR-TheMarketPlace-Writeup


 Merhabalar bugün sizlerle beraber Tryhackme'de bulunan "The Marketplace" adlı odayı çözümleyeceğiz. Bu yazıyı ayrıntılı yazmak istiyorum çünkü bu yazıyı anladığınız taktirde çoğu ctf'lerde zorluk çekmeyeceksinizdir.

## İhtiyacımız olanlar toolar : 
- Nmap
- Dirsearch
- BurpSuite

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

![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Add%20new%20listing%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_15_37.png)

Şikayet ediyoruz.


![Foto](https://github.com/mel4mi/The-Marketplace-Writeup-TR/blob/main/Messages%20%E2%80%94%20Mozilla%20Firefox%20(kali-linux)%2031.08.2022%2001_16_44.png)

Ve adminlerden cevap geldi. İlanımızda siteyi bozan başı şeyler olduğunu söylediler ve ilanımı kaldırdılar.

>Şimdi işin mantık kısmına gireceğiz. Bana kalırsa aslında hackerlık dediğimiz şey bir bakış açısı. Bir sistemin nasıl çalıştığını anlayıp bunu nasıl kendisi için kullanıcağını akıl edebilmesidir. Şimdi burda nasıl düşünmemiz gerektiğini anlatıcağım.

>Ben siteye zararlı bir kod yazabiliyorum ve bu kod çalışıyor. Zararlı kodumu incelemesi için adminlere atabiliyorum. O zaman zararlı kodum görüntülendiği anda adminin bilgisayarında da çalışmaz mı ??

>Şimdi size çözümü anlatmadan önce çözümün mantığını anlatmak istiyorum. Artık hemen hemen her siteye girdiğimizde Çerez Politikası diye bir pop-up sürekli sağda solda köşede çıkıyor.peki bu çerezler nedir ? 
>aslında çerez dediğimiz şey, bizim sitedeki benzersiz kimliğimiz ( bir nevi tc kimlik numaramız gibi).Sadece bize özel ve her siteye girdiğimizde site bizden bu çerezi alıyor, çözümlüyor ve bizi tanıyor. Bu yüzden siz internette bir laptop baktığınızda sürekli önünüze laptoplarla alakalı reklamlar geliyor. Peki bu çerezler sadece reklam için mi kullanılıyor ? Hayır. Mesela kullanıclara yetki vermek içinde kullanılıyor. adminin çerezi ile normal bir kullacının çerezinde fark olması lazım.

>şimdi işimize yarayacak kısımda burası. Ben adminin kullanıcı adı ve şifresini bilmiyorum ve kıramadım. O yüzden "pass to cookie" diye adlandırılan yöntemi kullanarak adminin kullanıcı adını şifresini bilmeden adminin hesabını çalacağım. çok ilginç değil mi ? 

