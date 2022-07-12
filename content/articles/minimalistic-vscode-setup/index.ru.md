---
title: "Минималистичный интерфейс в VSCode"
date: 2022-07-08T10:35:00Z
draft: false
tags: ['vscode']
description: _VSCode_ отличный редактор кода, однако его интерфейс изобилует огромным количеством ненужных вещей и переполнен информационным шумом. В этой статье попробуем убрать всё лишнее из интерфейса, а также найти сочетания клавиш чтобы к этому ненужному можно было быстро получить доступ.
---

Начнём с того что уберём ненужную боковую панель активностей и строку состояния. Они занимают пространство на экране и не несут какой-либо полезной информации. Откройте файл настроек: `Cmd+Shift+P` (`Ctrl+Shift+P` если у вас _Windows_ или _Linux_) и наберите `Open settings (JSON)`. В открывшейся файл и будем добавлять свои настройки:

```json
"workbench.activityBar.visible": false,
"workbench.statusBar.visible": false,
```

Доступ к панелям можно получить с клавиатуры:
+ `Cmd+P` - открыть файл в проекте
+ `Cmd+B` - включить или отключить боковую панель
+ `Cmd+Shift+E` - панель навигации по файлам проекта
+ `Cmd+Shift+F` - панель поиска в файлах
+ `Cmd+Shift+D` - панель отладки
+ `Cmd+Shift+X` - панель расширений
+ `Ctrl+Shift+G` - панель репозиториев

### Вкладки

Отключим вкладки и дополнительную информацию, отображающуюся на них.

```json
"workbench.editor.showTabs": false, // оставьте true если вкладки вам нужны
"workbench.editor.tabCloseButton": "off",
"workbench.editor.showIcons": false,
"workbench.editor.decorations.badges": false,
"workbench.editor.decorations.colors": false,
```

Перемещаться между активными группами редакторов можно с помощью клавиш `Cmd+1`, `Cmd+2` и так далее (если группа не была открыта, то она будет создана). Открыть вкладку в активном редакторе можно с `Ctrl+Tab` или `Ctrl+1`, `Ctrl+2` и так далее.

### Панели

Уберём лишние стрелки и индикаторы уровня вложенности для панелей отображающих деревья (навигация файлов, коммиты и так далее). Заодно поставим приличные [иконки](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme).

```json
// material-icon-theme - эта тема умеет скрывать стрелки открытия закрытия узла дерева
"workbench.iconTheme": "material-icon-theme",
"workbench.tree.renderIndentGuides": "none",
"material-icon-theme.hidesExplorerArrows": true, // Это настройка плагина!
```

Почистим панель отображения файлов:

``` json
// Всё что заигнорировано в git, не будет отображаться
"explorer.excludeGitIgnore": true,
"explorer.openEditors.visible": 0,
"explorer.confirmDragAndDrop": false,
"explorer.autoReveal": false,
```

## Редактор

Теперь перейдём к основному – редактору файлов. Он содержит наибольшее количество визуального шума.

```json
"editor.minimap.enabled": false, // Да кому она нужна вообще?
"editor.renderWhitespace": "none",
```

Отключим подсветку активной строки и прочего мусора:

```json
"editor.renderLineHighlight": "none",
"editor.overviewRulerBorder": false,
"editor.hideCursorInOverviewRuler": true,
"editor.occurrencesHighlight": false,

// Полосы прокрутки
"editor.scrollbar.horizontal": "hidden",
"editor.scrollbar.vertical": "auto",
"editor.scrollbar.verticalScrollbarSize": 6,

// Уровень вложенности
"editor.guides.indentation": false,

// Метки сворачивания кода. Вы теперь не сможете его свернуть.
"editor.folding": false,

// Прочие символы (точки остановки, запуск тестов и так далее)
"editor.glyphMargin": false,

// Номера строк.
"editor.lineNumbers": "off",
```

Шрифт. Тут на своё усмотрение. Включим также лигатуры.

```json
"editor.fontFamily": "Fira Code",
"editor.fontLigatures": true,
"editor.fontSize": 13,
"editor.lineHeight": 18,
```

## Хлебные крошки

Отключим хлебные крошки:

```json
"breadcrumbs.enabled": false,
"breadcrumbs.icons": false,
```