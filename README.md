# gitkraken-ja
Unofficial GitKraken Japanese translation project

This repository is a fork of https://github.com/megos/gitkraken-i18n/tree/main.

## Contents
- [Japanese](README-ja.md)

## Structure

Translations are kept per GitKraken version:

```
en/v12.2.1/strings.json   # original English strings
ja/v12.2.1/strings.json   # Japanese translation
```

## Change Language

**Please do this at your own risk! Changing the language may cause issues in GitKraken.**

1. Replace GitKraken's `strings.json` with `ja/{version}/strings.json` from this repository.
   - Windows: `%LOCALAPPDATA%\gitkraken\app-x.x.x\resources\app.asar.unpacked\src\strings.json` (x.x.x is version)
   - Mac: `/Applications/GitKraken.app/Contents/Resources/app.asar.unpacked/src/strings.json`
1. Restart GitKraken.
1. Open the preferences.  
![Preferences Menu Button](./images/options_button.png)
1. Go to "UI Customization" and select the language.  
![UI Customization preferences](./images/ui_customization.png)

## Translate

Translation is done with [Claude Code](https://claude.com/claude-code). No build tooling or dependencies are required.

1. Copy the new version's `strings.json` from GitKraken into `en/{version}/strings.json`.
1. Run the `/project:new-version` slash command to generate `ja/{version}/strings.json`.
1. Verify the result by switching the language, see [Change Language](#change-language).

Translation rules and the full workflow are documented in [CLAUDE.md](CLAUDE.md).
