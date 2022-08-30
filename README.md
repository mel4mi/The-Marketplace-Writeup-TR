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
