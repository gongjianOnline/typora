# Vscode配置Bash终端

```json
// 新版本下配置bash
"terminal.integrated.profiles.windows": {
    "PowerShell": {
      "source": "PowerShell",
      "icon": "terminal-powershell"
    },
    "Command Prompt": {
      "path": [
        "${env:windir}\\Sysnative\\cmd.exe",
        "${env:windir}\\System32\\cmd.exe"
      ],
      "args": [],
      "icon": "terminal-cmd"
    },
    "GitBash": {
      "path": "E:\\install\\Git\\bin\\bash.exe"
    }
  },
  "terminal.integrated.defaultProfile.windows": "GitBash"
```

# Vscode react HTML 不闭合

```json
"emmet.includeLanguages": {
	"javascript": "javascriptreact"
},
```

