# Story 001 - Movement, Top-Down Camera and Tava Attack: Blueprint Build Guide and Manual QA

> **Story**: `production/epics/garbagetohero/story-001-hareket-kamera-tava.md`
> **Design record**: `design/game-brief.md` (MVP feature 1) - no GDD/ADR at `workflow: minimal`
> **Engine**: Unreal Engine 5.8, Blueprint only (no C++)
> **Evidence level**: `qa.level: minimal` - this file is the advisory test evidence. Automated tests are waived.
> **Status**: Guide written. Blueprints NOT yet created (they are binary `.uasset` files and must be made in the editor).

Engine API note: `docs/engine-reference/unreal/modules/input.md` was written against 5.7 plus the 5.8 change notes, and `VERSION.md` says "Installed at pin time: NOT DETERMINED". Node names below are the standard Blueprint names for Enhanced Input. If a node is named differently in your installed editor, search by the name in bold and note the difference at the bottom of this file. Treat any such difference as unverified until recorded.

---

## 0. Read This First

### 0.1 Deviations from the written brief (flagged for review)

These are technical corrections. None changes the game design. Each is marked **[DEV-n]** where it applies.

| ID | Brief said | This guide does | Why |
|----|-----------|-----------------|-----|
| DEV-1 | IA_Move: AddMovementInput with *Actor* Forward/Right vector | Uses fixed **World** X and Y directions | `OrientRotationToMovement` rotates the actor toward its movement. If movement is then defined relative to the actor's own forward vector, input feeds back into itself and the character circles. A fixed top-down camera needs world-relative movement anyway. |
| DEV-2 | SpringArm Rotation = -45 pitch | Same, plus **Use Absolute Rotation = true** on the SpringArm | A SpringArm attached under the character inherits the character's yaw. Without absolute rotation the camera swings around every time the character turns. |
| DEV-3 | "has TakeDamage event" check, then call a custom `TakeDamage` | Uses the built-in **Apply Damage** node and **Event AnyDamage** receiver | Blueprint has no way to ask "does this actor have a custom event named X" without an interface. Apply Damage / AnyDamage is the engine-standard route, needs no cast or interface, and returns the damage actually applied, which is used to decide whether a hit counts. Story 002 enemies only need to implement Event AnyDamage. |
| DEV-3b | DamageCounter increments for every overlapping actor that is not self | Counter increments only when Apply Damage returns > 0 (excludes the wielder, and zero-damage cases) | Caveat: the engine's default Actor damage handling returns the damage value even for actors with no handler, so this check alone does not filter scenery. If props inflate the counter, add an actor tag `Damageable` and test it (see 4.3). |
| DEV-4 | Sphere radius 120 set on the component and `AttackRange` variable 120 | Radius is set from `AttackRange` at BeginPlay | One source of truth so tuning `AttackRange` really changes the range. |
| DEV-5 | Weapon attached to socket "hand_r" or root | Default: attach to the Capsule (root). `hand_r` is an optional step | The placeholder hero has no skeleton or socket. If a socket name does not exist, the actor silently attaches at the component origin. |
| DEV-6 | n/a | Adds a temporary debug key (H) on BP_Hero that damages the hero | AC-6 cannot be tested otherwise until Story 002 adds enemies. Remove it after QA. |

### 0.2 Data-driven note

All tunables are Blueprint variables with **Instance Editable** ticked and a default value, so they can be changed per level instance without opening the graph. Moving them to a Data Asset or table is deferred. Raise it with lead-programmer if designers need a single balance file before Story 003/005.

### 0.3 Out of scope (do not build)

Enemies (002), weapon degradation (003), XP/level (005), Umbrella/Slipper (007). Keep the weapon logic in its own Blueprint so those stories extend the same structure.

### 0.4 Build order

1. Section 1 Input assets
2. Section 4 BP_Weapon_Tava (BP_Hero references it)
3. Section 3 BP_Hero
4. Section 2 BP_GarbageGameMode
5. Section 6 Level
6. Section 7 QA

Create folders first in the Content Browser:
`Content/_Core/Input`, `Content/_Core/GameModes`, `Content/Characters/Hero/Blueprints`, `Content/Blueprints/Actors`, `Content/Blueprints/Actors/Debug`.

### 0.5 One-time Project Settings check

`Edit > Project Settings > Engine > Input > Default Classes`

- **Default Player Input Class** = `EnhancedPlayerInput`
- **Default Input Component Class** = `EnhancedInputComponent`

Both are the default in UE5. Confirm only. Do not enable or reference the Raw Input plugin (deprecated in 5.8). If you need a Windows gamepad backend choice, that is a separate task.

---

## 1. Input Data Assets (AC-8)

All three are created via Content Browser: right-click in the folder > `Input` > `Input Action` or `Input Mapping Context`.

### 1.1 `Content/_Core/Input/IA_Move`

1. Right-click in `Content/_Core/Input` > Input > **Input Action**. Name `IA_Move`.
2. Open it. Set:
   - **Value Type** = `Axis2D (Vector2D)`
   - **Consume Input** = true (default)
   - **Triggers** = leave empty (the implicit Down trigger fires `Triggered` every tick while the value is non-zero)
   - **Modifiers** = leave empty (modifiers go on the mappings in the IMC)
3. Save.

### 1.2 `Content/_Core/Input/IA_Attack`

1. Create Input Action `IA_Attack`.
2. Set **Value Type** = `Digital (bool)`.
3. **Triggers** = leave empty. In BP_Hero use the **Started** output pin, which fires once on the press.
4. Save.

### 1.3 `Content/_Core/Input/IMC_Hero`

Create Input Mapping Context `IMC_Hero`. Open it and add the mappings below. In the **Mappings** array click `+`, pick the Input Action, then expand it and pick the key and modifiers.

**IA_Move mappings (5 entries: 4 keys + stick)**

For `Axis2D`, a single key supplies one axis value (1.0 on X). Modifiers remap the key to the right axis and sign.

| # | Key | Modifiers (in this order) | Resulting Vector2D |
|---|-----|---------------------------|--------------------|
| 1 | `W` | **Swizzle Input Axis Values** = `YXZ` | (0, +1) |
| 2 | `S` | **Swizzle Input Axis Values** = `YXZ`, then **Negate** (X, Y, Z all true) | (0, -1) |
| 3 | `A` | **Negate** (X true) | (-1, 0) |
| 4 | `D` | none | (+1, 0) |
| 5 | `Gamepad Left Thumbstick 2D-Axis` | **Dead Zone** (Lower 0.2, Upper 1.0, Type Radial) | (X, Y) as tilted |

Order matters for S: Swizzle first, then Negate. Reversing them negates the wrong axis.

Optional: add arrow keys the same way as WASD. Not required by the story.

**IA_Attack mappings**

| # | Key |
|---|-----|
| 6 | `Left Mouse Button` |
| 7 | `Gamepad Face Button Bottom` (South: A on Xbox, Cross on PlayStation) |

No modifiers or triggers.

Save the IMC. Total entries: 5 for IA_Move, 2 for IA_Attack.

**Section 1 done when**: three assets exist, IMC shows all 7 mappings, value types are Axis2D and Digital.

---

## 2. `BP_GarbageGameMode` (AC-7 support)

Path: `Content/_Core/GameModes/BP_GarbageGameMode`

1. Right-click > Blueprint Class > search and pick **Game Mode Base**. Name `BP_GarbageGameMode`.
2. Open > **Class Defaults** (toolbar) > Classes section:
   - **Default Pawn Class** = `BP_Hero` (create BP_Hero first, or come back to this)
   - **HUD Class** = leave at default (`HUD`)
   - **Player Controller Class** = leave at default
3. Compile and save. No graph logic.

---

## 3. `BP_Hero` (AC-1, 2, 6, 7, 8)

Path: `Content/Characters/Hero/Blueprints/BP_Hero`. Parent class: **Character**.

### 3.1 Components

Inherited (do not delete): `CapsuleComponent` (root), `Arrow`, `Mesh`, `CharacterMovement`.

Add:

**SpringArm** (Add > Spring Arm, parent = CapsuleComponent)

| Property | Value |
|----------|-------|
| Target Arm Length | `600.0` |
| Relative Rotation | Roll `0`, Pitch `-45`, Yaw `0` |
| **Use Absolute Rotation** (Transform section, the "Rotation" absolute toggle) **[DEV-2]** | `true` |
| Use Pawn Control Rotation | `false` |
| Inherit Pitch / Inherit Yaw / Inherit Roll | all `false` |
| Do Collision Test | `false` |
| Enable Camera Lag | `false` (optional later) |

**Camera** (Add > Camera, parent = SpringArm)

| Property | Value |
|----------|-------|
| Use Pawn Control Rotation | `false` |
| Relative Transform | leave at zero |
| Field of View | `90` (default) |

Camera yaw is 0, so it looks along world +X. On screen, world +X is "up", world +Y is "right". Section 3.3 depends on this.

**Visual placeholder (needed to verify facing in AC-1)**

The Mesh component is empty until you assign one. Do one of:
- Assign a skeletal mesh to `Mesh` (for example the Manny from a template, if you have it) and set its Relative Location Z = `-88`, Rotation Yaw = `-90`.
- Or add placeholders: Add > Static Mesh `HeroBody` (Engine Basic Shapes `Cylinder`, scaled to about 0.6, 0.6, 1.5) and Static Mesh `FacingMarker` (Basic Shape `Cone` or `Cube`, scale 0.3, positioned about 60 units in front along +X). The marker must be in front so rotation is visible. Enable `Show Engine Content` in the Content Browser view options to find Basic Shapes. Set both to **No Collision**.

### 3.2 Class Defaults and variables

**Class Defaults (Details when nothing selected)**

| Property | Value |
|----------|-------|
| Use Controller Rotation Yaw | `false` |
| Use Controller Rotation Pitch | `false` |
| Use Controller Rotation Roll | `false` |
| Auto Possess Player | `Disabled` (the Game Mode spawns and possesses) |

**CharacterMovement component**

| Property | Value |
|----------|-------|
| Max Walk Speed | `400.0` |
| Orient Rotation to Movement | `true` |
| Use Controller Desired Rotation | `false` |
| Rotation Rate | Yaw `720` (default 540 also fine) |

**Variables** (all Instance Editable, tick it for each)

| Name | Type | Default |
|------|------|---------|
| `MaxHP` | Float | `100.0` |
| `CurrentHP` | Float | `100.0` |
| `bIsDead` | Boolean | `false` |
| `WeaponRef` | Object Reference of `BP_Weapon_Tava` | none |
| `WeaponClass` | Class Reference of `BP_Weapon_Tava` | `BP_Weapon_Tava` |
| `RestartDelay` | Float | `1.0` |
| `InputMappingContext` | Object Reference of `InputMappingContext` | `IMC_Hero` |
| `IA_MoveRef` | Object Reference of `InputAction` | `IA_Move` |
| `IA_AttackRef` | Object Reference of `InputAction` | `IA_Attack` |

`WeaponClass` lets Stories 003/007 swap weapons without editing the graph. `CurrentHP` should initialise from `MaxHP` in BeginPlay (below) so a changed MaxHP applies.

Do not create the `IA_*Ref` variables if you prefer the Enhanced Input event nodes: in the Event Graph, right-click and search the action name (`IA_Move`) to place the event node directly. Those nodes reference the asset themselves. The `IA_*Ref` variables are optional and can be skipped.

### 3.3 Event Graph

**A. Event BeginPlay (AC-7, AC-8)**

```
Event BeginPlay
  -> Set CurrentHP = MaxHP
  -> Get Controller -> Cast To PlayerController
        (Cast Failed -> Print String "BP_Hero: no PlayerController at BeginPlay" and continue to the weapon step)
  -> Get Enhanced Input Local Player Subsystem   (input: the PlayerController from the cast)
  -> Is Valid? -> Add Mapping Context
        Mapping Context = IMC_Hero
        Priority = 0
  -> Spawn Actor from Class
        Class = WeaponClass (BP_Weapon_Tava)
        Spawn Transform = Get Actor Transform
        Collision Handling Override = Always Spawn, Ignore Collisions
        Owner = Self
        (Instigator is set from the pawn automatically; if the pin is exposed, connect Self)
  -> Attach Actor To Actor
        Target (the weapon) = return value of Spawn
        Parent Actor = Self
        Socket Name = None            (optional later: hand_r, see DEV-5)
        Location Rule / Rotation Rule / Scale Rule = Snap to Target / Snap to Target / Keep World
        Weld Simulated Bodies = false
  -> Set WeaponRef = return value of Spawn
```

If the mapping context is not added (no input in PIE): the pawn may not be possessed at BeginPlay in your setup. Fallback: perform the three Cast / Subsystem / Add Mapping Context nodes in **Event Possessed** (parent Pawn event, "New Controller" output) instead of BeginPlay, and keep the weapon spawn in BeginPlay. Note the fallback in the sign-off section if used.

**B. Enhanced Input Action IA_Move, pin Triggered (AC-1)** **[DEV-1]**

Right-click graph > search `IA_Move` > place the **Enhanced Input Action IA_Move** event.

```
IA_Move [Triggered]  (Action Value = Vector2D)
  -> Branch (Condition = NOT bIsDead)  -> True:
       Break Vector 2D (Action Value) -> X, Y
       Add Movement Input
           World Direction = Make Vector (1, 0, 0)
           Scale Value = Y
       Add Movement Input
           World Direction = Make Vector (0, 1, 0)
           Scale Value = X
```

Notes:
- Do not use `Get Actor Forward Vector` / `Get Actor Right Vector`. With Orient Rotation to Movement enabled this makes the character spiral. See DEV-1.
- Rotation is done by CharacterMovement (`Orient Rotation to Movement`). No Set Actor Rotation or MakeRotFromX node is needed.
- Calling Add Movement Input twice is correct. CharacterMovement clamps the combined input to magnitude 1, so diagonal movement is not faster.
- Enable **Consume Input** in the action's default is fine. If the character feels like it slides when released, check `Braking Deceleration Walking` (default 2048 is fine).

**C. Enhanced Input Action IA_Attack, pin Started (AC-3)**

```
IA_Attack [Started]
  -> Branch (Condition = NOT bIsDead)  -> True:
       Is Valid (WeaponRef) -> Valid:
           WeaponRef -> PerformAttack
```

**D. Event AnyDamage (hero takes damage) (AC-6)** **[DEV-3]**

Right-click > search `Event AnyDamage` (override of the Actor event, output `Damage`).

```
Event AnyDamage (Damage)
  -> Branch (Condition = bIsDead)  -> True: do nothing (return)
  -> False:
       Set CurrentHP = Clamp (CurrentHP - Damage, 0.0, MaxHP)
       Print String "Hero HP: " + CurrentHP    (Duration 2, Print to Log true)   <- keep until QA is signed off
       Branch (Condition = CurrentHP <= 0.0)
         -> True: Call Die
```

The `bIsDead` guard ensures Die runs exactly once when several hits arrive in the same frame.

**E. Custom Event: Die (AC-6)**

```
Custom Event Die
  -> Branch (Condition = bIsDead) -> True: return
  -> Set bIsDead = true
  -> Get Player Controller (index 0) -> Disable Input   (Target = Self, Player Controller = that controller)
  -> Character Movement -> Stop Movement Immediately
  -> Print String "Hero died - restarting"
  -> Delay (Duration = RestartDelay)
  -> Get Current Level Name (Remove Prefix String = true)
  -> Open Level (by Name)   Level Name = that result,  Absolute = true
```

Open Level reloads the level: pawn, weapon, HP and DamageCounter all reset. This is the run restart for the MVP fail state.

**F. Temporary debug: damage key (DEV-6)**

```
Keyboard H (Pressed)
  -> Apply Damage
       Damaged Actor = Self
       Base Damage = 25.0
       Event Instigator = None
       Damage Causer = Self
       Damage Type Class = DamageType
```

Mark with a Comment box "DEBUG - REMOVE AFTER STORY 001 QA". Delete it once AC-6 passes (Story 002 enemies replace it).

### 3.4 Compile checklist for BP_Hero

- [ ] Compile shows no errors or warnings
- [ ] SpringArm has Absolute Rotation checked, Pitch -45, length 600, collision test off
- [ ] Camera is a child of SpringArm, Use Pawn Control Rotation off
- [ ] Orient Rotation to Movement on, Use Controller Rotation Yaw off, Max Walk Speed 400
- [ ] Variables listed in 3.2 exist with the listed defaults

---

## 4. `BP_Weapon_Tava` (AC-3, 4, 5, 7)

Path: `Content/Blueprints/Actors/BP_Weapon_Tava`. Parent class: **Actor**.

Keep this Blueprint free of hero-specific logic, so Stories 003/005/007 can reuse the structure (a future parent class `BP_Weapon_Base` is a likely refactor; not done here).

### 4.1 Components

| Component | Settings |
|-----------|----------|
| `DefaultSceneRoot` (SceneComponent) | root |
| `TavaMesh` (Static Mesh Component, child of root) | Static Mesh = Engine `Cube` scaled about (0.1, 0.1, 0.5) or another placeholder; **Collision Presets = NoCollision**; Relative Location about (60, 0, 0) |
| `AttackRange` (Sphere Collision, child of root) | rename it `RangeSphere` to avoid a name clash with the variable. Sphere Radius `120.0`; **Collision Presets = OverlapAllDynamic**; **Generate Overlap Events = true**; **Hidden in Game = true** (untick temporarily to see the range while debugging) |

Name the sphere `RangeSphere`. The variable named `AttackRange` below is different.

### 4.2 Variables (Instance Editable on all)

| Name | Type | Default |
|------|------|---------|
| `AttackRange` | Float | `120.0` |
| `AttackDamage` | Float | `25.0` |
| `AttackCooldown` | Float | `0.5` |
| `bCanAttack` | Boolean | `true` |
| `DamageCounter` | Integer | `0` |

### 4.3 Event Graph

**A. Event BeginPlay** **[DEV-4]**

```
Event BeginPlay
  -> RangeSphere -> Set Sphere Radius (AttackRange)
```

**B. Custom Event: PerformAttack** (called by BP_Hero)

Blueprints cannot return early from a latent custom event with a "return" node, so the cooldown check is a Branch and False simply ends.

```
Custom Event PerformAttack
  -> Branch (Condition = bCanAttack)
       False: end (nothing happens, AC-3 cooldown)
       True:
         -> Set bCanAttack = false
         -> RangeSphere -> Get Overlapping Actors (Class Filter = Actor)  -> Out Actors
         -> For Each Loop (Array = Out Actors)
              -> Branch (Condition = Array Element != Get Owner)        // never damage the wielder
                   True:
                     -> Apply Damage
                          Damaged Actor = Array Element
                          Base Damage = AttackDamage
                          Event Instigator = Get Instigator Controller
                          Damage Causer = Self
                          Damage Type Class = DamageType
                        -> Branch (Condition = Return Value > 0.0)
                             True:
                               -> DamageCounter = DamageCounter + 1       (Set variable)
                               -> Print String
                                    In String = Append("Damage Counter: ", ToString(DamageCounter))
                                    Print to Screen = true, Print to Log = true, Duration = 2.0
         -> Completed (of the For Each Loop):
              -> Delay (Duration = AttackCooldown)
              -> Set bCanAttack = true
```

Important wiring points:
- The `Delay` hangs off the **Completed** pin of the loop, not inside the loop body. Otherwise the cooldown is multiplied by the target count.
- `Get Overlapping Actors` already returns each actor once even if several of its components overlap.
- `bCanAttack` is set to false before the loop so a rapid second press during the loop cannot start a second attack.
- Zero targets in range is valid: the loop does nothing, the cooldown still runs, the counter does not change.
- The Apply Damage return value is the damage actually applied. An actor with no Event AnyDamage handler still returns the value from the engine, because Apply Damage reports damage dealt rather than whether a graph handled it. If non-damageable props (a plain cube with no handler) increment the counter during QA, add the actor tag `Damageable` to real targets and add `Actor Has Tag` to the branch. Record this in the sign-off notes if you hit it.
- Visual swing (animation) is out of scope. The attack is instant, with a cooldown.

### 4.4 Compile checklist for BP_Weapon_Tava

- [ ] Compiles with no errors
- [ ] RangeSphere profile OverlapAllDynamic, Generate Overlap Events on
- [ ] TavaMesh has NoCollision (otherwise it blocks the hero)
- [ ] Delay is on the Completed pin

---

## 5. Wire Attack: BP_Hero to BP_Weapon_Tava

Already specified in 3.3-A (spawn, attach, store `WeaponRef`) and 3.3-C (`IA_Attack [Started]` calls `WeaponRef -> PerformAttack`). Section 5 is satisfied when:

- [ ] `PerformAttack` appears as a callable node on a `BP_Weapon_Tava` reference (compile BP_Weapon_Tava first)
- [ ] `IA_Attack [Started]` is connected to it through the `Is Valid` branch

---

## 6. Level / World Setup

### 6.1 Test level

1. `File > New Level` > `Basic` (or Empty plus a floor). Save as `Content/Maps/L_TestArena` (create `Content/Maps` if missing).
2. `Window > World Settings` > **GameMode Override** = `BP_GarbageGameMode`.
3. Place a **Player Start** about 300 units above the floor origin if there is a floor at Z=0, or on the floor. Make sure the capsule does not spawn inside geometry.
4. Make sure the floor is large (at least 2000 x 2000) and has collision.
5. Optional: `Project Settings > Maps & Modes > Editor Startup Map / Game Default Map` = `L_TestArena`.

### 6.2 Debug target (temporary, replaced by Story 002 enemies)

Create `Content/Blueprints/Actors/Debug/BP_Debug_Target` (Parent Actor):

- Component: Static Mesh `Cube` as root, scale (1, 1, 1.5) so it is visible. **Generate Overlap Events = true**. Collision Preset: `BlockAllDynamic` or `OverlapAllDynamic`, both work with the Tava sphere (Overlap vs Block resolves to Overlap). Mobility = Movable.
- Event AnyDamage (Damage):
  ```
  Event AnyDamage (Damage)
    -> Print String  Append("Enemy hit: ", ToString(Damage))   (Print to Screen true, Print to Log true, Duration 2)
  ```

Place **three** instances in the level relative to the Player Start (units from the hero's capsule center):

| Instance | Position from Player Start | Purpose |
|----------|----------------------------|---------|
| `Target_Near` | 80 units in front | well inside the 120 range |
| `Target_Far` | 400 units away | clearly outside range |
| `Target_Edge` | about 200 units away | just outside range (120 radius + target half width ~50 means ~170 reaches the sphere edge) |

Because the sphere overlaps the target's mesh bounds, "in range" means the target body touches the radius, not just its center. Keep Target_Edge at 260 or more if you want it unambiguously outside.

**Section 6 done when**: PIE spawns BP_Hero at the Player Start with the top-down camera, with no extra default pawn visible.

---

## 7. Manual QA Checklist

Run everything in PIE (`Play > Selected Viewport`). Open the Output Log (`Window > Output Log`) to read Print String lines.

Preconditions:
- [ ] All Blueprints compile with no errors
- [ ] `BP_GarbageGameMode` is set in World Settings of `L_TestArena`
- [ ] Debug targets and the debug H key are in place (they are removed at the end)

### AC-1: Movement (keyboard, gamepad, facing)
- [ ] W moves the character up the screen, S down, A left, D right
- [ ] Release of all keys stops the character within a fraction of a second
- [ ] Character visibly rotates to face the movement direction (the FacingMarker or the mesh front points where it walks)
- [ ] Diagonal movement (W+D) is not noticeably faster than a single direction
- [ ] No spiralling or circling while holding a single direction
- [ ] Gamepad left thumbstick moves in the correct direction and rotates the character
- [ ] A small stick drift near center (inside the 0.2 dead zone) does not move the character
- [ ] Mouse movement does not rotate the character

### AC-2: Fixed top-down camera
- [ ] The camera looks down at roughly 45 degrees and the character is visible at screen centre
- [ ] The camera follows the character when it moves across the arena
- [ ] The camera angle does NOT change when the character rotates (verifies Absolute Rotation, DEV-2)
- [ ] Walking to the edges and around obstacles never hides the character (camera collision test is off)
- [ ] Mouse look does not move the camera
- [ ] Press F8 / eject check: in the Details of the spawned BP_Hero SpringArm, Rotation reads Pitch -45 and Arm Length 600

### AC-3: Attack input and cooldown
- [ ] Left Mouse Button triggers the attack (Output Log or on-screen text appears when a target is in range)
- [ ] Gamepad Face Button Bottom triggers the attack
- [ ] The attack fires once per press, holding the button does not auto-repeat
- [ ] Pressing again within 0.5 s of the previous attack does nothing (no new hit message)
- [ ] Pressing again after 0.5 s works
- [ ] Attacking with no target in range does nothing visible and does not error
- [ ] Changing `AttackCooldown` to `2.0` on the weapon default changes the wait accordingly (reset to `0.5` afterwards)
- [ ] Attack input is ignored once the hero is dead

### AC-4: Damage in range / out of range
- [ ] Standing so that `Target_Near` is within ~120 units and attacking prints "Enemy hit: 25.0"
- [ ] Attacking while only `Target_Far` is nearby prints nothing (no damage)
- [ ] Attacking while only `Target_Edge` (260+ units) is nearby prints nothing
- [ ] Walking toward Target_Edge until it is just inside range and attacking prints "Enemy hit"
- [ ] With two targets in range, one attack hits both (two "Enemy hit" lines)
- [ ] The hero never damages itself (no "Hero HP" line appears from attacking)
- [ ] Setting `AttackDamage` to `50.0` prints "Enemy hit: 50.0" (reset to `25.0` afterwards)
- [ ] Temporarily unhide `RangeSphere` (Hidden in Game off): the visible radius matches the observed hit range (and `AttackRange` change alters it, DEV-4)

### AC-5: Damage counter
- [ ] The first hit prints "Damage Counter: 1" on screen and in the Output Log
- [ ] Each additional successful hit increments it (2, 3, 4...)
- [ ] A swing that hits two targets increments the counter by 2
- [ ] A swing with no target does not change the counter
- [ ] A press blocked by cooldown does not change the counter
- [ ] The counter resets to 0 after the run restarts (AC-6), because the level reloads (record whether this matches the intended design; no persistence is specified)

### AC-6: HP, death, run restart
- [ ] At level start, Output Log or debug shows the hero at full HP (press H once: "Hero HP: 75")
- [ ] Press H four times total: HP goes 75, 50, 25, 0
- [ ] On reaching 0, "Hero died - restarting" prints exactly once, even if H is mashed
- [ ] After death the character stops moving and WASD / attack do nothing
- [ ] After about 1 s the level reloads and the hero is back at the Player Start with full HP and the Tava
- [ ] The restart works repeatedly (die three times in a row, no errors in the Output Log, no duplicate heroes)
- [ ] HP never displays below 0
- [ ] **Clean up**: remove the debug H key node and delete the Comment box, then recompile BP_Hero

### AC-7: Starts with the Tava
- [ ] On PIE start, the Outliner (World Outliner while playing, switch to "Simulate/PIE world") shows exactly one `BP_Weapon_Tava_C` instance attached to BP_Hero
- [ ] Attacking works immediately from the first frame with no pickup step
- [ ] After a restart there is still exactly one Tava (not two)
- [ ] (Optional) Select BP_Hero in PIE and confirm `WeaponRef` shows the Tava instance

### AC-8: Enhanced Input
- [ ] No legacy Action/Axis Mappings are used: `Project Settings > Input` Action Mappings and Axis Mappings are empty
- [ ] BP_Hero uses `Enhanced Input Action IA_Move` and `IA_Attack` event nodes (not InputAxis / InputAction legacy nodes). The temporary H key node is a debug exception and is removed
- [ ] `IMC_Hero` is added in BeginPlay via `Get Enhanced Input Local Player Subsystem` > `Add Mapping Context` (Priority 0)
- [ ] Console `showdebug enhancedinput` lists `IMC_Hero` and shows `IA_Move` / `IA_Attack` values changing when keys are pressed (command name is unverified for 5.8, the 5.8 Input Debugger may replace it, record what you used)
- [ ] Removing the `Add Mapping Context` node temporarily makes all input stop (proves the IMC is what drives input). Restore it afterwards
- [ ] No reference to the Raw Input plugin anywhere (`Edit > Plugins` shows it unused by this project)

### Regression and cleanliness
- [ ] Output Log has no Blueprint runtime errors ("Accessed None", "Accessed Trash") in a 2-minute session
- [ ] Debug nodes (H key) removed
- [ ] `Hero HP:` Print String is still acceptable until Story 002 introduces a HUD, then removed
- [ ] Packaging smoke test is not required for this story

---

## 8. Sign-off and Evidence

Required by project standards: the story changes player-observable behaviour, so it must be launched and observed. Save screenshots to `production/qa/evidence/` (create if missing). They must remain on disk.

| Evidence file (suggested name) | Shows |
|--------------------------------|-------|
| `production/qa/evidence/story-001-topdown-camera.png` | Hero visible from the 45 degree camera (AC-2) |
| `production/qa/evidence/story-001-attack-hit.png` | Tava attack with "Enemy hit" and "Damage Counter: N" on screen (AC-4, AC-5) |
| `production/qa/evidence/story-001-death-restart.png` | "Hero died - restarting" message (AC-6) |

Result record:

| Field | Value |
|-------|-------|
| Tester | |
| Date | |
| Editor build (Help > About) | |
| Fallbacks used (for example Event Possessed instead of BeginPlay) | |
| Node names that differed from this guide | |
| Failed items and notes | |
| Overall | [ ] Pass  [ ] Pass with notes  [ ] Fail |

After sign-off: tick the acceptance criteria in the story, set the story's Test Evidence **Status** to created, and run `/code-review` or `/story-done`.

## 9. Open Questions for the Designer

1. Should `DamageCounter` persist across run restarts (meta stat), or reset each run? Currently it resets, because it lives on the weapon and the level reloads.
2. Should the attack hit in a full circle around the hero (as built) or only in front? A forward arc would need an offset sphere or a dot-product check.
3. Is the 1 s restart delay and instant reload acceptable for the MVP fail state, or does a death screen come in a later story?
