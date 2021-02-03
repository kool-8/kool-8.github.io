---
layout: post
tags:
- vscode
- prettier
categories: Error
title: "[Error] when prettier not working on vscode"

---
Copy this and paste to your workspace .vscode / settings.json

    {
      "editor.formatOnSave": true,
      "vetur.validation.template": false,
      "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
      },
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
    