# Story 004: Malzeme düşüşü ve Konteyner "Onar"

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Integration
> **Estimate**: M
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 5

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Düşman ölünce malzeme düşer; miktar düşman tipine göre değişir
- [ ] Oyuncu malzemeyi toplar; toplam sayı HUD'da görünür
- [ ] Oda temizlenince oyuncu konteynere geçebilir
- [ ] Konteyner her zaman açık; düşman içeri giremez ("kısılamaz kapı")
- [ ] Konteynerde "Onar" düğmesi seçili silahın aşınmasını sıfırlar ve malzeme harcar
- [ ] Yeterli malzeme yoksa "Onar" kullanılamaz ve oyuncuya belirtilir
- [ ] Kırılıp kaybolmuş silah onarılamaz
- [ ] Onarım maliyeti veriden ayarlanabilir

---

## Implementation Notes

- Konteyner ayrı oda/bölge; düşman giremez (collision veya AI kısıtı).
- Tek eylem "Onar"; başka konteyner özelliği yok (brief kapsamı).
- `RepairWeapon` story 003'te tanımlı.

---

## Out of Scope

- Story 003: aşınma kuralları
- Konteynerde başka eylem (satın alma, yükseltme)

---

## Files

- `Content/Blueprints/Actors/BP_MaterialPickup`
- `Content/Blueprints/Actors/BP_Container`
- `Content/UI/Widgets/Gameplay/WBP_RepairPanel`
- `tests/integration/economy/onar_dongusu_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Integration
**Required evidence**:
- `tests/integration/economy/onar_dongusu_test.md` OR playtest doc (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `story-002-ciopcu-dusmanlar.md`, `story-003-asinma-kirilma.md`
- Unlocks: None
