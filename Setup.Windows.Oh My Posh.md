---
id: d87nej2sjpxbajqmva9qopl
title: Oh My Posh
desc: ""
updated: 1674904175704
created: 1674902573647
---

> [Oh-My-Posh](https://ohmyposh.dev/) -- Resource Link

![](/assets/images/2023-01-28-163655.png){max-width: 300px}

## Installation

```shell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

## Fonts

```shell
oh-my-posh font install
```

###

> ##### VS Code Config

```js
"terminal.integrated.fontFamily": "FiraCode NF",
```

### Create powershell profile

```shell
code $profile
```

### Create a [Theme](https://ohmyposh.dev/) File

> ### clean-detailed.omp.json [Credits](https://github.com/JanDeDobbeleer)

```json
{
  "$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
  "blocks": [
    {
      "alignment": "left",
      "newline": true,
      "segments": [
        {
          "background": "#FEF5ED",
          "foreground": "#011627",
          "leading_diamond": "\ue0b2",
          "properties": {
            "macos": "\uf179 ",
            "ubuntu": "\uf31b ",
            "windows": "\uf871 "
          },
          "style": "diamond",
          "template": " {{ if .WSL }}WSL at {{ end }}{{.Icon}}",
          "trailing_diamond": "<transparent,#FEF5ED>\ue0b2</>",
          "type": "os"
        },
        {
          "background": "#FEF5ED",
          "foreground": "#011627",
          "leading_diamond": "\ue0b2",
          "style": "diamond",
          "template": "\uf489 {{ .Name }} ",
          "trailing_diamond": "<transparent,#FEF5ED>\ue0b2</>",
          "type": "shell"
        },
        {
          "background": "#516BEB",
          "foreground": "#ffffff",
          "leading_diamond": "\ue0b2",
          "properties": {
            "branch_icon": "\ue725 ",
            "fetch_stash_count": true,
            "fetch_status": true,
            "fetch_upstream_icon": true,
            "fetch_worktree_count": true
          },
          "style": "diamond",
          "template": " {{ .UpstreamIcon }}{{ .HEAD }}{{if .BranchStatus }} {{ .BranchStatus }}{{ end }}{{ if .Working.Changed }} \uf044 {{ .Working.String }}{{ end }}{{ if and (.Working.Changed) (.Staging.Changed) }} |{{ end }}{{ if .Staging.Changed }} \uf046 {{ .Staging.String }}{{ end }}{{ if gt .StashCount 0 }} \uf692 {{ .StashCount }}{{ end }} ",
          "trailing_diamond": "<transparent,#516BEB>\ue0b2</>",
          "type": "git"
        },
        {
          "background": "#575656",
          "foreground": "#d6deeb",
          "leading_diamond": "\ue0b2",
          "properties": {
            "style": "roundrock",
            "threshold": 0
          },
          "style": "diamond",
          "template": " {{ .FormattedMs }} ",
          "trailing_diamond": "\ue0b0",
          "type": "executiontime"
        }
      ],
      "type": "prompt"
    },
    {
      "alignment": "left",
      "newline": true,
      "segments": [
        {
          "style": "plain",
          "template": "\u256d\u2500",
          "type": "text"
        },
        {
          "properties": {
            "time_format": "15:04"
          },
          "style": "plain",
          "template": " \u2665 {{ .CurrentDate | date .Format }} |",
          "type": "time"
        },
        {
          "style": "plain",
          "template": " \uf292 ",
          "type": "root"
        },
        {
          "properties": {
            "folder_icon": "\uf07b ",
            "folder_separator_icon": " \uf554 ",
            "home_icon": "\uf7db "
          },
          "style": "plain",
          "template": " {{ .Path }} ",
          "type": "path"
        }
      ],
      "type": "prompt"
    },
    {
      "alignment": "left",
      "newline": true,
      "segments": [
        {
          "properties": {
            "always_enabled": true
          },
          "style": "plain",
          "template": "\u2570\u2500 ",
          "type": "exit"
        }
      ],
      "type": "prompt"
    }
  ],
  "console_title_template": "{{ .Folder }}",
  "transient_prompt": {
    "background": "transparent",
    "foreground": "#FEF5ED",
    "template": "\ue285 "
  },
  "version": 2
}
```

###

> ### Microsoft.PowerShell_profile.ps1

```ps1
oh-my-posh init pwsh --config 'C:/Users/Posh/clean-detailed.omp.json' | Invoke-Expression
```
