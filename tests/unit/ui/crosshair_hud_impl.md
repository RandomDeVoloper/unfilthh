# Crosshair HUD + Sleep Policy (1s) — Uygulama Kılavuzu

**Mimari kısıtlar:** Blueprint-only, Cast yok, Tick yok, Interface/Event Dispatcher ile iletişim.

---

## 1. WBP_Crosshair Oluştur

**Content Browser → /Game/Blueprints/UI/ → Sağ tık → User Interface → Widget Blueprint**
İsim: `WBP_Crosshair`

### Designer sekmesi — Widget hiyerarşisi

```
[Canvas Panel]
  └── [Overlay] — Anchors: center (0.5, 0.5), Position: (0,0), Size: (30,30), Alignment: (0.5,0.5)
        ├── [Image] — yatay çizgi  Size: (20,2)  Color: (1,1,1,1)
        └── [Image] — dikey çizgi  Size: (2,20)  Color: (1,1,1,1)
```

> Alternatif: tek bir Image widget, 30x30, arka plan texture yok — yalnızca sınır çizgisi için
> Border kullan: `Brush Color: white, Draw As: Image, Tint: (1,1,1,0.8)`.
> En hızlı yol: **Overlay** içine iki Image widget, her biri için `Color and Opacity → (1,1,1,0.85)`.

### Graph sekmesi — Değişkenler

| Ad | Tür | Varsayılan |
|----|-----|-----------|
| `CrosshairWidget` | (otomatik, self) | — |

### Graph sekmesi — Custom Events & Functions

#### `ShowCrosshair` (Custom Event)
```
ShowCrosshair
  → SetVisibility (self) → Visibility: Visible
  → ClearTimerByFunctionName (self, FunctionName: "HideCrosshair")
```

#### `StartHideTimer` (Custom Event)
```
StartHideTimer
  → SetTimerByFunctionName (self, FunctionName: "HideCrosshair", Time: 1.0, Looping: false)
```

#### `HideCrosshair` (Custom Event — timer hedefi)
```
HideCrosshair
  → SetVisibility (self) → Visibility: Collapsed
```

---

## 2. BP_FirstPersonCharacter — HUD Kurulumu

**Değişken Ekle:**
| Ad | Tür |
|----|-----|
| `CrosshairWidget` | WBP_Crosshair (Object Reference) |

### EventBeginPlay
```
EventBeginPlay
  → CreateWidget (Class: WBP_Crosshair, Owning Player: GetOwningPlayerController)
    → (Return Value) → SET CrosshairWidget
  → AddToViewport (CrosshairWidget)
  → ShowCrosshair (CrosshairWidget)   ← başlangıçta görünür
```

### EventStartFiring bağlantısı (BP_WaterGun Event Dispatcher)

**Not:** BP_WaterGun'daki `EventStartFiring` ve `EventStopFiring` zaten Custom Event.
Bunları Event Dispatcher'a çevir veya aşağıdaki yaklaşımı kullan:

**BP_FirstPersonCharacter EventGraph'a ekle:**
```
Event StartFiring (Custom Event — WaterGun'dan gelen mesajı yakalar)
  → GET CrosshairWidget
  → ShowCrosshair (CrosshairWidget)
```

```
Event StopFiring (Custom Event)
  → GET CrosshairWidget
  → StartHideTimer (CrosshairWidget)
```

**BP_WaterGun'da değişiklik:**
- `EventStartFiring` sonuna: `CallOnStartFiring (self)` → Character'ı bulmak için
  `GetPlayerCharacter → Cast` **yasak**. Bunun yerine:

**Tercih edilen yaklaşım — Event Dispatcher:**
1. BP_WaterGun'a Event Dispatcher ekle: `OnFiringStarted`, `OnFiringStopped`
2. `EventStartFiring` → `OnFiringStarted` (Call)
3. `EventStopFiring` → `OnFiringStopped` (Call)
4. BP_FirstPersonCharacter'da BeginPlay'de:
   - `GET WaterGunComp` (veya weapon spawn edilmişse `GetChildActor`)
   - `Bind Event to OnFiringStarted` → `ShowCrosshair` çağrısı
   - `Bind Event to OnFiringStopped` → `StartHideTimer` çağrısı

---

## 3. Sleep Policy Özeti

```
Firing başladı → ShowCrosshair (görünür + mevcut timer iptal)
Firing durdu  → StartHideTimer (1s sonra HideCrosshair tetiklenir)
Firing 1s içinde yeniden başladı → ShowCrosshair timer'ı iptal eder, crosshair kalır
```

**Neden ClearTimer gerekli:** Oyuncu hızlıca bırakıp tekrar basarsa timer sıfırlanmalı.

---

## 4. Doğrulama

PIE'de:
- [ ] Oyun başladığında crosshair ekranda görünüyor
- [ ] Sol tık basılı tutarken crosshair görünüyor
- [ ] Sol tık bırakıldıktan 1 saniye sonra crosshair kayboluyor
- [ ] 1 saniye dolmadan tekrar ateş edilirse crosshair kaybolmuyor
