# PSOBB.io Fork Changes (`upstream/master` → `master`)

This document summarizes the server customizations maintained on top of upstream [`fuzziqersoftware/newserv`](https://github.com/fuzziqersoftware/newserv).

---

## Customized Files (`7` files vs `upstream/master`)

### 1. Enemy, Boss & Challenge Mode Telemetry ([`src/Client.hh`](src/Client.hh), [`src/HTTPServer.cc`](src/HTTPServer.cc), [`src/Map.hh`](src/Map.hh), [`src/Map.cc`](src/Map.cc), [`src/ReceiveSubcommands.cc`](src/ReceiveSubcommands.cc))
- **Origin:** PR #4 (`kill-counters`)
- Tracks per-connection Blue Burst enemy kills (`enemy_kill_count`, `rare_enemy_kill_count`, `last_rare_enemy_killed`) and per-`(Episode, Difficulty, Mode, EnemyType)` `kill_counts` in `Client` (`on_enemy_exp_request_bb`).
- Deduplicates multi-phase / multi-entity bosses so each boss fight counts once per killer via `is_boss_main_form()` and `MapState::boss_kills_counted` (cleared on `MapState::reset()`).
- Tracks Challenge Mode stage clears (`on_set_challenge_time_6x95`) with repeat deduplication across clients (`challenge_clear_count`, `last_challenge_clear_*`).
- Exposes `EnemyKillCount`, `RareEnemyKillCount`, `LastRareEnemyKilled`, `KillCounts`, `ChallengeClearCount`, and `LastChallengeClear` per connected client in the HTTP `/y/clients` JSON endpoint for external companion tools.

### 2. Extended Blue Burst Tool Stack Limits ([`system/client-functions/BlueBurstExclusive/StackLimits.s`](system/client-functions/BlueBurstExclusive/StackLimits.s))
- **Origin:** PR #6 (`sync/upstream-20261121`)
- Sets stack limits for Blue Burst tool item classes `0x0A` through `0x15` (Grinders, Materials, Mag Cells, Enemy Parts, AddSlots, Photon Drops, Badges, Presents, Cakes, Events) to `99` (`0x63`) instead of `1` (`0x01`), matching `ItemStackLimits.ToolLimits` in server configuration.

### 3. `$bank` Target Slot Validation ([`src/ChatCommands.cc`](src/ChatCommands.cc))
- **Origin:** PR #7 (`fix/bank-command-validate-slot`)
- Validates requested `$bank` slot indices (`<= 127`) and checks that the target character or bank file exists when selecting another character's bank.
- Preserves and restores `bb_bank_character_index` if loading the target bank fails.

---

## Upstreamed Fork Changes (Historical Note)

- **PR #5 (`shop-limits`)** — `BBExtendedToolShop` configuration and Blue Burst tool shop item-count capping (`DataIndex`, `ItemCreator`, `SendCommands`, `ReceiveSubcommands`, `config.example.json`) was accepted upstream and absorbed in `sync/upstream-20261121`, so only `BBExtendedToolShop` configuration remains downstream.
