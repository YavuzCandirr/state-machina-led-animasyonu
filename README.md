# STM32 Timer Kesmesi ile Kayan Işık (Chaser LED) Animasyonu

Bu proje, STM32 mikrodenetleyicilerinde donanımsal zamanlayıcı (Timer) kesmeleri ve **Durum Makinesi (State Machine)** mantığı kullanılarak geliştirilmiş, tamamen engellemesiz (non-blocking) bir "Kayan Işık" (Running LED / Chaser) animasyonudur.

Geleneksel `HAL_Delay()` fonksiyonları kullanılarak yazılan bloklayıcı kodların aksine, bu projede işlemci ana döngüde (`while(1)`) tamamen boşta bekler. Animasyonun tüm kareleri (frames) arka planda çalışan TIM2 zamanlayıcısının ürettiği periyodik kesmelerle yönetilir. 

## 🌟 Öne Çıkan Mühendislik Yaklaşımları

1. **Non-Blocking Mimari:** Sistemde hiçbir gecikme (delay) fonksiyonu kullanılmamıştır. İşlemci gücü animasyon için harcanmaz, sisteme başka görevler eklenmeye tamamen hazırdır.
2. **State Machine (Durum Makinesi):** Animasyonun her bir adımı `switch-case` yapısıyla modellenmiştir. Sistem, her kesmede `animasyon` değişkeni üzerinden hangi karede olduğunu bilir ve bir sonraki adımı planlar.
3. **Güvenli Değişken Yönetimi:** Kesme (Interrupt) fonksiyonu içinde değeri değiştirilen `animasyon` değişkeni, derleyici optimizasyonlarından korunmak için `volatile` anahtar kelimesi ile tanımlanmıştır.
4. **Temiz Sahne (Reset) Mantığı:** Işıkların birbirine karışmasını (ghosting) önlemek için, her animasyon karesinden önce tüm LED'ler tek bir komutla sıfırlanır (RESET), ardından sadece ilgili sıradaki LED yakılır (SET).

## ⚙️ Nasıl Çalışır?

Sistemin "kalp atışını" TIM2 zamanlayıcısı belirler. Süre her dolduğunda `HAL_TIM_PeriodElapsedCallback` fonksiyonu tetiklenir:
1. **Temizleme:** Port E üzerindeki 8 LED'in tamamı söndürülür.
2. **Kare Çizimi:** O anki `animasyon` durumuna (1'den 8'e kadar) göre yalnızca sıradaki LED yakılır.
3. **İlerleme:** `animasyon` değişkeni bir artırılır. Eğer 8. son LED yanmışsa, değişken 1'e eşitlenerek sistem başa sarılır ve sonsuz bir döngü elde edilir.

## 📌 Pin Konfigürasyonu (GPIO Ayarları)

* **LED Çıkışları (GPIO_Output):** `PE8` ile `PE15` arası (Kodda `ld1_Pin` - `ld8_Pin` olarak adlandırılmıştır).
* **Zamanlayıcı:** `TIM2` (Kalp atışı periyodu için Prescaler ve Period ayarları yapılmıştır).
* *Not: `push_button_Pin` giriş olarak yapılandırılmış olsa da bu temel animasyonda pasif durumdadır.*


### 👨‍💻 Geliştirici / Developer
<p align="center">
  <b>Yavuz ÇANDIR</b>
  <br>
  <a href="https://github.com/YavuzCandirr" target="_blank">
    <img src="https://img.shields.io/badge/GitHub-YavuzCandirr-black?logo=github" alt="GitHub">
  </a>
</p>
