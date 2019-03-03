---
title: WindowsのVSCodeでのtasks.jsonとsettings.jsonの設定
date: 2019-03-03T13:40:00+09:00
draft: false
tags: [vscode, Python, 開発環境]
---

いつも忘れるのでメモしておく

## tasks.json
```json
{
    "tasks": [
        {
            "label": "Python",
            "args": ["${file}"],
            "type": "shell",
            "command": "${config:python.pythonPath}",
            "problemMatcher": [],
            "isShellCommand": true,
            "options": {
                "env": {
                    "PYTHONIOENCODING": "UTF-8"
                }
            },
            "group": {
                "kind": "build",
                "isDefault": true
            },
        }
    ],    
}
```

## settings.json
```json
{
    "python.pythonPath": "C:\\Users\\gepur\\Anaconda3\\envs\\me\\python.exe",
    "files.autoGuessEncoding": true,
    // "terminal.integrated.shellArgs.windows": ["/K", "chcp 65001"]
    "terminal.integrated.shell.windows": "C:\\Windows\\system32\\cmd.exe",
    "terminal.integrated.shellArgs.windows": [
        "/k",
        "chcp",
        "65001"
    ]
}
```
