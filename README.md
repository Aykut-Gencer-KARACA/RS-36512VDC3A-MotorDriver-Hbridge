#H-bridge RS365 12V DC 3A Motor Driver With lm2596s-ADJ DC Stepdown Regulator Project
##The Way of Tought
###Regulator Part

  Bu projemde bir önceki projemdeki RS 365 serisi 12V DC gerilimle çalışan ve kaynaktan 3A akım çeken motor ailesinin H bridge yardımıyla çift yönlü ve hız kontrollü bir şekilde sürülmesini sağladım. 
Bunu yaparken +24V DC gerilim kaynağından LM2596S-ADJ entegresi yardımıyla hesaplanan feedback dirençleri vasıtasıyla çıkış voltajını 12 Volt düzeylerine düşürdüm. Motor gibi elemanlar kimi zamanlarda yük altında beklenenden
daha fazla akım çekebilecekleri için akım sınırı 3A olmasına rağmen yük üzerindeki akımın ripple etkisini azaltması amacıyla konan sabit değerlikli indüktörün çalışma akım düzeyini 4A olarak seçtim. Öngörülemeyen sonuçlara neden 
olmaması için satürasyon akım değerini (ISAT) 4.5 A olarak belirledim. Motorun ani akım çekiminde çıkıştaki gerilimin çakılmaması için ve aynı zamanda çekilebilecek olan ani akımın lenz kanunu gereğince hat boyunca oluşturacağı hipotetik
ekdüktansın geriliminin yük üzerindeki gerilimin stabilitesini bozmaması amacıyla hızlı tepki gereken anlarda çıkış tarafına en yakın olacak şekilde seramik kondansatörler ve süreğen voltaj düşüşlerinde düşük ESL değerlikli polarize kondansatörler
kullandım. Regülatörün giriş kısmında ise anahtarlama anlarında görülen ortalama akım miktarının 0-2A (Anahtarlama bazlı) arasında değişebileceğini LM2596S-ADJ'nin datasheetinden bilgilenip sonrasında anahtarlama zamaının 30-50 nS olduğu bilgisini 
kullanarak güç kaynağından çekilecek olan Coulomb miktarını kağıt üzerinden hesaplayıp izin verilen voltaj düşüşünü %6 olarak kabul edip formülüze edip giriş kısmındaki ani akım ihtiyacına yönelik ihtiyacı karşılayabilecek kondansatör gruplarını aynı
çıkıştakiler gibi konumlandırdım. 

##H-bridge Localication
  
    H birdgelerin mantığı basit bir anlatımla her yön için (veya her faz bağlantı noktası için) iki adet MOSFET kullanarak bunların gatelerini anahtarlayarak akım geçişine izin vermesi prensibiyle çalışır. Güç tüketimini azaltmak amacıyla
High side switch kısmındaki mosfetleri p kanallı mosfetler olarak seçtim. Bu mosfetlerin açık kalabilmeleri için gate bacaklarına 0 V (Pratik olarak gnd) verilmesi taktirinde mosfetler açılıp source'dan drain kısmına akım geçişine izin vereceklerdir.
Kullanılmadıkları anlarda kapalı kalabilmeleri adına onları birer pull-up dirençleriyle Regülatörümün çıkışından besledim. Bu durum kısadevre yapmadan miliamperler mertebesinde akım çekerek gate bacaklarındaki hipotetik kondansatörlerin dolmasına ve bu nedenle
Drain'den Source güzergahındaki kanalı elimine edip akım geçişini sızdırma düzeylerine düşürmektedir. Motorun çalışma prensibine göre akım geçişini PWM kontrolüyle sınırlandırıp bunu yapacak olan mosfetleri n kanallı low side switcher olarak kullanmayı planladım.
PWM sinyallerini n kanallı mosfetin gate bacaklarına uygulayıp motor hızını kontrol ettim. 

##The Creation of PWM Signals 

    Bu tarz projelerde genellikle PWM sinyalleri dijital devrelerle oluşturulur. Yazılan koda göre frekans ayarlanabilir. Ancak ben bu işi analog olarak çözmek istediğimden LM555 entegresini a-Stable modda çalıştırarak kendisinden bir kondansatörü Vcc geriliminin
1/3'ü ile 2/3'ü arasında doldurup boşaltmasını sağladım. Bu noktada kondansatörün kapasitans değeri frekansa direkt etki ettiği için datasheet içindeki periyot formülünden periyotu bulup oradanda frekansı elde ettim. tercihen motorun tekleme seslerinin insan kulağıyla
duyulmamasını istediğimden yaklaşık 20k Hz bandında bir frenkans sağlayacak kapasitans seçiminde bulundum. 


##Decision Unit

  Motorun dönüş yönüne karan veren mekanizma olarak bir anahtar kullandım. Anahtar AND ve NOT kapılarını çeşitli işlemlerden geçirerek hem p kanallı hemde n kanallı mosfetlerin gate bacaklarını sürecek şekilde konumlandırıldı.

##Gate Drivers

   Herhangi bir kapasitör boş durumdayken hipotetik olarak kısadevre olarak tanımlanır. Bu durumda bir mosfetin gate bacağı aslında bir kapasitörden ibaretse gate bacağını açmak nanosaniyler içerisinde yaklaşık 1.5 amper çekilmesi demektir. 
Bu yüksek değer AND ve NOT kapılarıyla sağlanamayacağı için ani akım ihtiyacında girişindeki akımı beslemesindeki seramik kapasitansler sayesinde anlık olarak şoklayıp onlarca kat yükselten gate driver entegreleri kullanılmıştır. 


<img width="1283" height="672" alt="Screenshot 2026-07-17 203948" src="https://github.com/user-attachments/assets/7df14e58-e9e2-476c-a682-4641e7217c15" />


<img width="1372" height="721" alt="Screenshot 2026-07-17 204121" src="https://github.com/user-attachments/assets/ce86112f-8a28-47d4-a4f9-64f9e91961ab" />


