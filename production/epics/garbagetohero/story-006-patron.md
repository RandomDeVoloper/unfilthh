# Story 006: Patron (tek oda, iki faz)

> **Epic**: garbagetohero
> **Status**: Ready
> **Layer**: N/A (minimal)
> **Type**: Logic
> **Estimate**: L
> **Manifest Version**: N/A (minimal — no control manifest)
> **Last Updated**: (set by /dev-story when implementation begins)

## Context

**GDD**: `design/game-brief.md`
**Requirement**: Brief MVP feature 6

**ADR Governing Implementation**: N/A (minimal — no ADRs)
**ADR Decision Summary**: N/A (minimal — no ADRs)
**ADR Version**: N/A (minimal — no ADRs)

**Engine**: Unreal 5.8 | **Risk**: HIGH
**Engine Notes**: none (no ADR engine-compatibility analysis at minimal)

**Control Manifest Rules (this layer)**: N/A (minimal — no control manifest)

---

## Acceptance Criteria

- [ ] Patron odası tek odadır; içinde yalnızca patron vardır
- [ ] Faz 1: temel saldırı seti çalışır
- [ ] Faz 2: patron HP eşiğini geçince farklı/daha güçlü saldırı seti başlar
- [ ] Faz geçişi oyuncuya belli olur (basit görsel veya ses ipucu)
- [ ] Patron öldürülünce oyun "kazandın" durumuna geçer (win state)
- [ ] Oyuncu patron odasında ölürse koşu baştan başlar (fail state)
- [ ] Patron değerleri (HP, hasar, faz eşiği) veriden ayarlanabilir
- [ ] Patron odasına geçiş, önceki odalar temizlenmeden açılmaz

---

## Implementation Notes

- Faz eşiği ör. %50 HP; değer veriden gelsin, balans cila fazında oturur.
- `BP_Enemy_Base` (story 002) üzerine kurulur.

---

## Out of Scope

- Hikaye / diyalog (brief'te kapsam dışı)
- Ek patron veya mini patron

---

## Files

- `Content/Characters/Enemies/Blueprints/BP_Boss`
- `Content/Levels/World/Industrial/` (patron odası haritası)
- `Content/Blueprints/DataTables/DT_Boss`
- `tests/unit/enemies/patron_test.md`

---

## QA Test Cases

*N/A — no qa-lead specs at this tier; implement against the Acceptance Criteria above*

---

## Test Evidence

**Story Type**: Logic
**Required evidence**:
- `tests/unit/enemies/patron_test.md` (waived/advisory at `qa.level: minimal`)

**Status**: [ ] Not yet created

---

## Dependencies

- Depends on: `story-002-ciopcu-dusmanlar.md`
- Unlocks: None
