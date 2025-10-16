ögrenci no 250541074 
Mehmet sait TAYŞİ 




BASLA KULLANICI_GIRIS_VE_URUN_EKLEME
    EKRAN_GOSTER("Giriş Yap / Üye Ol / Misafir Devam Et")

    // KONTROL NOKTASI 1: Oturum Durumu Kontrolü
    EGER KULLANICI_OTURUMU_YOK_ISE
        SECIM = KULLANICIDAN_GIRIS_AL()
        EGER SECIM == "Giriş Yap" İSE
            KULLANICI_DOGRULA()
        EĞER_DE SECIM == "Üye Ol" İSE
            YENI_UYE_KAYDI_OLUSTUR()
        EĞER_DE SECIM == "Misafir Devam Et" İSE
            OTURUM_OLUSTUR("Misafir")
        BİTİR EĞER
    BİTİR EĞER

    // Ürün Seçimi ve Sepete Ekleme Döngüsü
    DÖNGÜ KULLANICI_ALISVERIS_YAPIYOR İKEN
        URUN_SECIMI = KULLANICIDAN_URUN_VE_ADET_AL()

        // KONTROL NOKTASI 2: Stok Kontrolü (Ürün Ekleme Anında)
        EĞER STOK_VERITABANI(URUN_SECIMI.ID) >= URUN_SECIMI.ADET İSE
            SEPTE_EKLE(URUN_SECIMI.ID, URUN_SECIMI.ADET)
            EKRAN_GOSTER(URUN_SECIMI.ADET + " adet " + URUN_SECIMI.ID + " sepete eklendi.")
        EĞER_DE
            EKRAN_GOSTER("Hata: Yeterli stok bulunmamaktadır.")
        BİTİR EĞER

        DEVAM_ET = KULLANICIDAN_SEPETE_GIT_VEYA_DEVAM_ET_AL()
        EĞER DEVAM_ET == "Sepete Git" İSE
            DONGUYU_KES
        BİTİR EĞER
    BİTİR DÖNGÜ

BİTİR KULLANICI_GIRIS_VE_URUN_EKLEME
2. SEPET YÖNETİMİ VE ÖN HESAPLAMA
Kod snippet'i

BASLA SEPET_YONETIMI_VE_HESAPLAMA
    SEPTE_URUNLERI_GOSTER()
    TOPLAM_TUTAR = SEPET_TOPLAM_TUTARI_HESAPLA()

    // 2.1 İndirim Kodu Uygulama
    EKRAN_GOSTER("İndirim Kodu Giriniz (Opsiyonel)")
    KOD = KULLANICIDAN_GIRIS_AL()

    // KONTROL NOKTASI 3: İndirim Kodu Kontrolü
    EĞER KOD_GEÇERLİ(KOD) İSE
        İNDİRİM_MİKTARI = İNDİRİM_HESAPLA(TOPLAM_TUTAR, KOD)
        TOPLAM_TUTAR = TOPLAM_TUTAR - İNDİRİM_MİKTARI
        EKRAN_GOSTER("İndirim Uygulandı. Yeni Toplam: " + TOPLAM_TUTAR)
    EĞER_DE
        EKRAN_GOSTER("Geçersiz veya Süresi Dolan İndirim Kodu.")
    BİTİR EĞER

    // 2.2 Adres Seçimi
    EKRAN_GOSTER("Teslimat Adresini Seçiniz/Giriniz")
    ADRES = KULLANICIDAN_ADRES_AL()

    // 2.3 Kargo Hesaplama
    KARGO_UCRETI = KARGO_HESAPLA(ADRES, SEPET_AGIRLIK_HACIM_HESAPLA())

    // KONTROL NOKTASI 4: Kargo Ücretsiz Kontrolü
    EĞER TOPLAM_TUTAR >= UCRETSIZ_KARGO_LIMITI İSE
        KARGO_UCRETI = 0
        EKRAN_GOSTER("Ücretsiz Kargo Kazandınız.")
    BİTİR EĞER

    SON_ODENECEK_TUTAR = TOPLAM_TUTAR + KARGO_UCRETI
    EKRAN_GOSTER("Sipariş Özeti: Sepet Toplam: " + TOPLAM_TUTAR + ", Kargo: " + KARGO_UCRETI + ", Genel Toplam: " + SON_ODENECEK_TUTAR)

BİTİR SEPET_YONETIMI_VE_HESAPLAMA
3. ÖDEME AŞAMASI
Kod snippet'i

BASLA ODEME_ISLEMI

    // KONTROL NOKTASI 5: Son Stok Kontrolü
    DÖNGÜ HER URUN_ID İÇİN SEPETTE
        EĞER STOK_VERITABANI(URUN_ID) < SEPET(URUN_ID).ADET İSE
            EKRAN_GOSTER("Hata: " + URUN_ID + " ürününde yeterli stok kalmamıştır. Lütfen sepetinizi düzenleyin.")
            // Başarısız Senaryo
            GERI_DON("SEPET_YONETIMI_VE_HESAPLAMA")
        BİTİR EĞER
    BİTİR DÖNGÜ

    // 3.1 Ödeme Yöntemi Seçimi
    EKRAN_GOSTER("Ödeme Yöntemini Seçiniz (Kart, Havale/EFT, Kapıda Ödeme)")
    ODEME_YONTEMI = KULLANICIDAN_ODEME_YONTEMI_AL()

    EĞER ODEME_YONTEMI == "Kredi/Banka Kartı" İSE
        KART_BILGILERI = KULLANICIDAN_KART_BILGISI_AL()
        
        // KONTROL NOKTASI 6: 3D Secure Doğrulaması
        KOD_DOGRULAMA_EKRANI_AC() // 3D Secure / OTP için
        DOGRULAMA_KODU = KULLANICIDAN_GIRIS_AL()
        
        // Sanal POS İletişimi
        ODEME_SONUCU = SANAL_POS_ILE_ISLEM_YAP(SON_ODENECEK_TUTAR, KART_BILGILERI, DOGRULAMA_KODU)

        // KONTROL NOKTASI 7: Ödeme Başarısı Kontrolü
        EĞER ODEME_SONUCU == "Başarılı" İSE
            SIPARIS_DURUMU = "Onaylandı"
        EĞER_DE
            SIPARIS_DURUMU = "Ödeme Başarısız"
            EKRAN_GOSTER("Ödeme reddedildi. Lütfen kart bilgilerinizi kontrol edin.")
            GERI_DON("ODEME_ISLEMI")
        BİTİR EĞER

    EĞER_DE ODEME_YONTEMI == "Havale/EFT" İSE
        EKRAN_GOSTER("Havale yapılacak banka bilgileri gönderildi. Ödeme bekleniyor.")
        SIPARIS_DURUMU = "Ödeme Bekleniyor"
        
    EĞER_DE ODEME_YONTEMI == "Kapıda Ödeme" İSE
        SIPARIS_DURUMU = "Onaylandı (Kapıda Ödeme)"
        
    BİTİR EĞER
    
    // 3.2 Sipariş Kaydı ve Stok Güncelleme
    EĞER SIPARIS_DURUMU == "Onaylandı" VEYA SIPARIS_DURUMU == "Onaylandı (Kapıda Ödeme)" İSE
        YENI_SIPARIS_OLUSTUR(ADRES, ODEME_YONTEMI, SON_ODENECEK_TUTAR, SEPET)
        STOKTAN_DUS(SEPET)
        FATURA_OLUSTUR(YENI_SIPARIS.ID)
        KULLANICIYA_MAIL_GÖNDER("Siparişiniz Alınmıştır", YENI_SIPARIS.ID)
    BİTİR EĞER

    EKRAN_GOSTER("Sipariş İşlemi Tamamlandı. Durum: " + SIPARIS_DURUMU)

BİTİR ODEME_ISLEMI
