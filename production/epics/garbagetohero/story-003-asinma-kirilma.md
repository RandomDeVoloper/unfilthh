# Story 003: Silah aşınması ve kırılması

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: M
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 4

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Her silahın aşınma değeri var; yeni silah tam değerle başlar
- [ ] Silah kullanıldıkça aşınma azalır (her vuruşta sabit miktar)
- [ ] Aşınma değeri oyuncuya görünür (HUD veya log)
- [ ] Aşınma 0'a düşünce silah kırılır ve elden kaybolur
- [ ] Kırılma olayı bir event dispatcher ile duyurulur (diğer sistemler dinleyebilir)
- [ ] Elde silah kalmazsa oyun çökmeden çalışır (saldırı devre dışı, oyuncu hareket edebilir)
- [ ] Aşınmayı sıfırlayan `RepairWeapon` işlevi var (story 004 kullanır)
- [ ] Aşınma azalma miktarı veriden ayarlanabilir

---

## Implementation Notes

- Brief "elde silah kalmazsa" durumunu tanımlamıyor; varsayım: saldırı devre dışı. Gerekirse brief'e geri yaz.
- Aşınma, silah Blueprint'inin parçası; story 001'deki silah yapısı genişletilir.

---

## Out of Scope

- Story 004: malzeme ve onarım arayüzü
- Story 005: XP/seviye

---

## Files

- `Content/Blueprints/Actors/BP_Weapon_Tava`
- `Content/Blueprints/Components/BP_WearComponent`
- `tests/unit/weapons/asinma_kirilma_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/weapons/asinma_kirilma_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `story-001-hareket-kamera-tava.md`, `story-002-ciopcu-dusmanlar.md`
- Unlocks: `story-004-malzeme-konteyner.md`, `story-007-semsiye-terlik.md`
