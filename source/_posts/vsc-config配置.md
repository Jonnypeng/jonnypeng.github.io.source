---
title: vsc.config配置
date: 2019-07-18 19:02:42
tags:
- vsc
category:
- 代码片段
---

<!-- more -->

```
{
    "editor.quickSuggestions": {
        "other": false,
        "comments": false,
        "strings": false
    },
    "editor.suggest.snippetsPreventQuickSuggestions": false,
    "editor.acceptSuggestionOnCommitCharacter": false,
    "editor.suggestOnTriggerCharacters": false,
    "editor.wordBasedSuggestions": false,
    "editor.fontFamily": "Courier New",
    "editor.fontSize": 18,
    "editor.selectionHighlight": false,
    "editor.renderLineHighlight": "none",
    "editor.wordWrap": "on",
    "files.associations": {
        "*.cjson": "jsonc",
        "*.wxss": "css",
        "*.wxs": "javascript"
    },
    "emmet.includeLanguages": {
        "wxml": "html"
    },
    "workbench.colorTheme": "Base16 Tomorrow Dark",
    "editor.codeLens": false,
    "editor.colorDecorators": false,
    "editor.highlightActiveIndentGuide": false,
    "editor.folding": true,
    "editor.renderIndentGuides": false,
    "editor.hover.delay": 1000,
    "workbench.startupEditor": "newUntitledFile",
    "terminal.integrated.fontSize": 14,
    "terminal.integrated.lineHeight": 1.2,
    "workbench.colorCustomizations": {
        "editor.background": "#000000"
    },
    "typescript.updateImportsOnFileMove.enabled": "always",
    "workbench.editor.enablePreview": false,
    "window.zoomLevel": 0,
    "breadcrumbs.enabled": true,
    "editor.minimap.enabled": false,
    "javascript.updateImportsOnFileMove.enabled": "always",
    "terminal.integrated.rendererType": "dom",
    "update.enableWindowsBackgroundUpdates": false,
    "extensions.autoUpdate": false
}
```
