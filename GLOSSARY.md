# UNFILTH — Glossary

## Debris
A physics-simulated object (bottle, box, barrel, tire, metal scrap) that litters a Zone. The player clears it by hitting it with water force until it exits the Zone boundary. Each Debris type is configured via a DataTable row (mesh, mass, sound, loot table).

## Zone
A trigger volume that tracks a set of Debris within a distinct area of a Bölüm. Owns a cleanup percentage (remaining / initial count). Fires a "zone clear" event when all Debris are resolved.

## Bölüm
One of the four playable sections of the Vertical Slice (Sokak, Arka Sokak, Çöplük, Boss Alan). Contains one or more Zones. Has its own pacing goal (tutorial, depth, mastery, climax).

## Mahalle
The single neighbourhood that contains all four Bölüm in the Vertical Slice. The highest-level spatial container.

## Dar Jet
Water hose mode 1. High-pressure, narrow beam. Implemented as a line trace (~20m range) + impulse on hit. Primary mode for precision and force. Full DOOM-level exaggerated physics: lightweight Debris flies 15m+, heavy Debris tumbles and rolls.

## Impulse
The physics force applied to Debris when hit by water. Scaled by Debris Mass — lighter objects react dramatically, heavier objects slide/topple. Tuned for over-the-top satisfaction, not realism.

## Geniş Spray
Water hose mode 2. Low-pressure, wide area. Implemented as a sphere trace (~2.5m radius) + radial force. Pushes 3-4 objects at once — the "cluster sweep" tool. Lower per-object force than Dar Jet. (Week 2)

## Köpük Bombası
Water hose mode 3. Fire-and-forget area denial. Player shoots a foam blob that sticks to a surface, then applies sustained radial force to nearby Debris for 2-3 seconds before dissipating. Strategic placement enables mega-chains. The "place it, step back, watch the explosion" TikTok moment. (Week 2)

## Playtest Checkpoint
Week 3 go/no-go gate. Hybrid evaluation: quantitative metrics (play duration, longest chain, cleanup %) collected via minimal UE analytics + qualitative observation of 3-5 players. The question answered: "Is the core loop fun?"

## Zincirleme Reaksiyon
A chain reaction triggered when Debris impacts other Debris after being hit by water. Uses hybrid physics: UE Chaos handles the base simulation, but each Debris→Debris collision injects a Chain Boost impulse so chains sustain through 5-10+ objects instead of dying at 2-3. The domino/cascade effect that differentiates UNFILTH from PowerWash Simulator.

## Chain Boost
An extra impulse injected on Debris→Debris collision events to prevent chain reactions from damping out too quickly. Tuned via a single multiplier parameter during playtesting.

## Cluster
A hand-placed group of 3-5 Debris arranged for chain reaction potential. Each Cluster has a designed "trigger point" — the Debris the player should hit first. Types: şişe yığını (lightweight storm), varil sırası (domino), kutu piramidi (dramatic collapse).

## Combo
A multiplier that increases with each Debris clearance. Resets on a 5-second timer (base) since the last Debris exit from the Zone. If the Combo breaks, gold-tier Loot is permanently locked in that Zone — one chance per Zone, creating high-risk/high-reward tension. Timer extended by Combo Süresi +1s upgrade. Chain reactions keep the timer alive — strategic hit order sustains the Combo.

## Combo Timer
5-second countdown (base) that resets each time a Debris exits its Zone. When it hits zero, the Combo breaks. Chain Boost collisions that eject Debris reset the timer, rewarding well-planned chain reactions.

## Loot
Valuable items physically hidden beneath Cluster Debris. When a Cluster is scattered, loot actors become visible and are picked up automatically via magnetic pull when the player is nearby (range extendable via Loot Mıknatısı upgrade). Each loot type converts instantly to Coin on pickup (bozuk para: 1, telefon: 5, altın diş: 20, mücevher: 50). Gold-tier loot is locked out permanently if the Zone's Combo breaks.

## Coin
The single currency. Earned from Loot pickups + Skorboard combo bonus (peak combo × 1). Spent on Upgrades. Total across 4 Bölüm: ~300-350 base + ~30-50 combo bonus. Distributed escalating (Bölüm 1: ~40, Bölüm 2: ~70, Bölüm 3: ~100, Bölüm 4: ~120 base). Total upgrade cost: 405 — player affords 3 upgrades comfortably, 4th requires near-perfect play with combo bonuses. Creates meaningful build decisions and replay motivation.

## Temizlenme (Clearance)
A Debris is "cleared" when it physically exits its Zone boundary. In Week 1 prototype no despawn — Debris stays where it lands, only the Zone counter updates. Fade-out visual added during polish (Week 6-8).

## Boss Alan
Bölüm 4, the climax. A countdown timer (5 minutes) creates pressure — clear all Debris before time runs out. Chain reactions are mandatory to finish in time. On failure: instant retry prompt ("Süre doldu! Tekrar dene?"), low friction. Contains a mega-Cluster that requires strategic Köpük Bombası placement. Debris pushback applies here too — player gets knocked back by flying Debris, adding physical chaos to the time pressure.

## Sleep (Debris)
Aggressive sleep policy: Debris idle for 1 second enters physics sleep. Wakes on water hit or Debris→Debris collision. Prioritizes performance over perfectly natural settling — acceptable trade-off for maintaining 60 FPS target with 50 simultaneous physics objects.

## Bölüm Arası Kayıt
Auto-save between Bölüm transitions. Triggers after Upgrade purchase at the Skorboard. Player can quit and resume from the last completed Bölüm with all Upgrades intact. No manual save, no mid-Bölüm save.

## Dinamik Müzik
Layered music system tied to Combo level. Base = bass loop only (combo 0), drums layer added at combo 5+, synth at 10+, full synthwave at 15+. Implemented as 4 audio layers with volume crossfade driven by combo value. Fallback if time runs short: single loop.

## Before/After Reveal
End-of-Bölüm split-screen transition. A SceneCapture2D screenshot is taken at level start (dirty state). At section end, the screen shows dirty snapshot on left / live clean render on right, with a sliding wipe from center. The TikTok-viral "left dirty / right clean" format.

## Skorboard
Displayed after the Before/After Reveal at the end of each Bölüm. Shows cleanup %, Coin earned, combo stats, longest chain. Below the stats: Upgrade purchase options. Flow: Reveal → Skorboard → Upgrade → next Bölüm auto-transition.

## Loading Screen
PowerWash Simulator-style control tutorial shown during level loads. Displays all controls (WASD, mouse, left click, scroll, etc.) so no in-game tooltip is needed for basic movement. In-game tooltips reserved for game-specific concepts only: (1) first cleanup → "Zone Dışına It!", (2) first loot → coin popup.

## Fragman
Dual-format trailer. 45s structured version for Steam page (establishing shot → first spray → escalating montage → mega-chain → before/after reveal → logo + Wishlist CTA). 15s TikTok/Shorts hook clip (single mega-chain + reveal). Same footage, two cuts, two marketing channels.

## Upgrade
A permanent enhancement purchased with Coin at the Skorboard. 4 active nodes in the Vertical Slice: Su Basıncı +1 (75 coin), Köpük Bombası Aç (150 coin), Loot Mıknatısı (80 coin), Combo Süresi +1s (100 coin). Total cost: 405 coin. Tank Kapasitesi is listed but greyed out / "Coming Soon" — no tank mechanic in VS, water is unlimited. All upgrades available from the start (no unlock gates), player chooses based on coin budget. Once bought, persists through all remaining Bölüm.

## Loot Mıknatısı
Upgrade that extends the automatic magnetic Loot pickup range from 3m (base) to 8m. Base range = "walk near it", upgraded = "pulls from across the room". Keeps flow unbroken.

## Köpük Bombası Aç
Upgrade that unlocks the third water mode. Player starts with only Dar Jet + Geniş Spray. Purchasing this (150 coin, typically affordable by end of Bölüm 2-3) adds Köpük Bombası to the scroll cycle — a progression peak that gives the player a new tool for the harder Bölüm.

## Kir Mask
Phase 2 surface dirt system (Week 4-5, conditional on Week 3 playtest). World-space render target covering the entire level. Water hits draw circles on the RT, eroding the mask. Visual style: tonal shift — dirty = desaturated + dark, clean = vibrant + bright. Single lerp parameter, zero extra textures. Runs in parallel with Debris physics — water simultaneously applies impulse to Debris AND erodes mask on hit surfaces. Mask cleanup is visual satisfaction only — Zone completion is still Debris-based. Mask % shown on Skorboard as bonus stat, not a gate.
