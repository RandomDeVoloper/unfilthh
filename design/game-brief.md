# Game Brief: GarbageToHero

**One-sentence pitch:** Çöpten bulduğun silahlar düşman öldürdükçe güçlenir, aşındıkça kırılır; hurda malzemeyle onarıp patrona kadar hayatta kalırsın.

## Core loop
- Odaya gir → düşmanla savaş (yakın/orta menzil)
- Düşman öl → silah XP + malzeme düşer → silah seviye atlar (tavan 3)
- Silah hasar alır → aşınma artar; 0'a düşerse silah kırılır, kaybolur
- Konteynerde (güvenli oda) malzemeyle "Onar" → aşınma sıfırlanır

## Player goal & fail state — what "working" looks like
- Hedef: patronu öldür. Fail: karakter ölürse baştan (roguelike).
- Silah elde en fazla 3; onarılmayan silah kırılıp kaybolur.

## MVP — what must exist to be the game
- Hareket + sabit top-down kamera (45°) + Tava saldırısı
- Sıradan çöpçü + ağır çöpçü
- Silah XP → seviye (tavan 3) → istatistik artışı
- Aşınma → kırılma
- Malzeme düşüşü + Konteyner "Onar" düğmesi (kapı her zaman açık, düşman giremez)
- Patron (tek oda, iki faz)
- Şemsiye ve Terlik (3 silah)

## Out of scope — not building this
- Hikaye / diyalog, silah kombinasyonu / crafting, prosedürel harita (sabit düzey), çok oyunculu, yetenek ağacı

## Build order
1. Savaş çekirdeği: Tava ile düşman öldür, hasar sayacı
2. Aşınma + kırılma + malzeme düşüşü (oyunun yeni fikri, önce kanıtla)
3. Konteyner: Onar düğmesi + kısılamaz kapı
4. XP → seviye → istatistik
5. Ağır çöpçü, patron, Şemsiye/Terlik
6. Cila: ses, efekt, balans, paketlenmiş build

---
**Who it's for / what they feel:** Kısa oturumlu aksiyon severler; elindeki en kötü silahı idare ederek hayatta kalma hissi.

**Art & audio direction:** Çöp/hurda estetiği, static baked ışık; ses sonra.

**Reference game:** Hades — odadan odaya aksiyon döngüsü, meta-progression yok.

## Teknik notlar
Blueprint-only, Lumen/VSM/ray tracing kapalı, static baked ışık, Git LFS (`*.uasset`, `*.umap`, `*.fbx`, `*.png`, `*.tga`).

## Açık sorular
Jam bitiş tarihi? Mezuniyet teslim tarihi? Kaç oda?
