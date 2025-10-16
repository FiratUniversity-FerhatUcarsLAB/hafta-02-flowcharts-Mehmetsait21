“Universite ders kayıt sisteminin genel akısını acıkla: giris, ders listesi, ders ¨
secimi, kontroller, onaylama.”
2. Kontrol Mekanizmaları
“Ders kaydında yapılması gereken kontrolleri listele: kontenjan, ¨on kosul,
zaman ¸cakısması, kredi limiti, danısman onayı.”
3. Pseudocode Olusturma
“Tum bu kontrollerle birlikte detaylı pseudocode yaz. ˙
Icice EGER- ˘ ˙
ISE
yapıları ve donguler kullan.”
4. DOT donoşumu
“Bu pseudocode’u Graphviz DOT diline ¸cevir. Her kontrol noktasını
diamond ile goster.”
F.U - O.M.K (TF Yazılım Muh.) ¨ Hafta 2 - Akıs Diyagramları Lab 5
Kontrol Mekanizması,Açıklama
Kontenjan Kontrolü,"Öğrencinin seçtiği dersin, o şube için belirlenen maksimum öğrenci kapasitesini (kontenjanını) aşıp aşmadığı kontrol edilir. Kapasite doluysa ders seçilemez."
Ön Koşul Kontrolü,"Seçilen bir dersin, alınabilmesi için öğrencinin daha önce başarılı olması gereken zorunlu dersler (ön koşullar) olup olmadığı kontrol edilir. Öğrenci ön koşulu sağlamıyorsa dersi alamaz."
Zaman Çakışması Kontrolü,Öğrencinin seçtiği derslerin haftalık programlarının gün ve saat bazında birbirleriyle (örneğin aynı saatte iki farklı ders) çakışıp çakışmadığı kontrol edilir. Çakışma varsa ders seçimi engellenir.
Kredi Limiti Kontrolü,Öğrencinin o dönemde alabileceği toplam AKTS/Kredi miktarının üst sınırının aşılıp aşılmadığı kontrol edilir. (Genellikle GANO'ya göre değişir: GANO ≤ 1.80 olan öğrenci daha az kredi alabilir.)
Danışman Onayı,"Sistemdeki kontroller tamamlandıktan sonra, son ve kritik onay mekanizmasıdır. Akademik Danışman, öğrencinin kaydının müfredatına ve akademik kurallara uygunluğunu kontrol ederek kaydı onaylar veya düzeltme ister."
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

digraph DersKayitAkisi {
    // Grafik Ayarları
    rankdir=TB; // Yukaridan asagi (Top to Bottom)
    node [shape=box]; // Varsayilan dugum sekli

    // Dugumler (N: Node)
    N0 [label="BAŞLA\n(Öğrenci Girişi)"];
    N1 [label="Harç Borcu Kontrolü\n(Borcu Var mı?)", shape=diamond];
    N2 [label="GANO Getir ve Maks. Kredi Limiti Belirle"];
    N3 [label="Secilen_Dersler_Listesi için DÖNGÜ BAŞLAT"];
    N4 [label="Ders A: Kontenjan Dolu mu?", shape=diamond];
    N5 [label="Ders A: Ön Koşul Var mı?", shape=diamond];
    N6 [label="Ön Koşul Dersi (Ders B) Başarılı mı?", shape=diamond];
    N7 [label="Dersler A ve B: Zaman Çakışıyor mu?", shape=diamond];
    N8 [label="Toplam Krediyi Hesapla"];
    N9 [label="DÖNGÜ SONU"];
    N10 [label="Toplam Kredi > Limit mi?", shape=diamond];
    N11 [label="Kayit Geçerli mi?", shape=diamond];
    N12 [label="Danışman Onayına Gönder\n(KAYIT BAŞARILI)", shape=box, style=filled, fillcolor=lightgreen];
    N13 [label="HATA: Borç Öde!", shape=box, style=filled, fillcolor=salmon];
    N14 [label="HATA: Kontenjan Dolu!", shape=box, style=filled, fillcolor=salmon];
    N15 [label="HATA: Ön Koşul Eksik!", shape=box, style=filled, fillcolor=salmon];
    N16 [label="HATA: Zaman Çakışması!", shape=box, style=filled, fillcolor=salmon];
    N17 [label="HATA: Kredi Limiti Aşıldı!\n(Düzeltme Gerekli)", shape=box, style=filled, fillcolor=salmon];
    N18 [label="HATA: Seçimleri Düzenle\n(KAYIT BAŞARISIZ)", shape=box, style=filled, fillcolor=red];
    N19 [label="SON"];

    // Akış Bağlantıları
    N0 -> N1;
    
    // Harç Kontrolü
    N1:e -> N13 [label="Evet"];
    N1:s -> N2 [label="Hayır"];

    // Kredi Limiti Belirleme
    N2 -> N3;

    // Dersler Üzerinde Döngü
    N3 -> N4 [label="Her Ders A"];

    // Kontenjan Kontrolü
    N4 -> N14 [label="Evet\n(Kayit Geçerli=Hata)"];
    N4 -> N5 [label="Hayır"];

    // Ön Koşul Kontrolü
    N5 -> N6 [label="Ön Koşul Var"];
    N5 -> N7 [label="Ön Koşul Yok"];

    // Ön Koşul Başarı Kontrolü
    N6 -> N15 [label="Başarısız\n(Kayit Geçerli=Hata)"];
    N6 -> N7 [label="Başarılı"];

    // Zaman Çakışması Kontrolü
    N7 -> N16 [label="Çakışıyor\n(Kayit Geçerli=Hata)"];
    N7 -> N8 [label="Çakışmıyor"];
    
    // Kredi Hesaplama
    N8 -> N3 [label="Döngüye Devam Et"]; // Döngü tekrarı

    // Döngü Sonu ve Kredi Limit Kontrolü
    N3 -> N9 [label="Tüm Dersler Bitti"];
    N9 -> N10;

    // Toplam Kredi Kontrolü
    N10 -> N17 [label="Evet"];
    N10 -> N11 [label="Hayır"];

    // Sonuç Kontrolü
    N11 -> N12 [label="Geçerli=Evet"];
    N11 -> N18 [label="Geçerli=Hayır"];

    // Hata akışları tekrar N18'e yönlendirebilir veya direkt sona gidebilir. Basitlik için sona gitsin.
    N13 -> N19;
    N14 -> N9 [label="Döngüye Devam Et"]; // Hatadan sonra da döngü devam edebilir.
    N15 -> N9 [label="Döngüye Devam Et"];
    N16 -> N9 [label="Döngüye Devam Et"];
    N17 -> N18;

    N12 -> N19;
    N18 -> N19;

    // Görsel gruplama (optional)
    subgraph cluster_loop {
        label = "Dersler Üzerindeki Döngü";
        style = rounded;
        N3; N4; N5; N6; N7; N8;
    }
}
