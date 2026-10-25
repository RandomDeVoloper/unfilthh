# Story 001: Hareket, top-down kamera ve Tava saldırısı

> **Epic**: garbagetohero
> **Status**: In Progress
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: L
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: 2026-10-25

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 1

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Oyuncu klavye/gamepad ile hareket eder; karakter hareket yönüne döner
- [ ] Kamera sabit top-down (45°), oyuncuyu takip eder, karakter her zaman görünür
- [ ] Saldırı girdisi Tava'yı yakın menzilde sallar; saldırılar arasında bekleme süresi var
- [ ] Tava menzilindeki hedefe hasar verir, menzil dışındakine vermez
- [ ] Her hasar vuruşunda hasar sayacı artar (ekranda veya log'da görünür)
- [ ] Oyuncunun HP'si var; 0'a düşünce ölür ve koşu baştan başlar (fail state)
- [ ] Oyuncu Tava ile başlar (elde 1 silah)
- [ ] Girdiler Enhanced Input ile bağlı (Input Mapping Context)

---

## Implementation Notes

- Blueprint-only; kamera sabit açı, spring arm veya doğrudan kamera aktörü.
- UE 5.8 Enhanced Input API'si değişmiş olabilir: `docs/engine-reference/unreal/` doğrula.
- Silah mantığı ayrı Blueprint'te tutulsun, story 003/005/007 aynı yapıyı genişletecek.

---

## Out of Scope

- Story 002: düşmanlar
- Story 003: aşınma
- Story 005: XP/seviye
- Story 007: Şemsiye ve Terlik

---

## Files

- `tests/unit/combat/hareket_kamera_tava_test.md` — oluşturuldu (564 satır): Blueprint uygulama kılavuzu + manuel QA kontrol listesi
- `Content/Characters/Hero/Blueprints/BP_Hero` — **bekliyor** (Unreal Editor'de oluşturulacak)
- `Content/Blueprints/Actors/BP_Weapon_Tava` — **bekliyor** (Unreal Editor'de oluşturulacak)
- `Content/_Core/GameModes/BP_GarbageGameMode` — **bekliyor** (Unreal Editor'de oluşturulacak)

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/combat/hareket_kamera_tava_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: None
- Unlocks: `production/epics/garbagetohero/story-002-ciopcu-dusmanlar.md`, `story-003-asinma-kirilma.md`, `story-005-xp-seviye.md`, `story-007-semsiye-terlik.md`
