# Story 002: Sıradan ve ağır çöpçü düşmanlar

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: M
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 2

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Sıradan çöpçü: az HP, oyuncuya yürür, yakın saldırı yapar (öğretici düşman)
- [ ] Ağır çöpçü: çok HP, yavaş hareket, alan kontrolü yapan saldırı
- [ ] Oyuncu vuruşu düşman HP'sini düşürür; 0'da düşman ölür ve sahneden kalkar
- [ ] Düşman oyuncuya hasar verir; saldırılar arasında bekleme süresi var
- [ ] Aynı odada birden fazla düşman aynı anda çalışır
- [ ] Odadaki tüm düşmanlar ölünce "oda temizlendi" durumu tetiklenir
- [ ] Düşman değerleri (HP, hız, hasar) veriden okunur (DataTable/DataAsset)

---

## Implementation Notes

- Basit AI: oyuncuya MoveTo + menzilde saldırı. Behavior tree gerekmez, Blueprint state yeterli.
- Ortak taban Blueprint (`BP_Enemy_Base`), iki alt sınıf.
- Ağır çöpçü brief'in Build order'ında sonda; bu story'nin sonuna bırakılabilir.

---

## Out of Scope

- Story 004: malzeme düşüşü
- Story 005: XP kazanımı
- Story 006: patron

---

## Files

- `Content/Characters/Enemies/Blueprints/BP_Enemy_Base`
- `Content/Characters/Enemies/Blueprints/BP_Enemy_Cheap`
- `Content/Characters/Enemies/Blueprints/BP_Enemy_Heavy`
- `Content/Blueprints/DataTables/DT_Enemies`
- `tests/unit/enemies/ciopcu_dusmanlar_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/enemies/ciopcu_dusmanlar_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `production/epics/garbagetohero/story-001-hareket-kamera-tava.md`
- Unlocks: `story-003-asinma-kirilma.md`, `story-004-malzeme-konteyner.md`, `story-005-xp-seviye.md`, `story-006-patron.md`
