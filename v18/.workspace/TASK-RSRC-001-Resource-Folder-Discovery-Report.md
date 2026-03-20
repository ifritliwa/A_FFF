# Resource Folder Discovery Report
## 1. Executive Summary
- Requested focus path `C:\IAGOS\server\v18\Resource` does not exist in this workspace.
- Closest runtime-equivalent resource hub is `C:\IAGOS\server\v18\Server\Resource` (analyzed here).
- Scale: 13,724 files total.
- Dominant payload is model/world assets (`.o3d`, `.ani`, `.lnd`, `.dds`, `.chr`), but the highest-leverage editable surface is tabular/scripted root resources (`prop*`, `define*`, `*.inc`, `*.txt`, `*.csv`, `*.lua`).
- Strong structural pattern exists for schema-driven editing:
  - data table: `propX.txt` (or `.csv`)
  - localized strings: `propX.txt.txt`
  - enums/constants: `define*.h`
  - model binding: `mdlDyna.inc`, `mdlObj.inc`
- Recommended first implementation target: `propItem` family (`propItem.txt` + `propItem.txt.txt` + `defineItem.h`, then `mdlDyna.inc` binding).

## 2. Folder Inventory
- Top-level folders under analyzed root:
  - `LuaFunc`
  - `Model`
  - `World`
- Top-level file concentration:
  - Root files: 238
  - `Model`: 10,288 files
  - `World`: 3,187 files
  - `LuaFunc`: 11 files
- `World` structure:
  - 46 world-instance subfolders
  - naming split: 26 `Wd*` (world zones), 20 `Du*` (dungeon/instance zones)
  - strong per-world package pattern: usually `.wld` + `.rgn` + `.dyo` + many `.lnd` + optional `.dds` + optional `.cnt` + one `.txt.txt`
- `Model` structure:
  - heavy flat asset corpus; mostly `.o3d`, `.ani`, `.chr`
  - registry-style linkage files at root: `mdlObj.inc`, `mdlDyna.inc`
- Central vs peripheral:
  - Central: `prop*`, `define*`, `mdl*`, `World.inc`, `world.txt.txt`, `propQuest*`, `propMoverEx.inc`, `resource.txt`, `Masquerade.prj`
  - Peripheral/support/runtime baggage: DLL/SYS/ASI/EXE assets, `notice*.htm`, `network.log`, anti-cheat artifacts
- Repeated naming patterns:
  - `propX` triads: `.csv/.txt/.txt.txt` (e.g., `propItem`, `propSkill`, `propMover`)
  - language variants: `filter_*.inc`, `InvalidName_*.inc`, `Letter_*.inc`
  - duplicated text suffix family: `*.txt.txt` (73 files)
  - world packages by folder basename (`WdMadrigal.*`, `DuKalgas.*`)

## 3. File Type Inventory
- Total files: 13,724
- Major extensions by count:
  - `.o3d` 5,480
  - `.ani` 4,336
  - `.lnd` 1,796
  - `.dds` 1,198
  - `.chr` 465
  - `.txt` 128
  - `.inc` 81
  - `.rgn` 46
  - `.dyo` 46
  - `.wld` 46
  - `.lua` 28
  - `.h` 22
  - `.cnt` 9
  - `.dll` 9
  - `.csv` 8
  - others (small counts): `.ASE`, `.xls`, `.dat`, `.asi`, `.sys`, `.exe`, `.htm`, `.ini`, `.prj`, `.des`, `.tga`, `.inf`, `.log`
- Area split by extension:
  - `Model`: `.o3d/.ani/.chr/.ASE/.inf`
  - `World`: `.lnd/.dds/.wld/.rgn/.dyo/.cnt/.txt`
  - Root/Lua: table/config/script/header files and runtime binaries

## 4. Major Resource Families
- Text/table resources
  - Why: tab/comma-delimited rows with stable symbolic columns and ID tokens.
  - Representative files: `propItem.txt`, `propSkill.txt`, `propMover.txt`, `propCtrl.txt`, `propLang.txt`, `PaidWorldSet.txt`, `GuildCombat.txt`.
  - Likely purpose: core game definitions and balancing.
- Model resources
  - Why: large `.o3d/.ani/.chr` corpus under `Model`.
  - Representative files: `Model\arrow.o3d`, `Model\Item_RidRidAnipiyoco01.chr`, `Model\Item_RidRidAnipiyoco01_stand.ani`.
  - Likely purpose: mesh/animation/character rig data.
- Texture/icon resources
  - Why: `.dds` appears in world tiles and icon references in tables.
  - Representative files: `World\WdMadrigal\*.dds`, icon names in `propItem.txt` such as `itm_WeaAxeCurin.dds`.
  - Likely purpose: terrain textures, item/skill icons, material maps.
- Effect resources
  - Why: tokens `XI_*`, `MTI_*`, `dwSfx*` and effect-related tables/scripts.
  - Representative files: `propSkill.txt`, `EnvironmentEffect.txt`, `Event.lua`.
  - Likely purpose: spell/hit/visual effect binding.
- UI resources
  - Why: window script grammar (`WTYPE_*`) and UI text IDs.
  - Representative files: `resdata.inc`, `ResData.h`, `WndStyle.h`, `WorldDialog.txt`.
  - Likely purpose: client window layout and UI string binding.
- Localization/text resources
  - Why: `IDS_*` key/value files and language profile tables.
  - Representative files: `propItem.txt.txt`, `propQuest.txt.txt`, `world.txt.txt`, `textClient.inc`, `propLang.txt`, `filter_*.inc`.
  - Likely purpose: multilingual display strings and locale behavior.
- Map/world resources
  - Why: strict world package pattern and world enum/title mapping.
  - Representative files: `World.inc`, `defineWorld.h`, `World\WdMadrigal\WdMadrigal.wld`, `World\WdMadrigal\WdMadrigal.wld.cnt`.
  - Likely purpose: map topology, regions, metadata, labels.
- Animation-related resources
  - Why: high-volume `.ani` and motion tables.
  - Representative files: `Model\*.ani`, `propMotion.txt`, `propMotion.csv`.
  - Likely purpose: animation clips and motion metadata.
- Sound/music resources
  - Why: `defineSound.h`, `SND_*` tokens in `propItem/propMover/propSkill`, `bgm` fields in world files.
  - Representative files: `defineSound.h`, `propSkill.txt`, `WdMadrigal.wld`.
  - Likely purpose: sound event and BGM binding.
- Config/meta resources
  - Why: project/resource manifests and startup config.
  - Representative files: `Masquerade.prj`, `resource.txt`, `neuzDefault.ini`, `PreLoadDefine.inc`, `PreLoadText.inc`.
  - Likely purpose: load order, resource packaging, startup settings.
- Unknown/unclassified resources
  - Why: binary/proprietary formats and runtime binaries not self-describing.
  - Representative files: `.lnd`, `.rgn`, `.dyo`, `.chr`, `.o3d`, `.ani`, `.dat`, `.ASE`, `.des`, `.sys`.
  - Likely purpose: engine-specific serialized world/model/runtime state.

## 5. Text/Table Resource Analysis
- Row/column structured families (strongly schema-like):
  - `propItem.txt`, `propSkill.txt`, `propMover.txt`, `propCtrl.txt`, `propKarma.txt`, `propLang.txt`, `propTroupeSkill.txt`, `propMotion.txt`
  - delimiter: tab (`\t`) in `.txt`; comma in `.csv`
  - headers: typically dual comment-header rows (`// localized labels`, then `// internal field names`)
  - schema consistency: high within each family
- Parallel format families:
  - `propItem.csv` + `propItem.txt` + `propItem.txt.txt`
  - `propSkill.csv` + `propSkill.txt` + `propSkill.txt.txt`
  - `propMover.csv` + `propMover.txt` + `propMover.txt.txt`
  - `propCtrl.csv` + `propCtrl.txt` + `propCtrl.txt.txt`
  - indicates import/export/editor compatibility layers already existed.
- Non-tabular but structured script grammars:
  - brace-block DSL: `propQuest*.inc`, `propMoverEx.inc`, `resdata.inc`, `World.inc`, `CreateMonster.txt`, `itemScript.txt`
  - Lua script API style: `Event.lua`, `CreateMonster.lua`, `PartyDungeon.lua` + `LuaFunc\*.lua`
- Relational signs (strong):
  - Symbolic IDs reused across files (`II_*`, `MI_*`, `CI_*`, `WI_*`, `IDS_*`, `XI_*`, `SND_*`).
  - Example chain: `II_WEA_AXE_RODNEY` in `defineItem.h` -> `propItem.txt` row -> `IDS_PROPITEM_TXT_000124` -> `propItem.txt.txt` text -> `mdlDyna.inc` model binding.
- Generic editor suitability:
  - Best first candidates:
    - `propItem.txt`
    - `propSkill.txt`
    - `propMover.txt`
    - `propCtrl.txt`
    - `propKarma.txt`
  - Reason: stable columns, repeatable IDs, strong cross-file references, large gameplay leverage.

## 6. Probable Cross-File Connections
- `II_*` item ID graph
  - Evidence: `defineItem.h`, `propItem.txt`, `propItem.txt.txt`, `mdlDyna.inc`, events/quests.
  - Strength: strong evidence.
- `MI_*` monster ID graph
  - Evidence: `defineObj.h`, `propMover.txt`, `propMover.txt.txt`, `propMoverEx.inc`, `propQuest.inc`, `CreateMonster.*`, Lua events.
  - Strength: strong evidence.
- `CI_*` control/object graph
  - Evidence: `defineObj.h` + `propCtrl.txt` + `mdlDyna.inc`.
  - Strength: strong evidence.
- `WI_*` world graph
  - Evidence: `defineWorld.h` numeric enums + `World.inc` file aliases/titles + `world.txt.txt` names + world folder package names.
  - Strength: strong evidence.
- `IDS_*` localization graph
  - Evidence: almost every major table references `IDS_*`; resolved in corresponding `*.txt.txt`.
  - Strength: strong evidence.
- Item/skill icon and texture linkage
  - Evidence: icon filenames (`*.dds`) embedded in `propItem.txt`/`propSkill.txt`; `World` folder terrain `.dds` near `.lnd` tiles.
  - Strength: strong evidence for item/skill icon lookup, moderate evidence for full material pipeline.
- Model-to-texture linkage in model binaries
  - Evidence: binary `arrow.o3d` payload visibly contains `ARROW1.dds` string.
  - Strength: moderate evidence (format still proprietary).
- Shop/drop/quest/NPC linkage
  - Evidence: `propQuest.inc` calls like `QuestItem(MI_AIBATT1, II_...)`; `propMoverEx.inc` drop tables; event files reference `II_*`/`MI_*`.
  - Strength: strong evidence.
- Load-order/dependency chain
  - Evidence: `Masquerade.prj`, `resource.txt`, `PreLoadDefine.inc`, `PreLoadText.inc` enumerate ordered resources.
  - Strength: strong evidence.
- Potential missing/stale declaration
  - Evidence: `Masquerade.prj` references `textServer.inc` and additional `Client\*.txt.txt` files not present under analyzed root.
  - Strength: moderate evidence (could exist in other folder/package).

## 7. Editability Classification
- `prop*` tabular families (`propItem/Skill/Mover/Ctrl/Karma/Motion/TroupeSkill`)
  - Label: generic-table with custom validation
  - Why: schema-like rows, but heavy enum/reference constraints.
- `*.txt.txt` localization maps
  - Label: generic-table friendly
  - Why: key/value patterns are stable and simple.
- `define*.h` enum constants
  - Label: adapter-heavy
  - Why: C macro syntax and numeric ID stability rules require guarded editing.
- Quest/drop DSL (`propQuest*.inc`, `propMoverEx.inc`, `propDropEvent.inc`)
  - Label: adapter-heavy
  - Why: nested script blocks and function-like directives.
- Lua event/gameplay scripts
  - Label: adapter-heavy
  - Why: executable logic, not pure data.
- UI scripts (`resdata.inc`)
  - Label: likely visual editor later
  - Why: coordinate-rich widget layout benefits from visual tooling.
- World package (`.wld/.rgn/.dyo/.lnd/.cnt` + world txt labels)
  - Label: likely visual editor later
  - Why: binary/spatial formats and map composition complexity.
- Model/animation assets (`.o3d/.ani/.chr/.ASE`)
  - Label: unknown / requires deeper parsing
  - Why: proprietary binaries, high break risk.

## 8. Risks and Unknowns
- Implicit ordering dependencies
  - `Masquerade.prj` and preload scripts imply order-sensitive loading.
- Quoted string/escaping sensitivity
  - Triple-quote patterns (`"""..."""`) in `.txt/.csv` data.
- Duplicate or drifting IDs
  - Same symbolic IDs appear across many files; accidental divergence risk is high.
- Mixed-purpose files
  - Some `.inc` files are lists, others are DSL scripts, others are near-freeform text.
- Binary format opacity
  - `.lnd/.rgn/.dyo/.o3d/.ani/.chr/.dat` not safely editable without format adapters.
- Client/server coupling
  - IDs and string keys are shared with client-facing resources and world metadata.
- Encoding pitfalls
  - mixed Korean/English legacy encoding; warning comments explicitly caution editor choice for Unicode-sensitive files.
- Path/packaging mismatch
  - project manifest references files not present in this single folder scope.

## 9. Recommended Hub/Core Implications
- First shared core should support:
  - schema registry per family (columns, types, enums, constraints)
  - reference resolver (ID cross-link graph across `II/MI/CI/WI/IDS`)
  - text encoding-aware reader/writer modes
  - diff-safe row editing with key-column identity
  - validator pipeline (enum existence, key collisions, dangling references)
- Should be schema-driven first:
  - `propItem`, `propSkill`, `propMover`, `propCtrl`, `propKarma`, `propMotion`, localization `*.txt.txt`
- Should be version-profile driven:
  - `define*.h` domains and table column sets (legacy versions can drift)
  - world list/world IDs (`defineWorld.h` + `World.inc`) and load manifest specifics
- First module recommendation:
  - `propItem` module with linked views into:
    - `defineItem.h` (enum/source of truth)
    - `propItem.txt` (stats)
    - `propItem.txt.txt` (localized strings)
    - `mdlDyna.inc` (model binding)
- Reusable table framework group:
  - `propItem`, `propSkill`, `propMover`, `propCtrl`, `propKarma`, `propMotion`, `propTroupeSkill`
- Defer initially:
  - world binaries, model binaries, UI visual layout (`resdata.inc`) until adapter/visual stages.

## 10. Priority Next Steps
1. Build ID graph extractor for `defineItem.h`, `defineObj.h`, `defineWorld.h`, and all `prop*.txt` key columns.
2. Prototype schema-driven import/export for `propItem.txt` + `propItem.txt.txt` with strict validator checks.
3. Add enum/reference validators (`II/MI/CI/WI/IDS`) and unresolved-key reporting.
4. Add second module `propSkill` using same table framework to prove reuse.
5. Add model-link adapter read-only view from `mdlDyna.inc` for item/monster ID binding.
6. Avoid editing/deploying world/model binaries early (`.lnd/.rgn/.dyo/.o3d/.ani/.chr`).

Recommended first implementation target for the hub: `propItem` family with cross-file validation and localization linkage.

## Appendix A — Extension Summary Table
| Extension | Count | Primary Area | Notes |
|---|---:|---|---|
| .o3d | 5480 | Model | model meshes (binary) |
| .ani | 4336 | Model | animation clips (binary) |
| .lnd | 1796 | World | terrain tile chunks (binary) |
| .dds | 1198 | World | terrain/icon textures |
| .chr | 465 | Model | character/rig assets (binary) |
| .txt | 128 | Root/World | tabular + script-like + labels |
| .inc | 81 | Root | config/script DSL families |
| .rgn | 46 | World | region data (binary) |
| .dyo | 46 | World | world dynamic data (binary) |
| .wld | 46 | World | world metadata script-like |
| .lua | 28 | Root/LuaFunc | event and server logic |
| .h | 22 | Root | enum/define headers |
| .csv | 8 | Root | table variants for prop families |
| .cnt | 9 | World | continent/polygon metadata |
| .dll | 9 | Root | runtime binary dependencies |
| .xls | 3 | Root | legacy spreadsheet artifacts |
| others | 31 | Root/Model | `.ASE/.dat/.exe/.sys/.asi/...` |

## Appendix B — Candidate First-Module Files
- `C:\IAGOS\server\v18\Server\Resource\defineItem.h`
- `C:\IAGOS\server\v18\Server\Resource\propItem.txt`
- `C:\IAGOS\server\v18\Server\Resource\propItem.txt.txt`
- `C:\IAGOS\server\v18\Server\Resource\mdlDyna.inc`
- `C:\IAGOS\server\v18\Server\Resource\defineObj.h`
- `C:\IAGOS\server\v18\Server\Resource\propMover.txt`
- `C:\IAGOS\server\v18\Server\Resource\propMover.txt.txt`
- `C:\IAGOS\server\v18\Server\Resource\propSkill.txt`
- `C:\IAGOS\server\v18\Server\Resource\propSkill.txt.txt`
- `C:\IAGOS\server\v18\Server\Resource\propCtrl.txt`
- `C:\IAGOS\server\v18\Server\Resource\propCtrl.txt.txt`
- `C:\IAGOS\server\v18\Server\Resource\propLang.txt`

## Appendix C — Unknown Files Requiring Deeper Research
- World binary family:
  - `.lnd`, `.rgn`, `.dyo` (per-world packaged)
- Model binary family:
  - `.o3d`, `.ani`, `.chr`, `.ASE`, `.inf`
- Legacy/system/binary metadata:
  - `questState.dat`, `regInfo.dat`, `GameGuard.des`, `npkcrypt.*`, `mss*.asi`, runtime DLLs
- Legacy spreadsheets and pack tooling:
  - `properties.xls`, `region.xls`, `propSkillAdd.xls`, `- Merge.exe`, `3. Database.exe`, `7. World.exe`
- Manifest mismatch checks:
  - `Masquerade.prj` references `textServer.inc` and some `Client\*.txt.txt` files not present in this folder scope.
