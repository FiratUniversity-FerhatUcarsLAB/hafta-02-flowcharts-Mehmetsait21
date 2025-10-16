ögrenci no 250541074
ad ve soyad Mehmet Sait TAYŞİ
BAŞLA

    // SABİTLER
    DEĞİŞKENLER:
    DOĞRU_PIN = "1234"           // Gerçek sistemde veritabanından alınır
    BAKİYE = 1500.00             // Örnek Bakiye
    GÜNLÜK_LİMİT = 1000.00       // Örnek Günlük Limit
    GÜNLÜK_ÇEKİLEN_TOPLAM = 0.00  // Başlangıçta 0
    MAKS_PIN_DENEME = 3
    PIN_DENEME_SAYISI = 0
    KART_BLOKE = YANLIŞ          // Başlangıçta bloke değil
    DEVAM_ET = EVET              // İşlem tekrarı için

    // 1. PIN DOĞRULAMA DÖNGÜSÜ
    DÖNGÜ: PIN_DENEME_SAYISI < MAKS_PIN_DENEME VE KART_BLOKE = YANLIŞ
        YAZ "Lütfen PIN kodunuzu giriniz:"
        OKU KULLANICI_PIN

        EĞER KULLANICI_PIN = DOĞRU_PIN İSE
            YAZ "PIN Doğrulama Başarılı."
            ÇIKIŞ DÖNGÜDEN // Başarılıysa döngüyü sonlandır
        DEĞİLSE
            PIN_DENEME_SAYISI = PIN_DENEME_SAYISI + 1
            YAZ "Hatalı PIN. Kalan deneme hakkınız: " + (MAKS_PIN_DENEME - PIN_DENEME_SAYISI)
            EĞER PIN_DENEME_SAYISI = MAKS_PIN_DENEME İSE
                KART_BLOKE = DOĞRU
                YAZ "Çok fazla hatalı giriş. Kartınız bloke edilmiştir."
            SON EĞER
        SON EĞER
    SON DÖNGÜ

    // 2. ANA İŞLEM DÖNGÜSÜ (EĞER KART BLOKE DEĞİLSE)
    EĞER KART_BLOKE = YANLIŞ İSE
        DÖNGÜ: DEVAM_ET = EVET
            YAZ "Lütfen yapmak istediğiniz işlemi seçin:"
            YAZ "1: Para Çekme"
            YAZ "2: İşlemi Sonlandır"
            OKU İŞLEM_SEÇİM

            EĞER İŞLEM_SEÇİM = 1 İSE
                // Para Çekme İşlemleri
                BAŞARILI_ÇEKME = YANLIŞ
                DÖNGÜ: BAŞARILI_ÇEKME = YANLIŞ
                    YAZ "Çekmek istediğiniz tutarı giriniz (20 TL'nin katları olmalı):"
                    OKU ÇEKİLECEK_TUTAR

                    // 3. BAKİYE KONTROLÜ
                    EĞER ÇEKİLECEK_TUTAR > BAKİYE İSE
                        YAZ "Yetersiz Bakiye. Mevcut Bakiyeniz: " + BAKİYE + " TL"
                    DEĞİLSE
                        // 4. TUTAR KONTROLÜ (20 TL KATLARI)
                        EĞER ÇEKİLECEK_TUTAR MOD 20 = 0 İSE
                            // 5. GÜNLÜK LİMİT KONTROLÜ
                            EĞER (GÜNLÜK_ÇEKİLEN_TOPLAM + ÇEKİLECEK_TUTAR) <= GÜNLÜK_LİMİT İSE
                                // İşlemi Gerçekleştir
                                BAKİYE = BAKİYE - ÇEKİLECEK_TUTAR
                                GÜNLÜK_ÇEKİLEN_TOPLAM = GÜNLÜK_ÇEKİLEN_TOPLAM + ÇEKİLECEK_TUTAR
                                YAZ "İşlem Başarılı. Lütfen paranızı ve kartınızı alınız."
                                YAZ "Yeni Bakiyeniz: " + BAKİYE + " TL"
                                YAZ "Bugün Çekilen Toplam: " + GÜNLÜK_ÇEKİLEN_TOPLAM + " TL"
                                BAŞARILI_ÇEKME = DOĞRU // İç döngüden çık

                            DEĞİLSE // Günlük Limit Aşımı
                                YAZ "Günlük Para Çekme Limitinizi aşıyorsunuz."
                                YAZ "Kalan Günlük Limitiniz: " + (GÜNLÜK_LİMİT - GÜNLÜK_ÇEKİLEN_TOPLAM) + " TL"
                            SON EĞER
                        DEĞİLSE // 20 TL Katları Değil
                            YAZ "Lütfen 20 TL'nin katları bir tutar giriniz."
                        SON EĞER
                    SON EĞER

                    EĞER BAŞARILI_ÇEKME = YANLIŞ İSE
                        YAZ "İşlemi Tekrar Denemek ister misiniz? (E/H)"
                        OKU TEKRAR_DENEME_SEÇİM
                        EĞER TEKRAR_DENEME_SEÇİM = "H" İSE
                            ÇIKIŞ DÖNGÜDEN // Başarısız durumda kullanıcı tekrar denemek istemezse
                        SON EĞER
                    SON EĞER

                SON DÖNGÜ // Para Çekme İç Döngüsü Sonu (Başarılı çekilene veya kullanıcı vazgeçene kadar)

            DEĞİLSE EĞER İŞLEM_SEÇİM = 2 İSE
                YAZ "İşlem sonlandırılıyor. Kartınızı almayı unutmayınız. İyi günler dileriz."
                DEVAM_ET = YANLIŞ // Ana döngüyü sonlandır
            DEĞİLSE
                YAZ "Geçersiz işlem seçimi. Lütfen tekrar deneyiniz."
            SON EĞER
        SON DÖNGÜ // Ana İşlem Döngüsü Sonu

    SON EĞER // Kart Bloke Kontrolü Sonu

DUR
