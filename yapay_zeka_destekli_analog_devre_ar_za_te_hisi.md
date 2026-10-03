# 🔍 Yapay Zeka Destekli Analog Devre Arıza Teşhisi (Analog Circuit Fault Diagnosis)

Bu proje, analog elektronik devrelerde fiziksel yaşlanma veya tolerans kaybı sonucu oluşan "Yumuşak Hataları" (Soft Faults) makine öğrenmesi algoritmaları kullanarak tespit etmeyi amaçlamaktadır. 

Standart test yöntemleriyle bulunması zor olan komponent bozulmaları, devrenin frekans/zaman tepkisindeki milimetrik sapmalar analiz edilerek yapay zeka tarafından saniyeler içinde sınıflandırılır.

## 🎯 Projenin Amacı
*   Elektronik kartlarda arıza arama süresini minimuma indirmek.
*   Simülasyon ortamında üretilen sentetik verilerle (Monte Carlo) gerçek dünya fiziksel arızalarını modellemek.
*   Elektrik-Elektronik Mühendisliği (Devre Analizi) ile Makine Öğrenmesini (Sınıflandırma) tek bir projede birleştirmek.

## 📊 Kusursuz Veri Seti Oluşturma Adımları (Data Generation Pipeline)
Makine öğrenmesi modelinin başarısı, verinin kalitesine bağlıdır. Bu projede veri seti tamamen "bilimsel temellere" dayanarak şu adımlarla oluşturulacaktır:

1.  **Referans Devre Tasarımı (Sallen-Key Filtre):** Proteus üzerinde temel bir aktif filtre devresi kurulur.
2.  **Sağlıklı Durum (Nominal State) Verisi:** Tüm komponentlere %5 doğal üretim toleransı tanımlanır. Monte Carlo simülasyonu ile devrenin sağlıklı haldeki AC/Transient analiz çıktıları (frekans ve voltaj yanıtları) 100+ farklı iterasyonla alınır ve CSV olarak kaydedilir (Etiket: `0 - Saglikli`).
3.  **Parametrik Arıza Enjeksiyonu (Soft Fault Injection):** Komponentler tek tek, fiziksel yaşlanma karakteristiklerine göre bozulur:
    *   *R1 Arızası:* R1 direnç değeri %30 artırılır. Diğerleri nominal toleransta bırakılır. 100+ simülasyon alınır (Etiket: `1 - R1_Arizali`).
    *   *C1 Arızası:* C1 kondansatör değeri %40 düşürülür (kuruma simülasyonu). 100+ simülasyon alınır (Etiket: `2 - C1_Arizali`).
4.  **Veri Birleştirme (Data Fusion):** Proteus'tan alınan tüm CSV dosyaları Python (Pandas) ortamında tek bir matris haline getirilir.

## 🧠 Makine Öğrenmesi Aşaması
*   **Ön İşleme (Preprocessing):** Verilerdeki gürültüler temizlenir, özellik ölçekleme (Feature Scaling) yapılır.
*   **Model Eğitimi:** `Scikit-learn` kütüphanesi kullanılarak Random Forest (Rastgele Orman) veya SVM (Destek Vektör Makineleri) algoritmaları eğitilir.
*   **Test & Doğrulama:** Modelin daha önce görmediği %20'lik test verisi üzerindeki teşhis doğruluğu (Accuracy) ve Karmaşıklık Matrisi (Confusion Matrix) raporlanır.

## 🛠️ Kullanılan Teknolojiler
*   **Donanım / Simülasyon:** Proteus Design Suite (Monte Carlo Simulation)
*   **Veri Bilimi / ML:** Python, Pandas, NumPy, Scikit-learn
*   **Geliştirme Ortamı:** Google Colab / Jupyter Notebook