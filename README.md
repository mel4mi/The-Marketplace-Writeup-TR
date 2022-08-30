# TR-TheMarketPlace-Writeup


 Merhabalar bugün sizlerle beraber tryhackme'de bulunan "The Marketplace" adlı odayı çözümleyeceğiz.

## İhtiyacımız olanlar toolar:
- Nmap
- Gobuster
- BurpSuite

### İlk Adım : Ağ Keşfi
Bir sızma testinde olsun veya herhangi bir CTF yarışmasında olsun bir sızma testinin en önemli aşaması hedef hakkında toplayabildiğimiz kadar bilgi toplamadktır.Çünkü Bir sistemin nasıl çalıştığını anlamazsak onu istismar etmemiz imkansız olacaktır.

Bu yüzden ilk olarak "nmap" ile cihaz üzerindeki portların ve servislerin keşfini yapacağız

  ```
nmap -Pn -p- -T5 <hedef ip>
```
  
  yazdığımız kodu adım adım açıklayalım:
  
  Özellik:
  >-Pn parametresi : Tarama öncesi Ping Paketi atmaz (Hedef sistemi açık olarak kabul eder ve direk taramaya başlar.)
  
  Açıklama:
  
  >Nmap, Tarama yapacağı zaman ilk başta hedef sistemin açık olup olmadığını kontrol eder. Bunu hedef sisteme default olarak ping atarak kontrol eder.
  Bu taramanın sıkıntısı şurda Windows sistemlerde ping taramaları otomatik olarak bloke edilir.Bu yüzden eğer bir windows sisteme tarama yaparsanız nmap cihazın offline olduğunu kabul eder ve tarama yapmaz.

