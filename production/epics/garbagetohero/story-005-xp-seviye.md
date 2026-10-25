# Story 005: Silah XP, seviye ve istatistik artışı

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: M
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 3

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Düşman öldürmek aktif silaha XP kazandırır
- [ ] XP eşiği dolunca silah seviye atlar
- [ ] Seviye tavanı 3; tavanda XP seviye artırmaz ve taşma olmaz
- [ ] Seviye atlayınca silahın istatistikleri artar (Tava: hasar +, alan saldırısı)
- [ ] XP ve seviye HUD'da görünür
- [ ] XP/seviye silaha bağlıdır; silah kırılınca kaybolur
- [ ] Eşikler ve istatistik artışları veriden ayarlanabilir

---

## Implementation Notes

- XP verme: düşman ölüm olayına bağlanır (story 002).
- Her silahın kendi XP/seviye durumu; story 007'de üç silah için tekrar kullanılır.

---

## Out of Scope

- Story 007: Şemsiye/Terlik'e özel artışlar
- Yetenek ağacı (brief'te kapsam dışı)

---

## Files

- `Content/Blueprints/Components/BP_LevelComponent`
- `Content/Blueprints/DataTables/DT_WeaponLevels`
- `tests/unit/weapons/xp_seviye_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/weapons/xp_seviye_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `story-001-hareket-kamera-tava.md`, `story-002-ciopcu-dusmanlar.md`
- Unlocks: `story-007-semsiye-terlik.md`
