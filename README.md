# H-Bridge Motor Sürücü — RS365 12V DC 3A
### LM2596S-ADJ DC Step-Down Regülatörlü Çift Yönlü ve Hız Kontrollü Motor Sürme Projesi

Bu projede, bir önceki projemde kullandığım **RS365 serisi 12V DC / 3A** motor ailesini, bir **H-Bridge** yardımıyla **çift yönlü** ve **hız kontrollü** şekilde sürdüm. Güç tarafında ise +24V DC kaynaktan **LM2596S-ADJ** ile 12V seviyesine düşüş sağlayan bir step-down regülatör tasarladım.

---

## 📋 İçindekiler

- [Regülatör Tasarımı](#-regülatör-tasarımı)
- [H-Bridge Yapısı](#-h-bridge-yapısı)
- [PWM Sinyal Üretimi](#-pwm-sinyal-üretimi)
- [Karar Birimi](#-karar-birimi)
- [Gate Driver Kullanımı](#-gate-driver-kullanımı)
- [Devre Görselleri](#-devre-görselleri)

---

## ⚡ Regülatör Tasarımı

+24V DC gerilim kaynağından, **LM2596S-ADJ** entegresi ve hesaplanan **feedback dirençleri** vasıtasıyla çıkış voltajını **12V** seviyesine düşürdüm.

### İndüktör Seçimi
Motor gibi elemanlar yük altında beklenenden fazla akım çekebildiği için:

- Akım sınırı 3A olmasına rağmen, yük üzerindeki **ripple etkisini azaltmak** amacıyla kullanılan sabit değerlikli indüktörün çalışma akımını **4A** seçtim.
- Öngörülemeyen sonuçların önüne geçmek için satürasyon akımını **I<sub>SAT</sub> = 4.5A** olarak belirledim.

### Çıkış Kondansatörleri
Motorun ani akım çekiminde çıkış geriliminin çakılmaması ve Lenz kanunu gereği hat boyunca oluşacak hipotetik endüktans geriliminin yük üzerindeki gerilim stabilitesini bozmaması için:

- **Seramik kondansatörler** → Hızlı tepki gereken anlar için, çıkışa en yakın konumda
- **Düşük ESL değerlikli polarize kondansatörler** → Süreğen voltaj düşüşleri için

### Giriş Kondansatörleri
LM2596S-ADJ datasheet'inden, anahtarlama anlarındaki ortalama akımın **0–2A** arasında değiştiğini ve anahtarlama süresinin **30–50 ns** olduğunu öğrendim. Bu bilgilerle:

1. Güç kaynağından çekilecek **Coulomb miktarını** kağıt üzerinde hesapladım.
2. İzin verilen voltaj düşüşünü **%6** kabul ederek formülize ettim.
3. Girişteki ani akım ihtiyacını karşılayacak kondansatör gruplarını, çıkıştakilerle aynı mantıkla konumlandırdım.

---

## 🔀 H-Bridge Yapısı

H-Bridge'lerin mantığı, basit bir anlatımla, her yön (veya her faz bağlantı noktası) için **iki adet MOSFET** kullanıp bunların gate'lerini anahtarlayarak akım geçişine izin verme prensibine dayanır.

### High-Side: P Kanallı MOSFET'ler
Güç tüketimini azaltmak amacıyla high-side switch tarafında **P kanallı MOSFET'ler** kullandım:

- Gate bacağına **0V (pratik olarak GND)** verildiğinde MOSFET açılır ve Source → Drain yönünde akım geçişine izin verir.
- Kullanılmadıkları anlarda kapalı kalmaları için gate bacaklarını **pull-up dirençleriyle** regülatör çıkışından besledim.
- Bu sayede kısa devre yapmadan, miliamperler mertebesinde akımla gate'teki hipotetik kondansatör dolar; Drain–Source kanalı elimine edilerek akım geçişi sızıntı seviyelerine düşer.

### Low-Side: N Kanallı MOSFET'ler
Motor hız kontrolü için akım geçişini **PWM** ile sınırlandırdım:

- PWM sinyallerini **N kanallı low-side MOSFET'lerin** gate bacaklarına uygulayarak motor hızını kontrol ettim.

---

## 📶 PWM Sinyal Üretimi

Bu tarz projelerde PWM sinyalleri genellikle dijital devrelerle üretilir ve frekans yazılımla ayarlanır. Ancak ben bu işi **tamamen analog** çözmek istedim:

- **LM555** entegresini **a-stable modda** çalıştırarak bir kondansatörü V<sub>CC</sub>'nin **1/3'ü ile 2/3'ü** arasında doldurup boşalttım.
- Kondansatörün kapasitans değeri frekansa direkt etki ettiği için, datasheet'teki **periyot formülünden** periyodu, oradan da frekansı elde ettim.
- Motorun tekleme seslerinin insan kulağıyla duyulmaması için **~20 kHz** bandında frekans sağlayacak bir kapasitans seçimi yaptım.

---

## 🎮 Karar Birimi

Motorun dönüş yönüne karar veren mekanizma olarak bir **anahtar** kullandım.

Anahtar çıkışı, **AND** ve **NOT** kapılarından geçirilerek hem P kanallı hem de N kanallı MOSFET'lerin gate bacaklarını sürecek şekilde konumlandırıldı.

---

## 🚀 Gate Driver Kullanımı

Herhangi bir kondansatör boş durumdayken hipotetik olarak **kısa devre** gibi davranır. Bir MOSFET'in gate bacağı da aslında bir kondansatörden ibaret olduğuna göre, gate'i açmak nanosaniyeler içinde **~1.5A** akım çekilmesi demektir.

Bu yüksek anlık akım AND ve NOT kapılarıyla sağlanamayacağı için:

- Beslemesindeki **seramik kondansatörler** sayesinde girişindeki akımı anlık olarak şoklayıp onlarca kat yükselten **gate driver entegreleri** kullanıldı.

---

## 📷 Devre Görselleri

<img width="1283" height="672" alt="Regülatör Devresi" src="https://github.com/user-attachments/assets/7df14e58-e9e2-476c-a682-4641e7217c15" />

<img width="1372" height="721" alt="H-Bridge Devresi" src="https://github.com/user-attachments/assets/ce86112f-8a28-47d4-a4f9-64f9e91961ab" />
