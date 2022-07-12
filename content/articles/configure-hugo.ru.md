---
title: "Настройка рабочей среды VSCode для Hugo"
date: 2022-04-10T12:02:13Z
draft: true
---

В этой статье рассмотрим как развернуть окружение для Hugo в контейнере Docker и разрабатывать сайт с использование VSCode ибо так удобнее: проще воспроизводить оркужение после переустановки, не захламляет систему и обеспечивает безопасность если вдруг в какой-то пакет/продукт проникнет зловредный код.

 <!--more-->

Создадим новый проект и внутри организуем следующую структуру:

```
.devcontainer
  |- devcontainer.json
  +- Dockerfile
```

Содержимое `Dockerfile` довольно простое – мы всего лишь устанавливаем _Hugo_ в базовый контейнер _Alpine_.

```Dockerfile
FROM alpine:3.14
RUN apk add --no-cache hugo
WORKDIR /my-site
```

Содержимое файла `devcontainer.json` также просто:

```json
{
  "name": "my-site",
  "build": {
    "dockerfile": "Dockerfile",
  },
  "workspaceFolder": "/my-site",
  "workspaceMount": "source=${localWorkspaceFolder},target=/my-site,type=bind,consistency=cached",
}
```

Далее переоткрываем проект в контейнере <kbd>cmd+p</kbd>`cmd+p`