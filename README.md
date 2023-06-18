# DEUROV Mimari Taslak
###### Güncelleme: 2023-06-17

## Sistem Özeti
Mimari tasarıma başlamak için tüm girdi ve çıktı alınan aygıtları ve datasheetleri buraya eklemeliyiz

#### Sistem girdileri
- Sistem zamanı
- Derinlik
- Kamera `1080p30`, yatay `63.7°`, dikey `70°` [^1]
- İvme, jiroskop, pusula `(x,y,z)` [^2]
- Sıcaklık
- ~~Sonar~~ (Arızalı)
- ~~Alt Kamera~~ (Eklenebilir)
- ~~Mesafe Sensörü~~ (Eklenebilir)

#### Sistem Çıktıları
- Motorlar `4 adet xy`, `2 adet z`
- Farlar
- ~~İşaret LED'leri~~ (Eklenebilir)

#### Notlar
- Test esnasında ledler aracılığıyla hızlıca anlık durumu izleyebilmek testleri hızlandırabilir
- Hareket ederken gerçek dünyada ne kadar hareket ettiğimizi nasıl takip edebiliriz? `lidar, sonar, görüntü işleme, uzaklık sensörleri vs.`
- Sensör testleri yazılmalı. Ana koddan izole çalışabilir, erken başlanması vakit kazandırır.




## Motorlar ve Hareket
#### Kısıtlar
- Motorlara gönderilen sinyal sadece belli bir alt eşiğin üstünde motorları başlatabiliyor
- Motorların uzun ömürlü olması için sinyal belirli bir üst eşiği geçmemeli
- Son hızda havuzu baştan sonra gitmek yaklaşık 30s sürüyor

#### Ön hazırlık
- IMU calibration yürütülüyor
- IMU'dan gelen degerler programa yükleniyor

Bu kısım otomatikleştirilebilir

### Hedefler
Motorlara giden 6 kanaldaki parametreleri soyutlaştırmalıyız.

- Bunun için nasıl bir model olmalı?
- Hareket ettiğimizde konum farkını takip edebiliyor muyuz?


- Konum ve yön birlikte (Konum farkını takip edebiliyorsak)
	- Hem konum hem yön değiştirme `motor_move_turn(vec3 distance, vec3 direction)`
	-
    - Çalışacağı süre belirsiz
	- Yazması daha zor
	- Arama için hızlı
	
- Konum ve yön ayrı ayrı
	- Sabit konumda yön değiştirme `motor_move(vec3 direction, int ms)`
	- Sabit yönde konum değiştirme `motor_turn(vec3 power, int ms)`
	-
    - Gideceği mesafe belirsiz
	- Yazması ve hata ayıklaması kolay
	- Arama için yavaş
	- Bu gibi bir modelde her adımı küçük parçalara bölüp istenilen dönüşe ulaşana kadar sık sık çağırabiliriz.



## Misyon Algoritması
##### Mevcut model
```c
void gorev() {
	while (hedefi bulana kadar) {
		hedef_ara();
	}
	while (hedefe yerleşene kadar) {
		hedef_yaklas();
	}
	while (hedefi tamamlanana kadar) {
		hedef_tamamla();
	}
}
```
##### Hedefler
- Kör noktaları olmamalı
- Minimum sürede çalışmalı
- Hareket ederken hata paylarını tolere edebilmeli
- Hedefe dik açıda kalındığında kamera hedefi görebiliyor mu?

##### Öneriler
- Hedef aramada havuzun kenarından başlayıp yan yan ilerlenebilir 
- Hedef aramada havuzun merkezinde dönerek etrafa bakılabilir

[^1]: [RPi Camera module datasheet](https://www.arducam.com/downloads/modules/RaspberryPi_camera/RaspberryPi_Camera_Module_DS_V3.0.pdf)

[^2]: [MPU6050 Datasheet](http://www.i2cdevlib.com/docs/html/class_m_p_u6050.html)