**Siamese Network ve İmzalar Arası Benzerlik Bulma Çalışması**

Bu çalışma Siamese Network kullanarak imzalar arası benzerlik bulmayı içermektedir.
Çalışmanın network eğitim kısmı GPU desteği nedeniyle Google Colab'da gerçekleştirilmiş olup, elde edilen çıktılar ve notebook dosyası *siamese-network-on-signatures* dosyasına yüklenmiştir.

**Aşamalar**

**1- Veri Önişleme :** Çalışmanın bu aşaması *datapreprocessing.py* dosyasında gerçekleştirilmiştir. Kodun çalıştırılabilmesi için imza verilerinden oluşan *genuines* dosyası bulunmalıdır.
*genuines* dosyasındaki imza verileri, farklı boyutlarda olmaları ve network inputlarının 105x105 olması nedeniyle yeniden boyutlandırılmıştır. Daha sonra görüntüdeki gürültüler azaltılmıştır. Oluşturulan yeni görüntüler *originals* dosyasına kaydedilmiştir.
Ayrıca, imza verileri dosya adları kullanılarak imza resmi dosya yolu, kişi numarası ve imza numarasına göre ayrıştırılarak *Genuines.csv* dosyasına kaydedilmiştir.

**2- Siamese Network Eğitimi :** Veri önişleme bölümünde oluşturulan *originals*,*Genuines.csv* dosyaları Google Drive'a yüklendikten sonra aynı dosya içerisinde bir *Siamese_Network.ipynb* dosyası oluşturulmuştur. Oluşturulan *Siamese_Network.ipynb* dosyasında GPU kullanımı için gerekli konfigurasyonlar tamamlandıktan sonra çalışılmak istenen dizine gerekli komutlarla gidilmiştir. Kodu çalıştırabilmek için dosya yollarının düzenlenmesi gereklidir.
Gerekli kütüphaneler yüklendikten sonra *Genuines.csv* dosyası okunmuştur. Okunan verilerden elde edilen imza görüntü dosya yolu ve kişi numarası listeleri kullanılarak ikili imza verileri ve bu verilerin benzerlik değerleri oluşturulmuştur. Oluşturulan imza çiftlerinin %50'si aynı kişiye ait rastgele seçilen iki imzadan, diğerleri farklı kişilere ait rastgele seçilen 2 imzadan oluşturulmuştur. Aynı kişiye ait imza çiftinin benzerlik değerine 1, farklı kişilere ait imza çifti değerine 0 verilmiştir.
*euclidean_distance* ve *contrastive_loss* fonksiyonları tanımlandıktan sonra modelin performansını değerlendirebilmek için *accuracy* ve *compute_accuracy* metrik fonksiyonları tanımlanmıştır. Girdi boyutları (1,105,105) olacak şekilde cnn modeli oluşturulmuştur. Bu aşamadan sonra, ortak kullanılacak olan sinir ağı tanımlanmıştır. Oluşturulan model girdi olarak 2 imza resmi alıp, çıktı olarak iki resim arasındaki uzaklığı döndürmektedir. Oluşturulan *contrastive_loss* fonksiyonu ile model derlenmiştir.
Model derlendikten sonra eğitim için kullanılacak olan imza çiftleri ve etiket değerleri bölünerek test ve eğitim kümeleri oluşturulmuştur. Oluşturulan eğitim kümesi çiftleri ile model eğitildikten sonra,*compute_accuracy* fonksiyonu kullanılarak, modelin test verileri üzerindeki doğruluk değeri hesaplanmıştır. Test verileri üzerinden yapılan tahminlere bakıldığında benzer imzalar arası uzaklık 0'a yakınken etiket değeri 1'dir. Bu nedenle test tahmin değerleri 1'den çıkarılarak karmaşıklık matrisi oluşturulmuştur. Oluşturulan karmaşıklık matrisinden kesinlik, duyarlılık ve f1 skorları hesaplanmıştır. Test verilerinin gerçek ve tahmin değerleri kullanılarak ROC eğrisi oluşturulmuş ve bu eğriyle AUC skoru hesaplanmıştır. Son aşamada *kişi,imza numarası,kişi numarası,imza numarası,benzerlik skoru* çıktıları oluşturulan *similarity_score.txt* dosyasına istenen şekilde yazdırılmıştır.
