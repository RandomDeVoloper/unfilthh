# Epic: garbagetohero

> **Source**: `design/game-brief.md` (minimal tier — no GDD/ADR)
> **Status**: Ready

## Goal
Çöpten bulduğun silahlar düşman öldürdükçe güçlenir, aşındıkça kırılır; hurda malzemeyle onarıp patrona kadar hayatta kalırsın.

## Scope (brief MVP)
- Hareket + sabit top-down kamera + Tava saldırısı
- Sıradan çöpçü + ağır çöpçü
- Silah XP → seviye (tavan 3) → istatistik artışı
- Aşınma → kırılma
- Malzeme düşüşü + Konteyner "Onar"
- Patron (tek oda, iki faz)
- Şemsiye ve Terlik (3 silah)

## Order
Brief Build order: savaş çekirdeği → aşınma/kırılma/malzeme → konteyner → XP → ağır çöpçü/patron/ek silahlar → cila.
Story 002'de ağır çöpçü en sona bırakılabilir.

## Stories

| # | Story | Type | Status | ADR |
|---|-------|------|--------|-----|
| 001 | Hareket, top-down kamera ve Tava saldırısı | Logic | Ready | N/A |
| 002 | Sıradan ve ağır çöpçü düşmanlar | Logic | Ready | N/A |
| 003 | Silah aşınması ve kırılması | Logic | Ready | N/A |
| 004 | Malzeme düşüşü ve Konteyner "Onar" | Integration | Ready | N/A |
| 005 | Silah XP, seviye ve istatistik artışı | Logic | Ready | N/A |
| 006 | Patron (tek oda, iki faz) | Logic | Ready | N/A |
| 007 | Şemsiye ve Terlik silahları | Logic | Ready | N/A |
