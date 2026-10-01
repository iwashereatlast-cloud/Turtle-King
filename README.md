# Turtle AutoLogin — WoW 3.3.5a (WotLK) GlueXML Port

A port of [Haaxor1689/turtle-autologin](https://github.com/Haaxor1689/turtle-autologin) to the
**World of Warcraft 3.3.5a (12340)** client, using loose GlueXML files in
`Data/Interface/GlueXML/` — **no Patch-Y MPQ required**.

## Features (same as the original Turtle WoW patch)

- Adds an Accounts select panel to the login screen
- Automatically adds accounts with saved login info to the list
- Select accounts to log in (double-click to login directly)
- Check "Auto-login this character" on the character select screen to always
  automatically load into game with this character selected on future logins
- Remove saved characters and accounts with controls at the bottom
- Duplicate passwords are compressed in the saved string; a warning is shown
  if you exceed the 128-character `accountName` limit in Config.wtf

## Requirements

The stock WoW 3.3.5a client blocks edits to GlueXML files. You must patch your
client before installing, e.g. with the
[WoW 3.3.5 Patcher by #KUG](https://github.com/Stormhand-dev/WoWPatcher335)
("Allow Interface Edits"). This is the GlueXML equivalent of the Turtle WoW
client, which permits interface edits out of the box.

## Installation

> ⚠️ This mod replaces core GlueXML files. Back up the originals before
> installing. (If you don't have backups, pristine 3.3.5a GlueXML sources are
> available at [s0h2x/WoTLK-3.3.5-UI-Source](https://github.com/s0h2x/WoTLK-3.3.5-UI-Source).)

1. Navigate to your WoW client folder
2. Create the folder `Data/Interface/GlueXML/` if it does not exist
3. Copy the files from `Interface/GlueXML/` of this repository into it:

```
Data/
  Interface/
    GlueXML/
      AccountLogin.lua
      AccountLogin.xml
      CharacterSelect.lua
      CharacterSelect.xml
```

4. Delete `Data/Cache/WDB` (cached interface files) before the first launch so
   the client re-reads the loose GlueXML.

Only these four files are needed — the rest of the stock GlueXML is untouched.

## How it works

Account information is stored in `accountName` in `WTF/Config.wtf`, the same
variable the vanilla client uses to remember your account name. The stock
3.3.5a API (`GetSavedAccountName` / `SetSavedAccountName`) round-trips this
string, so the Turtle serialization format is preserved:

```
<name> <password> <character-index?>;
```

Each entry ends with a `;`. Duplicate passwords are stored as `~<index>`
references. Omit the character index to disable character auto-login for that
account. Because `accountName` is capped at 128 characters, the mod shows a
warning on the login panel when the limit is exceeded.

On the character select screen, the stored index is used to select the saved
character and enter the world. When "Auto-login this character" is checked and
you press Enter World, the currently selected character index is saved for that
account.

> ⚠️ `WTF/Config.wtf` will **contain your passwords** — think before sharing
> it, before uploading logs, or before posting it anywhere.

## Uninstalling

Delete the four files above from `Data/Interface/GlueXML/` and restore your
backed-up originals (or re-extract them from `interface.mpq`/a source mirror).

## Differences from the 1.12 Turtle version

- 3.3.5a XML handlers receive `self` instead of the implicit `this`; all
  handlers were updated accordingly.
- `string.gfind` (1.12 only) is replaced with `string.gmatch`.
- The account list panel is a child of the stock WotLK `AccountLoginUI`
  layout, so the stock WotLK login visuals (Northrend model, token support,
  server alerts, etc.) are preserved.
- The stock account-name dropdown is hidden when the autologin data format is
  present, since both use `accountName`.
