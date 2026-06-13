# Station Budget Watchdog

Keeps player stations with configured name tags funded to a manual target you
control, and reclaims their hoarded surplus. It can also bypass the name-tag
filter and handle every player station using the vanilla planned budget,
reproducing the original HappyStation style. No vanilla files are patched; safe
to add to or remove from an existing save.

## Why

The vanilla "expected operating budget" is calculated from a station's potential
cargo volume, so a big warehouse politely asks you to donate more credits than
you will gather in the entire playthrough — and then parks whatever it gets as
unusable dead weight. A manual per-tag target keeps each station liquid enough
to trade while your money keeps working elsewhere.

## Game version compatibility

- 9.00 release - **supported**
- 9.00 betas and release candidates - **supported**
- 8.00 release - **supported**

## Dependencies

- **SirNukes Mod Support APIs** ([link](https://www.nexusmods.com/x4foundations/mods/503)) —
  hard dependency; provides the Extension Options menu used for configuration.

## How it works

Put a tag like `[QT57]` in a station's name and give that tag a target budget
`T`. On every check the watchdog looks at the live balance (default levels
shown; all of them are sliders in the options menu):

- `money < 1x T`: grants credits up to the fill level (`2x T`), capped by your
  account. If you cannot afford that, it tries the plain deficit (back to
  `1x T`); if you cannot afford even that, it warns and waits.
- `money >` trigger level (`3x T`): reclaims the surplus, leaving the leave
  level (`1.5x T`) at the station.
- in between: leaves the station alone.

The wide deadband prevents back-and-forth transfers, and the watchdog clamps
the levels at runtime so a fresh top-up can never itself trip the drain.

## Configuration

Open Extension Options and use the Station Budget Watchdog menu to add or remove
station-name tags and their target budgets, and to tune the watchdog:

| Slider | Default | Meaning |
| --- | --- | --- |
| Check interval | 10 s | How often all stations are checked. |
| Minimum transfer | 1 M Cr | Floor for any single grant or drain. |
| Minimum kept player money | 20 M Cr | Wallet reserve top-ups never dip into. |
| Top-up fill level | 200 % | Balance a grant fills up to, in percent of target. |
| Drain trigger level | 300 % | Balance above which surplus is reclaimed. |
| Drain leave level | 150 % | Balance a drain leaves at the station. |

Default tags:

| Tag | Target |
| --- | --- |
| `[AB5]`  | 5,000,000 Cr |
| `[AB10]` | 10,000,000 Cr |
| `[AB20]` | 20,000,000 Cr |
| `[AB40]` | 40,000,000 Cr |

The first configured tag found in a station's name wins. Untagged stations are
ignored unless `Ignore tag system and handle all player stations` is enabled.
In that mode every operational player station is handled and its target is the
station's vanilla planned budget (`productionmoney`).

To reproduce original HappyStation behavior, enable `Ignore tag system and
handle all player stations`, keep `Top-up to fill target` enabled, disable
`Drain surplus`, and set both `Minimum transfer` and `Minimum kept player money`
to `0`.

Stations under construction are also ignored until they become operational:
they have no operating account yet, and the engine immediately refunds any
money sent to them. Construction budgets are out of scope for this mod.

The engine quietly returns station money above the station's internal
`maxbudget` to the player - a cap that can be tiny on warehouses regardless of
the huge "expected operating budget" the UI shows. The watchdog lifts a handled
station's `maxbudget` clear of its operating band (2x the drain trigger), so
grants stick and only the watchdog's own drain reclaims surplus. Removing a
tag leaves the lifted cap in place. Vanilla account-management player actions,
such as changing and confirming the station account balance, will reset that
internal value to the vanilla calculation under the hood.

The first check after loading a save is skipped, so the watchdog stays quiet
until one full check interval has passed.

## Pairs well with

**Toggle Upkeep Missions**
([link](https://www.nexusmods.com/x4foundations/mods/1073)) — its "disable
money transfer missions" toggle removes the vanilla upkeep nags that demand the
full calculated auto-budget, the very number this mod exists to ignore. With
the watchdog managing budgets, those missions are pure noise.

## Credits

- A rewrite of **Happy Station** by Bob Hood, used under the MIT License. See
  `LICENSE`. ([nexusmods link](https://www.nexusmods.com/x4foundations/mods/975)).
  ([github link](https://github.com/b0bh00d/HappyStation)).
- By VasiliyTemniy.

## Source

https://github.com/VasiliyTemniy/x4-foundations-station-budget-watchdog
