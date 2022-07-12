---
title: "Minimalistic VSCode setup"
date: 2022-07-08T10:35:00Z
draft: false
tags: ['vscode']
description: _VSCode_ is a great code editor, but its interface is littered with a lot of unnecessary things and overflowing with informational noise. In this article, we will try to remove everything unnecessary from the interface, as well as find keyboard shortcuts for quick access to it.
---

![cover.png](cover.png)

Let's start by removing the sidebar and the status bar. They take up space on the screen and do not provide any useful information. Open the settings file: {{< kbd "Cmd+Shift+P" >}} ({{< kbd "Ctrl+Shift+P" >}} if you are on _Windows_ or _Linux_) and type _Open settings (JSON)_. In the file that opens, we will add our settings:

```json
{
"workbench.activityBar.visible": false,
"workbench.statusBar.visible": false,
}
```

Panels can be accessed from the keyboard:
+ {{< kbd "Cmd+P" >}} - open file in project
+ {{< kbd "Cmd+B" >}} - enable or disable the sidebar
+ {{< kbd "Cmd+Shift+E" >}} - navigation bar for project files
+ {{< kbd "Cmd+Shift+F" >}} - search bar in files
+ {{< kbd "Cmd+Shift+D" >}} - debug panel
+ {{< kbd "Cmd+Shift+X" >}} - extensions panel
+ {{< kbd "Ctrl+Shift+G" >}} - repositories panel

### Tabs

Disable tabs and additional information displayed on them.

```json
"workbench.editor.showTabs": false,
"workbench.editor.tabCloseButton": "off",
"workbench.editor.showIcons": false,
"workbench.editor.decorations.badges": false,
"workbench.editor.decorations.colors": false,
```

You can move between editor groups using {{< kbd "Cmd+1" >}}, {{< kbd "Cmd+2" >}} keys and so on (if the group has not been opened, it will be created). You can open a tab in the active editor group with {{< kbd "Ctrl+Tab" >}} or {{< kbd "Ctrl+1" >}}, {{< kbd "Ctrl+2" >}} and so on.

### Panels

Let's remove unnecessary arrows and nesting level indicators for panels displaying trees (file navigation, commits, and so on). Also, install nice [icons](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme).

```json
"workbench.iconTheme": "material-icon-theme",
"workbench.tree.renderIndentGuides": "none",
"material-icon-theme.hidesExplorerArrows": true,
```

Clean up the Files panel:

``` json
"explorer.excludeGitIgnore": true,
"explorer.openEditors.visible": 0,
"explorer.confirmDragAndDrop": false,
"explorer.autoReveal": false,
```

## Editor

Now let's move on to the main file editor. It contains a lot of visual noise.

```json
"editor.minimap.enabled": false,
"editor.renderWhitespace": "none",
```

Turn off the highlighting of the active line and other noise:

```json
"editor.renderLineHighlight": "none",
"editor.overviewRulerBorder": false,
"editor.hideCursorInOverviewRuler": true,
"editor.occurrencesHighlight": false,

// Scrollbars
"editor.scrollbar.horizontal": "hidden",
"editor.scrollbar.vertical": "auto",
"editor.scrollbar.verticalScrollbarSize": 6,

// Misc
"editor.guides.indentation": false,
"editor.folding": false,
"editor.glyphMargin": false,
"editor.lineNumbers": "off",
```

Font. Here you can customize it as you like:

```json
"editor.fontFamily": "Fira Code",
"editor.fontLigatures": true,
"editor.fontSize": 13,
"editor.lineHeight": 18,
```

## Breadcrumbs

Disable breadcrumbs:

```json
"breadcrumbs.enabled": false,
"breadcrumbs.icons": false,
```