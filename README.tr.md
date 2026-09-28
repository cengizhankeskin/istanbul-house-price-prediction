# İstanbul Konut Fiyat Tahmini

**İstanbul'daki satılık daire ilanlarından XGBoost ile fiyat tahmini.**

[English](README.md) · [Notebook](HouseProject.ipynb) · [Veri kaynağı](DATA_SOURCE.md)

## Amaç

Konum, metrekare ve bina özelliklerinden ilan fiyatını tahmin eden bir makine öğrenmesi çalışmasıdır. **24.767 ilan ve 30 sütun** içeren veri incelenir; filtreleme sonrasında **22.995 ilan ve 19 girdi değişkeni** kullanılır.

## Sonuçlar

| Model | Test R² | MAE (TL) | RMSE (TL) |
| --- | ---: | ---: | ---: |
| Eğitim fiyatı ortalaması | −0,00003 | 4.345.701 | 5.923.879 |
| **XGBoost** | **0,8222** | **1.619.965** | **2.498.006** |

5.749 ilandan oluşan aynı test kümesinde XGBoost'un ortalama mutlak hatası, basit ortalama tahminine göre **%62,7 daha düşüktür**. R², yüzde doğruluk değildir. Bu sonuçlar filtrelenmiş veri için geçerlidir.

![Model sonuçları](reports/evaluation.png)

## Uygulanan adımlar

1. Veri yapısı, eksik değerler ve dağılımlar incelendi.
2. Hedef fiyattan türetilen `price_per_sqm`, ilan kimliği ve bazı seyrek alanlar çıkarıldı.
3. Eksik aidatlar sıfır kabul edildi; fiyatlar 3×IQR kuralıyla, aidatlar 5.000 TL altı olacak şekilde filtrelendi.
4. Veri %75 eğitim / %25 test olarak ayrıldı (`random_state=42`).
5. Sayısal değişkenlerde medyan; kategorik değişkenlerde en sık değer ve `TargetEncoder` kullanıldı.
6. `ColumnTransformer` ve `Pipeline` içinde XGBoost eğitildi, test metrikleri hesaplandı ve model kaydedildi.

Notebook'un özgün modelleme yaklaşımı korunarak bölüm açıklamaları, basit model karşılaştırması ve değerlendirme grafiği eklendi. Kod hücreleri sırayla yeniden çalıştırıldı ve ilk kaydedilmiş sonuç doğrulandı.

## Çalıştırma

Python 3.13.9 ile doğrulandı. Proje klasöründe, tercihen ayrı bir sanal ortam içinde:

```bash
python -m pip install -r requirements.txt
python -m jupyterlab HouseProject.ipynb
```

CSV dosyasını notebook ile aynı klasörde tutun. Notebook'ta doğru Python ortamını seçerek çekirdeği yeniden başlatın ve tüm hücreleri çalıştırın. `houseprice.pkl` ile `reports/` altındaki çıktılar yeniden üretilir. Ayrıntılı ortam kurulum adımları [İngilizce README](README.md#run-locally) içindedir.

## Sonuçların sınırları

- Fiyat filtresinin eşikleri eğitim/test ayrımından önce hesaplanıyor. Bu nedenle test fiyatlarının dağılımı örnek seçimini etkiliyor. Sonraki sürümde önce ayrım yapılmalı, veriden öğrenilen eşikler yalnızca eğitim verisinden hesaplanmalıdır.
- Rastgele ayrım gelecekteki ilanlardaki başarıyı kanıtlamaz; aynı veya benzer konuta ait ilanların ayrımın iki tarafına düşmesi ayrıca denetlenmelidir.
- Veriler gerçekleşmiş satış fiyatlarını değil, Mart 2026 ilan fiyatlarını içerir. Filtreler pahalı konutların bir kısmını dışarıda bırakır.
- Kodlayıcıdaki beş kat, hedef kodlamanın iç katlarıdır; model için beş katlı performans değerlendirmesi yapılmış değildir.

## Kaynak

**Enes Ulusoy — [Istanbul Apartment Prices 2026](https://www.kaggle.com/datasets/brahimenesulusoy/istanbul-apartment-prices-2026)**. Veri seti kartında CC BY-SA 4.0 lisansı belirtilir. Kaynak ve lisans ayrıntıları [DATA_SOURCE.md](DATA_SOURCE.md) içindedir.

**Modelleme çalışması:** [Cengizhan Keskin](https://github.com/cengizhankeskin)
