# Müşteri Kaybı Tahmin Projesi

Bu proje, Telekom endüstrisinde müşterilerin şirketten ayrılıp ayrılmayacağını tahmin etmek amacıyla makine öğrenimi sınıflandırma algoritmalarını kullanmaktadır. Amacımız, müşteri kaybını etkileyen önemli faktörleri belirleyerek, müşteri kaybını azaltmak ve müşteri sadakatini artırmak için stratejiler geliştirmektir.

## Veri Seti

Veri setimiz aşağıdaki bilgileri içermektedir:

### Sınıflandırma Etiketi
- **Churn**: Müşterinin ayrılıp ayrılmadığı (Yes veya No)

### Her Müşterinin Kaydolması Gereken Hizmetler
- **PhoneService**: Müşterinin telefon hizmetinin olup olmadığı (Yes veya No)
- **MultipleLines**: Müşterinin birden fazla hattının olup olmadığı (Yes, No, No phone service)
- **InternetService**: Müşterinin sahip olduğu internet hizmet türü (DSL, Fiber Optic, No)
- **OnlineSecurity**: Müşterinin çevrimiçi güvenliği olup olmadığı (Yes, No, No Internet Service)
- **OnlineBackup**: Müşterinin çevrimiçi yedeği olup olmadığı (Yes, No, No Internet Service)
- **DeviceProtection**: Müşterinin cihaz korumasına sahip olup olmadığı (Yes, No, No Internet Service)
- **TechSupport**: Müşterinin teknik desteğe sahip olup olmadığı (Yes, No, No Internet Service)
- **StreamingTV**: Müşterinin yayın yapan TV'si olup olmadığı (Yes, No, No Internet Service)
- **StreamingMovies**: Müşterinin film akışı olup olmadığı (Yes, No, No Internet Service)

### Müşteri Hesap Bilgileri
- **Tenure**: Müşterinin şirkette kaldığı süre
- **Contract**: Müşterinin sözleşme türü (Month-to-Month, One year, Two years)
- **PaperlessBilling**: Müşterinin kağıtsız faturalandırması olup olmadığı (Yes, No)
- **PaymentMethod**: Müşteri tarafından kullanılan ödeme yöntemi (Electronic check, Mailed check, Bank transfer (automatic), Credit card (automatic))
- **MonthlyCharges**: Müşteriden aylık olarak tahsil edilen tutar
- **TotalCharges**: Müşteriden tahsil edilen toplam tutar

### Müşteri Demografik Bilgileri
- **customerID**: Her müşteri için benzersiz değer
- **Gender**: Müşterinin cinsiyet türü (Female, Male)
- **SeniorCitizen**: Müşterinin yaşlı olup olmadığı (Yes, No)
- **Partner**: Müşterinin ortağı olup olmadığı (Yes, No)
- **Dependents**: Müşterinin bağımlısı olup olmadığı (Yes, No)

## Modelleme

Başlangıçta, makine öğrenmesi sınıflandırma modellerini herhangi bir özellik mühendisliği uygulaması yapmadan, sadece eksik verileri silerek kurduk. Aşağıdaki modeller kullanıldı:

- KNN
- Logistic Regression
- Random Forest
- CART
- GBM
- XGBoost
- Adaboost
- CatBoost
- Naive Bayes
- LDA
- QDA
- Extra Trees
- LightGBM
- Hist Gradient Boosting
- Nearest Centroid
- Neural Network (MLP)
- Stochastic Gradient Descent

### En İyi 3 Modelin İncelenmesi
- **Adaboost**: Sınıf 0'ı doğru şekilde sınıflandırmada en iyi performansı göstermiştir. Sınıf 1'de iyileştirmeye ihtiyaç duyulmaktadır. Genel olarak dengeli bir modeldir.
- **Naive Bayes**: Sınıf 1 üzerinde yüksek recall sağlamaktadır, ancak precision oranı düşüktür. Genel doğruluğu Adaboost'tan düşüktür.
- **QDA**: Sınıf 0 için yüksek precision sağlarken, genel doğruluk ve F1-skoru daha düşüktür. Model seçiminde, hangi sınıfın daha önemli olduğuna karar vermek faydalı olabilir. Sınıf 0'ın doğru sınıflandırılması ön plandaysa, Adaboost en iyi seçenek olabilir. Sınıf 1'in yanlış sınıflandırılması maliyetli ise, Naive Bayes'in yüksek recall oranı dikkate alınabilir.

## Özellik Mühendisliği

Özellik mühendisliği uygulamaları şunları içerir:

A. **Müşteri Süreleri**: Müşterilerin kaldığı toplam ayları dört gruba ayırdık:
- Yeni Müşteri (0-12 ay)
- Kısa Süreli Müşteri (13-37 ay)
- Orta Süreli Müşteri (37-61 ay)
- Uzun Süreli Müşteri (61-73 ay)

B. **Aldıkları Hizmet Sayısı**: 'PhoneService', 'InternetService', 'OnlineSecurity', 'OnlineBackup', 'DeviceProtection', 'TechSupport', 'StreamingTV', 'StreamingMovies' sütunlarındaki “Yes” ve “No” değerlerine bakarak yeni bir sütun oluşturduk.

C. **Aldıkları Hizmet Başına Aylık Ortalama Ücret**: Aldıkları Hizmet Sayısı değerinin 0 olup olmamasına bakarak, MonthlyCharges değerinin Aldıkları Hizmet Sayısı değerine bölümü ile yeni bir sütun oluşturduk.

D. **Sözleşmeli Mi?**: Contract sütununda değeri Month-to-month olanlar için 0, "One year", "Two year" değerleri için 1 olan ve müşterinin sözleşmeli olup olmadığını kontrol eden yeni bir sütun oluşturduk.

E. **Otomatik Ödeme mi?**: Müşterinin otomatik ödeme yapıp yapmadığını kontrol etmek için yeni bir sütun oluşturduk.

F. **No Internet Service**: InternetService sütununda değeri No olanlar için diğer sütunlardaki değerleri de etkileyen "No Internet Service" değerini "No" olarak değiştirdik.

G. **No Phone Service**: PhoneService sütununda değeri No olanlar için MultipleLines sütunundaki "No phone service" değerini "No" olarak değiştirdik.

H. **Streaming**: StreamingMovies veya StreamingTV hizmetlerine abone olup olmadığını daha basit ve anlaşılır bir biçimde temsil etmek için yeni bir sütun oluşturduk.

## Analizler

Analizler sonucunda şu bulgulara ulaşıldı:

- **Sözleşme Türleri ve Müşteri Dağılımı**: Contract değeri "Month-to-month" olan "Yeni Müşteri" ve "Kısa Süreli Müşteri" grubunun sayısı fazla olmasına rağmen, bu müşteri grubunun "One year" ve "Two year" sözleşmeli müşterilerindeki sayının belirgin bir düşüş yaşandığı gözlemlenmiştir. Bu, uzun vadeli sözleşmelere geçişin zorluklarını veya kısa süreli sözleşmelere olan yüksek talebi gösterebilir.

- **MonthlyCharges ve Tenure**: Tenure değeri 0-10 arasında bulunan müşterilerde, MonthlyCharges değeri ortalama 70 birimi geçtikten sonra müşteri kaybının sık yaşandığı gözlemlenmiştir. Bu, yüksek aylık ücretlerin müşteri kaybını artırabileceğini işaret etmektedir.

## Sonuçlar

- **Model Performansı**: Özellik mühendisliği sonrası modellerin performansında genel olarak iyileşme gözlemlendi. Özellikle, Adaboost ve Naive Bayes modellerinin performansı arttı. Adaboost sınıf 0'ı doğru sınıflandırmada iyi performans sergilemeye devam etti, Naive Bayes ise sınıf 1 üzerinde yüksek recall sağladı. QDA modeli, yüksek precision değerleriyle dikkat çekti ancak genel doğruluk ve F1-skorunda diğer modellerin gerisinde kaldı.
- 
## İletişim

Projeye öneri veya katkıda bulunmak isterseniz, [Linkedin](https://www.linkedin.com/in/ibrahimsezginim) profilimi ziyaret edebilir veya [e-posta](mailto:benibrahimsezgin@outlook.com) ile bana ulaşabilirsiniz.
