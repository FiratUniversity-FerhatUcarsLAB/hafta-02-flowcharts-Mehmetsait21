bu iki kodu birleştirip tek bir sayfa şeklinde yazar mısın


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
digraph HastaneSistemiAkisSemasi {
    // Grafiğin genel ayarları
    rankdir=TB; // Top to Bottom (Yukarıdan Aşağıya) akış
    node [shape=box, style="filled", fillcolor="#F4F4F4", fontname="Arial"];
    edge [fontname="Arial"];

    // Başlangıç ve Bitiş Düğümleri
    start [label="Sistem Başlangıç\n(AnaHastaneSistemi)", shape=Mdiamond, fillcolor="#CCFFCC"];
    end [label="Sistem Bitiş", shape=Mdiamond, fillcolor="#FFCCCC"];

    // Ana Akış Adımları
    kimlik_al [label="Kimlik Bilgilerini Al"];
    kimlik_dogrula [label="Kimlik Doğrulama Başarılı mı?", shape=diamond, fillcolor="#FFFFCC"];
    ana_menu [label="Ana Menü Göster\n(Randevu, Tahlil, Çıkış)", shape=box, fillcolor="#ADD8E6"];
    secim_randevu [label="Seçim = 1 (Randevu)", shape=diamond, fillcolor="#FFFFCC"];
    cikis [label="Seçim = 3 (Çıkış)", shape=diamond, fillcolor="#FFFFCC"];

    // Randevu Modülü Düğümleri (SubGraph)
    subgraph cluster_randevu {
        label = "Randevu Modülü";
        style = "rounded";
        color = "#32CD32"; // Yeşil

        randevu_start [label="RandevuModulunuCalistir", shape=box, fillcolor="#90EE90"];
        poliklinik_sec [label="Poliklinik ve Doktor Seçimi"];
        saat_uygun [label="Uygun Saat Var mı?", shape=diamond];
        onay_al [label="Randevu Onay Al", shape=diamond];
        kaydet_sms [label="Randevuyu Kaydet\n& SMS Gönder"];
        randevu_end [label="Randevu Modülü Bitiş", shape=box, fillcolor="#90EE90"];
    }

    // Tahlil Modülü Düğümleri (SubGraph)
    subgraph cluster_tahlil {
        label = "Tahlil Modülü";
        style = "rounded";
        color = "#1E90FF"; // Mavi

        tahlil_start [label="TahlilModulunuCalistir", shape=box, fillcolor="#ADD8E6"];
        tahlil_var [label="Kayıtlı Tahlil Var mı?", shape=diamond];
        sonuc_hazir [label="Sonuç Hazır mı?", shape=diamond];
        goster_indir [label="Sonucu Göster\n(PDF İndirme Opsiyonu)"];
        bekle [label="Bekleme Mesajı Göster"];
        tahlil_end [label="Tahlil Modülü Bitiş", shape=box, fillcolor="#ADD8E6"];
    }

    // ****************************
    // AKIŞ BAĞLANTILARI
    // ****************************

    // Ana Akış
    start -> kimlik_al;
    kimlik_al -> kimlik_dogrula;
    kimlik_dogrula -> ana_menu [label="Evet"];
    kimlik_dogrula -> end [label="Hayır\n(Hata Mesajı)"];

    // Ana Menü Döngüsü
    ana_menu -> secim_randevu;

    // Randevu Seçimi
    secim_randevu -> randevu_start [label="Evet"];
    secim_randevu -> cikis [label="Hayır"]; // Randevu değilse, diğer seçeneklere bak
    
    // Çıkış Kontrolü
    cikis -> end [label="Evet"];
    cikis -> tahlil_start [label="Hayır\n(Seçim=2)"]; // Eğer 3 değilse ve 1 değilse (yani 2 ise)

    // ****************************
    // Randevu Modülü Akışı
    // ****************************
    randevu_start -> poliklinik_sec;
    poliklinik_sec -> saat_uygun;
    saat_uygun -> onay_al [label="Evet"];
    saat_uygun -> randevu_end [label="Hayır\n(Hata)"];
    onay_al -> kaydet_sms [label="Evet"];
    onay_al -> randevu_end [label="Hayır\n(İptal)"];
    kaydet_sms -> randevu_end;
    randevu_end -> ana_menu [label="Geri"];

    // ****************************
    // Tahlil Modülü Akışı
    // ****************************
    tahlil_start -> tahlil_var;
    tahlil_var -> sonuc_hazir [label="Evet"];
    tahlil_var -> tahlil_end [label="Hayır\n(Hata)"];

    sonuc_hazir -> goster_indir [label="HAZIR"];
    sonuc_hazir -> bekle [label="BEKLEMEDE"];
    sonuc_hazir -> bekle [label="ONAY BEKLİYOR\nvb."];

    goster_indir -> tahlil_end;
    bekle -> tahlil_end;

    tahlil_end -> ana_menu [label="Geri"];
}
