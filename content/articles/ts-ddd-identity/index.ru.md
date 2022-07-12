---
title: "Identity ValueObject для DDD в Type Script"
date: 2022-06-17T16:25:13Z
draft: false
tags: ['ddd', 'ts']
---

Допустим мы разрабатываем простейшее приложение – менеджер задач. У нас будет всего две сущности: проект и задача. В качестве идентификаторов объектов будем использовать `int` (ведь обычно мы сохраняем в реляционную базу, то почему бы и нет?). Задачу можно назначить на проект и для этого у нас есть простой интерфейс:

```ts
function assignTaskToProject(taskId: number, projectId: number) {}
```

И где-то в обработчике событий _UI_ подальше от описанного выше интерфейса мы делаем следующее:

```ts
const projectId = project.id
const taskId = tasks.getUnassigned().first().id
assignTaskToProject(projectId, taskId)
```
Без подглядывания в описание интерфейса – какие тут проблемы?
1. Во-первых, аргументы перепутаны местами и код не будет работать так как от него ожидают.
2. Во-вторых, этот код скомпилируется и мы узнаем об ошибке только в _runtime_. Хорошо если не на _production_.
3. В-третьих, (в особо запущенных случаях, и я уверен у вас были такие случаи) отладка такого кода может занять достаточно времени.

Чего бы хотелось? Чтобы компилятор проверял такого рода ошибки и запрещал передавать/назначать идентификатор одного типа (для проектов, например) как идентификатор другого типа (задач, например).

<!--more-->

## Наивное решение

Для начала создадим простой _ValueObject_ для хранения _Id_.

```ts
class Identity<TType> {
  constructor(private readonly value: TType) { }

  equals(other: Identity<TType>): boolean {
    return this.value === other.value
  }
}
```

Теперь реализуем несколько идентификаторы, для наших сущностей и обновим интерфейс, чтобы он принимал идентификаторы только определённого типа.

```ts
class TaskId extends Identity<number> { }
class ProjectId extends Identity<number> { }

// обновим интерфейс
function assignTaskToProject(taskId: TaskId, projectId: ProjectId) {}
```

Теперь попробуем сделать что-нибудь плохое и передать неправильный идентификатор. И да это сработает, компилятор не будет ругаться.

```ts
assignTaskToProject(new TaskId(42), new TaskId(42)) // it works O_O

// И даже так можно
const taskId = new TaskId(42) as ProjectId
const wtfId = new Identity<number>(42) as ProjectId as TaskId
```

Почему это работает? Чтобы это понять, рассмотрим разные системы типов: _номинальную_ и _структурную_. _Номинальная_ система типов использует имя типа для проверки эквивалентности. _Структураная_ же система прверяет структуру типа: имя свойств и их тип. Для _номинальной_ системы типов существует разница между `TypeId` и `TaskId`, потому что они имеют разные имена. Но между этими типами нет никакой разницы для _структурной_ системы типов (и _TypeSrcipt_ использует её), потому что они имеют одни и те же свойства тех же типов. Итак, чтобы сделать эти типы отличными друг от друга, мы должны изменить их структуру.

## Брендирование типов

Немного модифицируем наш класс, добавив "брендирование":

```ts
class Identity<TType, Brand> {
  equals(other: Identity<TType, Brand>): boolean {
    return this.value === other.value
  }
  private __brand__: Brand // вся магия тут
}
```

Попробуем сделать что-то плохое ещё раз:

```ts
assignTaskToProject(new TaskId(42), new TaskId(42))

// Error: Argument of type 'TaskId' is not assignable to parameter of type 'ProjectId'. Type '"TaskId"' is not assignable to type '"ProjectId"'.

const taskId = new TaskId(42) as ProjectId

// Error: Conversion of type 'TaskId' to type 'ProjectId' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first. Type '"TaskId"' is not comparable to type '"ProjectId"'
```

Теперь компилятор будет проверять типы идентификаторов и выдаст ошибку компиляции. Это может уберечь нас от глупых ошибок.

