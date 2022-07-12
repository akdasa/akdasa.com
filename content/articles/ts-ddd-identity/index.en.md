---
title: "Identity ValueObject, DDD and TypeScript"
date: 2022-06-17T16:25:13Z
draft: false
tags: ['ddd', 'ts']
---

Let's say we are developing a simple application - a task manager. We will have only two entities: a project and a task. We will use `int` as object identifiers (after all, we usually save to a relational database, why not?). A task can be assigned to a project and we have a simple interface for this:

```ts
function assignTaskToProject(taskId: number, projectId: number) {}
```

We do the following somewhere in the _UI_ event handler:

```ts
const projectId = project.id
const taskId = tasks.getUnassigned().first().id
assignTaskToProject(projectId, taskId)
```

Without looking at the interface - what are the problems here?
1. The arguments are reversed and the code will not work as expected.
2. This code will compile and we will know about an error only at _runtime_. (Good, if not on _production_)
3. Debugging and finding the issue can take quite a while (I'm sure you have had such an experience).

What do we need? The compiler must check for such errors and forbids passing / assigning an identifier of one type (for projects, for example) as an identifier of another type (tasks, for example). How we can accomplish that?

<!--more-->

## Naive solution

Let's create a simple _ValueObject_ to store _Id_.

```ts
class Identity<TType> {
  constructor(private readonly value: TType) { }

  equals(other: Identity<TType>): boolean {
    return this.value === other.value
  }
}
```

Now let's implement multiple identifiers for our entities and update the interface to only accept identifiers of a certain type.

```ts
class TaskId extends Identity<number> { }
class ProjectId extends Identity<number> { }

// update this also
function assignTaskToProject(taskId: TaskId, projectId: ProjectId) {}
```

Now let's try to do something bad and pass the wrong ID. And yes it will work:

```ts
assignTaskToProject(new TaskId(42), new TaskId(42)) // it works O_O

// you can even do that
const taskId = new TaskId(42) as ProjectId
const wtfId = new Identity<number>(42) as ProjectId as TaskId
```

Why does it work? To understand that, let's look at different type-systems: _nominal_ and _structural_. A _nominal_ type-system uses the name of the type to check for equivalence. A _structural_ type-system uses the structure of the type: name, and type of the properties, to assert equality between types. For the _nominal_ type-system, there is a difference between `TypeId` and `TaskId` because they have different names. But there is no difference between these types for _structural_ type-system (and _TypeSrcipt_ uses it) because they have the same properties of the same types. So to make these types different from each other, we have to change their structure.

## Type branding

Let's slightly modify our class by adding "branding" to the `Identity`:

```ts
class Identity<TType, Brand> {
  equals(other: Identity<TType, Brand>): boolean {
    return this.value === other.value
  }
  private __brand__: Brand // magic
}
```

Let's try to do something bad again:

```ts
assignTaskToProject(new TaskId(42), new TaskId(42))

// Error: Argument of type 'TaskId' is not assignable to parameter of type 'ProjectId'. Type '"TaskId"' is not assignable to type '"ProjectId"'.

const taskId = new TaskId(42) as ProjectId

// Error: Conversion of type 'TaskId' to type 'ProjectId' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first. Type '"TaskId"' is not comparable to type '"ProjectId"'
```

Now the compiler checks the types of identifiers and shows an error. This can save us from stupid mistakes.
