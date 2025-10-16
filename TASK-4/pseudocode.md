FONKSIYON Ders_Kayıt_Kontrol_Sistemi (Ogrenci_ID, Secilen_Dersler_Listesi)

    // 1. GENEL GİRİŞ KONTROLLERİ
    EĞER Ogrenci_Borcu_Var_mı(Ogrenci_ID) İSE
        EKRANA_YAZ "HATA: Ders kaydı yapabilmek için öğrenim ücreti borcunuzu ödemelisiniz."
        DÖN (HATA)
    SON

    // 2. MAKSİMUM KREDİ LİMİTİ KONTROLÜ İÇİN LİMİTİ BELİRLE
    GANO = Ogrencinin_GANO_Getir(Ogrenci_ID)
    MAKS_KREDI_LIMITI = 30 // Varsayılan AKTS/Kredi Limiti

    EĞER GANO >= 2.80 İSE
        MAKS_KREDI_LIMITI = 45 // Üst sınıftan ders alabilme şartıyla yüksek limit
    DEĞİLSE EĞER GANO < 1.80 İSE
        MAKS_KREDI_LIMITI = 30 // Başarısız derslere öncelik için temel limit (veya daha az)
    SON

    SECILEN_TOPLAM_KREDI = 0
    KAYIT_GEÇERLİ = DOĞRU
    ÇAKIŞAN_DERS_VAR = YANLIŞ

    // 3. SEÇİLEN HER DERS İÇİN KONTROLLERİ UYGULA (DÖNGÜ)
    HER DERS (Ders_A) İÇİN Secilen_Dersler_Listesi İÇİNDE YAP
        // A. KONTENJAN KONTROLÜ
        EĞER Dersin_Kontenjanı_Dolu_mu(Ders_A) İSE
            EKRANA_YAZ "HATA: " + Ders_A.Adı + " dersinin kontenjanı doludur. Başka bir şube seçin."
            KAYIT_GEÇERLİ = YANLIŞ
            DEVAM ET // Bir sonraki derse geç
        SON

        // B. ÖN KOŞUL KONTROLÜ
        EĞER Dersin_On_Kosulu_Var_mı(Ders_A) İSE
            ON_KOSULLAR = Dersin_On_Kosullarını_Getir(Ders_A)
            HER ON_KOSUL_DERS (Ders_B) İÇİN ON_KOSULLAR İÇİNDE YAP
                EĞER Ogrenci_Ders_Basarısız_mı(Ogrenci_ID, Ders_B) VEYA Ogrenci_Ders_Almadı_mı(Ogrenci_ID, Ders_B) İSE
                    EKRANA_YAZ "HATA: " + Ders_A.Adı + " dersi için ön koşul olan " + Ders_B.Adı + " dersini geçmelisiniz."
                    KAYIT_GEÇERLİ = YANLIŞ
                    DÖNGÜYÜ_KES // İçteki döngüyü kes, Ders_A için yeterli
                SON
            SON
        SON

        // C. ZAMAN ÇAKIŞMASI KONTROLÜ (İÇ İÇE DÖNGÜ)
        HER DERS (Ders_B) İÇİN Secilen_Dersler_Listesi İÇİNDE YAP
            EĞER Ders_A.ID != Ders_B.ID VE Dersler_Zaman_Cakısıyor_mu(Ders_A, Ders_B) İSE
                EKRANA_YAZ "HATA: " + Ders_A.Adı + " ve " + Ders_B.Adı + " derslerinin saatleri çakışmaktadır."
                ÇAKIŞAN_DERS_VAR = YANLIŞ // Dış döngüden sonra tekrar kontrol etmemek için
                KAYIT_GEÇERLİ = YANLIŞ
                DÖNGÜYÜ_KES // İçteki döngüyü kes
            SON
        SON

        // D. TOPLAM KREDİ HESAPLAMA
        SECILEN_TOPLAM_KREDI = SECILEN_TOPLAM_KREDI + Ders_A.Kredi
    SON // DÖNGÜ SONU

    // 4. TOPLAM KREDİ LİMİTİ KONTROLÜ (DÖNGÜ DIŞI)
    EĞER KAYIT_GEÇERLİ == DOĞRU VE SECILEN_TOPLAM_KREDI > MAKS_KREDI_LIMITI İSE
        EKRANA_YAZ "UYARI: Seçtiğiniz toplam kredi (" + SECILEN_TOPLAM_KREDI + ") limitinizi (" + MAKS_KREDI_LIMITI + ") aşmaktadır. Kredi limitiniz düşürülecektir."
        // BU NOKTADA SİSTEM OTOMATİK DÜZELTME YAPABİLİR VEYA ÖĞRENCİDEN DÜZELTME İSTEYEBİLİR.
        // Şimdilik sadece uyarı verelim.
        KAYIT_GEÇERLİ = YANLIŞ // Öğrenci Düzeltmeli
    SON

    // 5. SONUÇ VE ONAYLAMA ADIMI
    EĞER KAYIT_GEÇERLİ == DOĞRU İSE
        EKRANA_YAZ "Tebrikler! Ders seçimleriniz sistem kontrollerinden geçmiştir. Kaydınız danışman onayına gönderilmiştir."
        Gonder_Danısman_Onayı(Ogrenci_ID, Secilen_Dersler_Listesi)
        DÖN (BAŞARILI)
    DEĞİLSE
        EKRANA_YAZ "Lütfen yukarıdaki hataları kontrol edip ders seçiminizi düzenleyiniz."
        DÖN (HATA)
    SON

SON FONKSIYON
