# Story 007: Şemsiye ve Terlik silahları

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: M
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 7

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Şemsiye: yakın/orta menzil dürme atağı; bloklarken gelen hasar azalır
- [ ] Terlik: fırlatma silahı; geri sekme yapar, seviyeyle hızı artar
- [ ] Elde en fazla 3 silah taşınır; 4. silah alınamaz
- [ ] Oyuncu silahlar arasında geçiş yapar (girdi bağlı)
- [ ] Her silahın kendi aşınma, XP ve seviye (tavan 3) durumu vardır
- [ ] Silahlar odada yerden alınır (etkileşim)
- [ ] Silah değişiminde saldırı bekleme süresi ve durum tutarlı kalır

---

## Implementation Notes

- Tava, aşınma (003) ve XP (005) yapısı yeniden kullanılır; yeni silah = yeni Blueprint + veri satırı.
- Silah slotları basit dizi (max 3).

---

## Out of Scope

- Silah kombinasyonu / crafting (brief'te kapsam dışı)
- Dördüncü ve sonraki silahlar

---

## Files

- `Content/Blueprints/Actors/BP_Weapon_Semsiye`
- `Content/Blueprints/Actors/BP_Weapon_Terlik`
- `Content/Blueprints/Components/BP_InventoryComponent`
- `tests/unit/weapons/semsiye_terlik_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/weapons/semsiye_terlik_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `story-001-hareket-kamera-tava.md`, `story-003-asinma-kirilma.md`, `story-005-xp-seviye.md`
- Unlocks: None
