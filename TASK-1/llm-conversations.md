İsim - Soy isim Mehmet Sait Tayşi
Öğrenci No:250541074


ATM para cekme sistemi için detaylı pseudocode yazmanı istiyorum.

Sunları icermeli: PIN dogrulama (3 hak), bakiye kontrolu, tutar

kontrol¨u (20 TL katları), gunluk limit kontrolu, islem tekrarı secenegi. tum

adımları BASLA, OKU, YAZ, EGER- ˘ ˙

ISE, DONG U yapılarıyla yaz.

Harika bir istek! ATM para çekme sistemi için detaylı bir pseudocode hazırlayalım. Türkçe komutları ve belirtilen yapıları (BAŞLA, OKU, YAZ, EĞER-İSE, DÖNGÜ) kullanarak adımları yazacağım.

ATM Para Çekme Sistemi Pseudocode

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
Bu pseudocode’u Graphviz DOT diline ¸cevir. Ba¸sla/Bitir i¸cin oval,

girdi/¸cıktı i¸cin parallelogram, i¸slem i¸cin box, karar i¸cin diamond kullan.

T¨um okları ve etiketleri ekle. renkli olsun

Harika bir fikir! Yukarıdaki pseudocode'u Graphviz DOT diline çevirelim. Akış şeması standartlarına uygun olarak belirtilen şekilleri ve renkleri kullanacağım.

Kod snippet'i
digraph ATM_Para_Cekme_Sistemi {
    // Grafik Ayarları
    rankdir=TB; // Akış yönü: Yukarıdan Aşağıya (Top to Bottom)
    node [shape=box, style=filled, fontname="Helvetica", color="#4E4E4E", fontcolor=black];

    // Düğümlerin Tanımlanması
    
    // BAŞLANGIÇ
    start [label="BAŞLA", shape=oval, fillcolor="#A8DADC"];
    
    // PIN Doğrulama
    pin_loop_cond [label="PIN_DENEME_SAYISI < 3 VE KART_BLOKE = YANLIŞ", shape=diamond, fillcolor="#FFB347"];
    pin_oku [label="PIN kodunuzu giriniz.\n(OKU KULLANICI_PIN)", shape=parallelogram, fillcolor="#C3E88D"];
    pin_karar [label="KULLANICI_PIN = DOĞRU_PIN", shape=diamond, fillcolor="#FF6B6B"];
    pin_basarili [label="PIN Doğrulama Başarılı.", shape=box, fillcolor="#66BB6A"];
    pin_hata [label="PIN_DENEME_SAYISI += 1\nYAZ 'Hatalı PIN'", shape=box, fillcolor="#E57373"];
    pin_bloke_karar [label="PIN_DENEME_SAYISI = 3", shape=diamond, fillcolor="#FF6B6B"];
    kart_bloke [label="KART_BLOKE = DOĞRU\nYAZ 'Kartınız bloke edildi.'", shape=box, fillcolor="#E53935", fontcolor=white];
    
    // Ana İşlem Döngüsü
    kart_bloke_kontrol [label="KART_BLOKE = YANLIŞ", shape=diamond, fillcolor="#FFB347"];
    ana_islem_loop_cond [label="DEVAM_ET = EVET", shape=diamond, fillcolor="#81C784"];
    islem_sec [label="İşlem Seçimi Yapınız\n(OKU İŞLEM_SEÇİM)", shape=parallelogram, fillcolor="#C3E88D"];
    secim_karar [label="İŞLEM_SEÇİM = 1", shape=diamond, fillcolor="#FFB347"];
    
    // Para Çekme İşlemi (İç Döngü)
    cekme_loop_cond [label="BAŞARILI_ÇEKME = YANLIŞ", shape=diamond, fillcolor="#FFB347"];
    tutar_oku [label="Çekilecek Tutarı Giriniz\n(OKU ÇEKİLECEK_TUTAR)", shape=parallelogram, fillcolor="#C3E88D"];
    bakiye_karar [label="ÇEKİLECEK_TUTAR > BAKİYE", shape=diamond, fillcolor="#FF6B6B"];
    bakiye_hata [label="YAZ 'Yetersiz Bakiye'", shape=box, fillcolor="#E57373"];
    tutar_kat_karar [label="ÇEKİLECEK_TUTAR MOD 20 = 0", shape=diamond, fillcolor="#FFB347"];
    tutar_kat_hata [label="YAZ '20 TL katları giriniz'", shape=box, fillcolor="#E57373"];
    limit_karar [label="ÇEKİLEN_TOPLAM + TUTAR <= LİMİT", shape=diamond, fillcolor="#81C784"];
    limit_hata [label="YAZ 'Günlük Limit Aşımı'", shape=box, fillcolor="#E57373"];
    
    // İşlem Başarılı
    para_ver [label="BAKİYE -= TUTAR\nÇEKİLEN_TOPLAM += TUTAR\nBAŞARILI_ÇEKME = DOĞRU\nYAZ 'Para veriliyor.'", shape=box, fillcolor="#66BB6A"];
    
    // Tekrar Deneme
    tekrar_deneme_karar [label="BAŞARILI_ÇEKME = YANLIŞ", shape=diamond, fillcolor="#FFB347"];
    tekrar_deneme_sor [label="İşlemi Tekrar Deneyin?\n(OKU SEÇİM)", shape=parallelogram, fillcolor="#C3E88D"];
    vazgec_karar [label="SEÇİM = 'H'", shape=diamond, fillcolor="#FF6B6B"];
    
    // İşlemi Sonlandır
    islem_son [label="YAZ 'İşlem sonlandırılıyor.'\nDEVAM_ET = YANLIŞ", shape=box, fillcolor="#4FC3F7"];
    gecersiz_secim [label="YAZ 'Geçersiz işlem.'", shape=box, fillcolor="#E57373"];
    
    // BİTİŞ
    end [label="DUR", shape=oval, fillcolor="#A8DADC"];

    // Akış Bağlantıları
    
    // PIN Doğrulama Akışı
    start -> pin_loop_cond;
    pin_loop_cond -> pin_oku [label="EVET"];
    pin_loop_cond -> kart_bloke_kontrol [label="HAYIR"]; // Döngü biterse
    
    pin_oku -> pin_karar;
    
    pin_karar -> pin_basarili [label="EVET"];
    pin_karar -> pin_hata [label="HAYIR"];
    
    pin_hata -> pin_bloke_karar;
    pin_bloke_karar -> kart_bloke [label="EVET"];
    pin_bloke_karar -> pin_loop_cond [label="HAYIR"];
    kart_bloke -> kart_bloke_kontrol; // Bloke olduysa döngü dışına
    
    pin_basarili -> kart_bloke_kontrol; // Başarılıysa doğrudan ana işleme
    
    // Ana İşlem Akışı
    kart_bloke_kontrol -> ana_islem_loop_cond [label="EVET"];
    kart_bloke_kontrol -> end [label="HAYIR"]; // Bloke ise DUR
    
    ana_islem_loop_cond -> islem_sec [label="EVET"];
    ana_islem_loop_cond -> end [label="HAYIR"]; // Kullanıcı "2" seçtiyse veya vazgeçtiyse DUR
    
    islem_sec -> secim_karar;
    
    // İşlem 1: Para Çekme
    secim_karar -> cekme_loop_cond [label="1"];
    
    // İşlem 2: Sonlandır
    secim_karar -> islem_son [label="2"];
    
    // Diğer Seçim
    secim_karar -> gecersiz_secim [label="Diğer"];
    gecersiz_secim -> ana_islem_loop_cond;
    
    islem_son -> ana_islem_loop_cond; // DEVAM_ET = YANLIŞ ataması ile çıkış sağlanacak
    
    // Para Çekme İç Döngüsü
    cekme_loop_cond -> tutar_oku [label="EVET"];
    cekme_loop_cond -> ana_islem_loop_cond [label="HAYIR"]; // Başarılı Çekme VEYA Vazgeçme
    
    tutar_oku -> bakiye_karar;
    
    bakiye_karar -> bakiye_hata [label="EVET"];
    bakiye_karar -> tutar_kat_karar [label="HAYIR"];
    
    tutar_kat_karar -> tutar_kat_hata [label="HAYIR"];
    tutar_kat_karar -> limit_karar [label="EVET"];
    
    limit_karar -> limit_hata [label="HAYIR"];
    limit_karar -> para_ver [label="EVET"];
    
    para_ver -> cekme_loop_cond; // BAŞARILI_ÇEKME = DOĞRU olduğu için HAYIR dalından ana döngüye dönecek
    
    bakiye_hata -> tekrar_deneme_karar;
    tutar_kat_hata -> tekrar_deneme_karar;
    limit_hata -> tekrar_deneme_karar;
    
    // Tekrar Deneme Akışı
    tekrar_deneme_karar -> tekrar_deneme_sor [label="EVET"];
    tekrar_deneme_karar -> ana_islem_loop_cond [label="HAYIR"]; // Başarılı çekme olduysa (bu düğüme hiç gelmemeli, sadece yapısal olarak var)

    tekrar_deneme_sor -> vazgec_karar;
    
    vazgec_karar -> ana_islem_loop_cond [label="EVET"]; // Vazgeçti ("H" ise) -> Ana döngüye dön
    vazgec_karar -> cekme_loop_cond [label="HAYIR"]; // Tekrar dene ("E" ise) -> İç döngü başına dön

}
