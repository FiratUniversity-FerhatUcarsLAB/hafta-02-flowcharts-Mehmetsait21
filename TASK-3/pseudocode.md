FONKSIYON AnaHastaneSistemi()
    BASLANGIC

    KULLANICI_GIRISI = KimlikBilgileriniAl()

    // 1. Kimlik Doğrulama
    EGER KimlikDogrulamaBasarili(KULLANICI_GIRISI) ISE
        MESAJ_GOSTER("Giriş Başarılı. Hoş geldiniz.")

        DONGU_BASLANGIC
            // Ana Menü
            MESAJ_GOSTER("Lütfen yapmak istediğiniz işlemi seçiniz:")
            MESAJ_GOSTER("1: Randevu Al/Görüntüle")
            MESAJ_GOSTER("2: Tahlil Sonucu Görüntüle")
            MESAJ_GOSTER("3: Çıkış")
            Secim = KullanicidanSecimAl(1, 2, 3)

            EGER Secim ESIT 1 ISE
                RandevuModulunuCalistir(KULLANICI_GIRISI)
            BASKA EGER Secim ESIT 2 ISE
                TahlilModulunuCalistir(KULLANICI_GIRISI)
            BASKA EGER Secim ESIT 3 ISE
                DONGU_BITIR
            BASKA
                MESAJ_GOSTER("Geçersiz seçim. Lütfen tekrar deneyiniz.")
            BITIR EGER
        DONGU_SONU

    BASKA
        MESAJ_GOSTER("Hata: Kimlik doğrulama başarısız. Lütfen bilgilerinizi kontrol edin.")
    BITIR EGER

    BITIS


// ************************************************************
//                          MODÜL 1: RANDEVU SİSTEMİ
// ************************************************************
FONKSIYON RandevuModulunuCalistir(KULLANICI_BILGILERI)
    BASLANGIC

    // Poliklinik Seçimi
    POLIKLINIK_LISTESI = TumPoliklinikleriGetir()
    PoliklinikSecimi = KullanicidanSecimAl(POLIKLINIK_LISTESI)

    // Doktor Listesi Görüntüleme
    DOKTOR_LISTESI = DoktorlariGetir(PoliklinikSecimi)
    EGER DOKTOR_LISTESI BOS DEGIL ISE
        DoktorSecimi = KullanicidanSecimAl(DOKTOR_LISTESI)

        // Uygun Saatleri Gösterme
        MEVCUT_RANDEVU_SAATLERI = UygunSaatleriBul(DoktorSecimi, BugundenSonrakiXGun)
        EGER MEVCUT_RANDEVU_SAATLERI BOS DEGIL ISE
            RandevuSaatSecimi = KullanicidanSecimAl(MEVCUT_RANDEVU_SAATLERI)

            // Randevu Onaylama
            Onay = KullanicidanOnayAl("Randevuyu onaylıyor musunuz?")

            EGER Onay ESIT "EVET" ISE
                RandevuID = RandevuKaydet(KULLANICI_BILGILERI.ID, DoktorSecimi.ID, RandevuSaatSecimi.ID)

                EGER RandevuID FARKLI 0 ISE
                    // SMS Gönderme
                    SMS_MESAJI = "Randevunuz oluşturuldu. ID: " + RandevuID + " Tarih: " + RandevuSaatSecimi.Tarih
                    SMSGonder(KULLANICI_BILGILERI.TelefonNumarasi, SMS_MESAJI)
                    MESAJ_GOSTER("Randevunuz oluşturuldu ve SMS gönderildi.")
                BASKA
                    MESAJ_GOSTER("Hata: Randevu kaydı sırasında bir sorun oluştu.")
                BITIR EGER

            BASKA
                MESAJ_GOSTER("Randevu alma işlemi iptal edildi.")
            BITIR EGER
        BASKA
            MESAJ_GOSTER("Uygun randevu saati bulunmamaktadır.")
        BITIR EGER
    BASKA
        MESAJ_GOSTER("Seçilen poliklinikte doktor bulunmamaktadır.")
    BITIR EGER

    BITIS


// ************************************************************
//                         MODÜL 2: TAHLİL SİSTEMİ
// ************************************************************
FONKSIYON TahlilModulunuCalistir(KULLANICI_BILGILERI)
    BASLANGIC

    // Tahlil Varlığı Kontrolü
    TAHLIL_LISTESI = TahlilleriGetir(KULLANICI_BILGILERI.ID)

    EGER TAHLIL_LISTESI BOS DEGIL ISE
        MESAJ_GOSTER("Mevcut Tahlilleriniz:")
        TahlilSecimi = KullanicidanSecimAl(TAHLIL_LISTESI)

        // Sonuç Hazır mı Kontrolü
        TahlilDurumu = DurumuKontrolEt(TahlilSecimi.TahlilID)

        EGER TahlilDurumu ESIT "HAZIR" ISE
            // Görüntüleme ve PDF İndirme
            MESAJ_GOSTER("Tahlil Sonucu: Görüntülenmeye Hazır.")
            TahlilSonucuVerisi = TahlilSonucunuGetir(TahlilSecimi.TahlilID)
            EkrandaGoster(TahlilSonucuVerisi)

            Secim = KullanicidanSecimAl("Sonucu PDF olarak indirmek ister misiniz? (EVET/HAYIR)")
            EGER Secim ESIT "EVET" ISE
                PDFOluşturveIndir(TahlilSonucuVerisi, TahlilSecimi.TahlilID)
                MESAJ_GOSTER("PDF indirme işlemi başlatıldı.")
            BITIR EGER

        BASKA EGER TahlilDurumu ESIT "BEKLEMEDE" ISE
            MESAJ_GOSTER("Tahlil sonucu henüz sisteme yüklenmemiştir. Lütfen daha sonra tekrar deneyiniz.")

        BASKA
            MESAJ_GOSTER("Tahlil durumu (Örn: ONAY BEKLİYOR) veya hata oluştu.")
        BITIR EGER

    BASKA
        MESAJ_GOSTER("Sisteme kayıtlı sonuçlanmış herhangi bir tahliliniz bulunmamaktadır.")
    BITIR EGER

    BITIS
