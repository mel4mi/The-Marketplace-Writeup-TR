# TR-TheMarketPlace-Writeup


 Merhabalar bugün sizlerle beraber Tryhackme'de bulunan "The Marketplace" adlı odayı çözümleyeceğiz.

## İhtiyacımız olanlar toolar : 
- Nmap
- Gobuster
- BurpSuite

### İlk Adım : Ağ Keşfi
Bir sızma testinde veya herhangi bir CTF yarışmasında olsun bir sızma testinin en önemli aşaması hedef hakkında toplayabildiğimiz kadar bilgi toplamaktır.Çünkü Bir sistemin nasıl çalıştığını anlamazsak o sistemi istismar etmemiz imkansız olacaktır.

Bu yüzden ilk olarak "Nmap" ile cihaz üzerindeki portların ve servislerin keşfini yapacağız : 

  ```
nmap -Pn -p- -T5 <hedef ip>
```
  
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



