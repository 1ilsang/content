---
title: Beginning our Angular todo list app
slug: Learn_web_development/Core/Frameworks_libraries/Angular_todo_list_beginning
page-type: learn-module-chapter
sidebar: learnsidebar
---

{{PreviousMenuNext("Learn_web_development/Core/Frameworks_libraries/Angular_getting_started","Learn_web_development/Core/Frameworks_libraries/Angular_styling", "Learn_web_development/Core/Frameworks_libraries")}}

At this point, we are ready to start creating our to-do list application using Angular. The finished application will display a list of to-do items and includes editing, deleting, and adding features. In this article you will get to know your application structure, and work up to displaying a basic list of to-do items.

<table>
  <tbody>
    <tr>
      <th scope="row">Prerequisites:</th>
      <td>
        Familiarity with the core <a href="/en-US/docs/Learn_web_development/Core/Structuring_content">HTML</a>,
        <a href="/en-US/docs/Learn_web_development/Core/Styling_basics">CSS</a>, and
        <a href="/en-US/docs/Learn_web_development/Core/Scripting">JavaScript</a> languages,
        knowledge of the
        <a
          href="/en-US/docs/Learn_web_development/Getting_started/Environment_setup/Command_line"
          >terminal/command line</a
        >.
      </td>
    </tr>
    <tr>
      <th scope="row">Objective:</th>
      <td>
        To create our basic app structure, get it displaying a list of to-do
        items, and understand fundamental Angular concepts such as component
        structure, sharing data between components, and looping content
        creation.
      </td>
    </tr>
  </tbody>
</table>

## The to-do application structure

Like any web application, an Angular application has an `index.html` as the entry point. The `index.html` actually is the app's top level HTML template:

```html
<!doctype html>
<html lang="en">
  <head>
    <!-- ... -->
  </head>
  <body>
    <app-root></app-root>
  </body>
</html>
```

Within the `<body>` tag, Angular uses a special element — `<app-root>` — to insert your main component, which in turn includes other components you create.
Generally, you don't need to touch the `index.html`, and you mostly focus your work within specialized areas of your application called components.

### Organize your application with components

Components are a central building block of Angular applications.
This to-do application has two components — a component as a foundation for your application, and a component for handling to-do items.

Each component is made up of a TypeScript class, HTML, and CSS.
TypeScript transpiles, or converts, into JavaScript, which means that your application ultimately ends up in plain JavaScript but you have the convenience of using TypeScript's extended features and streamlined syntax.

### Control flow with @if and @for blocks

This tutorial covers two important Angular [control flow blocks](https://angular.dev/guide/templates/control-flow) which tell the framework when and how your templates should be rendered.
The first block that this tutorial covers is the [`@for`](https://angular.dev/api/core/@for) block which loops through a collection and repeatedly renders the content of a block.

The second block that you learn in this tutorial is [`@if`](https://angular.dev/api/core/@if).
You can use `@if` to display content based on a condition.
For example, if a user clicks an "edit" button, you can show elements used to edit an item.
If a user clicks "cancel", you can remove the elements used for editing.

### Share data between components

In this to-do application, you configure your components to share data.
To add new items to the to do list, the main component has to send the new item to the second component.
This second component manages the items and takes care of editing, marking as done, and deleting individual items.

You accomplish sharing data between Angular components with special decorators called `@Input()` and `@Output()`.
You use these decorators to specify that certain properties allow data to go into or out of a component.
To use `@Output()`, you raise an event in one component so that the other component knows that there is data available.

## Define Item

In the `app` directory, create a new file named `item.ts` with the following contents:

```ts
export interface Item {
  description: string;
  done: boolean;
}
```

You won't use this file until [later](/en-US/docs/Learn_web_development/Core/Frameworks_libraries/Angular_item_component#add_logic_to_itemcomponent), but it is a good time to know and record your knowledge of what an `item` is. The `Item` interface creates an `item` object model so that your application will understand what an `item` is. For this to-do list, an `item` is an object that has a description and can be marked done.

## Add logic to AppComponent

Now that you know what an `item` is, you can give your application some items by adding them to the app.
In `app.component.ts`, replace the contents with the following:

```ts
import { Component } from "@angular/core";
import { CommonModule } from "@angular/common";

@Component({
  standalone: true,
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrl: "./app.component.css",
  imports: [CommonModule],
})
export class AppComponent {
  componentTitle = "My To Do List";

  filter: "all" | "active" | "done" = "all";

  allItems = [
    { description: "eat", done: true },
    { description: "sleep", done: false },
    { description: "play", done: false },
    { description: "laugh", done: false },
  ];

  get items() {
    if (this.filter === "all") {
      return this.allItems;
    }
    return this.allItems.filter((item) =>
      this.filter === "done" ? item.done : !item.done,
    );
  }
}
```

The first two lines are JavaScript imports. In this case they are importing Angular libraries.
The `@Component()` decorator specifies metadata about the `AppComponent`.
Here's some more information about the metadata we're using:

- [`standalone`](https://angular.dev/api/core/Component#standalone): Describe whether the component requires a [NgModule](https://angular.dev/guide/ngmodules#the-basic-ngmodule) or not.
  Your app will directly manage template dependencies (components, directives, etc.) using imports when it's a standalone.
- [`selector`](https://angular.dev/api/core/Directive#selector): Tells you the CSS selector that you use in a template to place this component. Here it is `'app-root'`.
  In the `index.html`, within the `body` tag, the Angular CLI added `<app-root></app-root>` when generating your application.
  You use all component selectors in the same way by adding them to other component HTML templates.
- [`templateUrl`](https://angular.dev/api/core/Component#templateUrl): Specifies the HTML file to associate with this component.
  Here it is, `'./app.component.html'`,
- [`styleUrl`](https://angular.dev/api/core/Component#styleUrl): Provides the location and name of the file for your styles that apply specifically to this component. Here it is `'./app.component.css'`.
- [`imports`](https://angular.dev/api/core/Component#imports): Allows you to specify the component's dependencies that can be used within its template.

The `filter` property is of type `union`, which means `filter` could have the value of `all`, `active`, or `done`.
With the `union` type, if you make a typo in the value you assign to the `filter` property, TypeScript lets you know so that you can catch the bug early.
This guide shows you how to add filtering in a later step, but you can also use a filter to show the default list of all the items.

The `allItems` array contains the to-do items and whether they are done.
The first item, `eat`, has a `done` value of true.

The getter, `get items()`, retrieves the items from the `allItems` array if the `filter` is equal to `all`. Otherwise, `get items()` returns the done or pending items depending on how the user filters the view.
The getter also establishes the name of the array as `items`, which you'll use in the next section.

## Add HTML to the AppComponent template

To see the list of items in the browser, replace the contents of `app.component.html` with the following HTML:

```html
<div class="main">
  <h1>\{{ componentTitle }}</h1>
  <h2>What would you like to do today?</h2>

  <ul>
    @for(item of items; track item.description){
    <li>\{{item.description}}</li>
    }
  </ul>
</div>
```

The `<li>` is inside a `@for` block that iterates over the items in the `items` array.
For each item, a new `<li>` is created.
The double curly braces that contain `item.description` instructs Angular to populate each `<li>` with the text of each item's description.

The `track` keyword in Angular's `@for` block helps Angular identify which items in an array have changed, been added, or removed.
This makes it easier and faster for Angular to update the DOM when the array is modified.

In the browser, you should see the list of items as follows:

```plain
My To Do List
What would you like to do today?

* eat
* sleep
* play
* laugh
```

## Add items to the list

A to-do list needs a way to add items, so let's get started.
In `app.component.ts`, add the following method to the class after the `allItems` array:

```ts
export class AppComponent {
  // …
  addItem(description: string) {
    if (!description) return;

    this.allItems.unshift({
      description,
      done: false,
    });
  }
  // …
}
```

The `addItem()` method takes an item that the user provides and adds it to the array when the user clicks the **Add** button.
The `addItem()` method uses the array method `unshift()` to add a new item to the beginning of the array and the top of the list.
You could alternatively use `push()`, which would add the new item to the end of the array and the bottom of the list.

To use the `addItem()` method, edit the HTML in the `AppComponent` template.
In `app.component.html`, replace the `<h2>` with the following:

```html
<label for="addItemInput">What would you like to do today?</label>

<input
  #newItem
  placeholder="add an item"
  (keyup.enter)="addItem(newItem.value); newItem.value = ''"
  class="lg-text-input"
  id="addItemInput" />

<button class="btn-primary" (click)="addItem(newItem.value)">Add</button>
```

In the above HTML, `#newItem` is a template variable. The template variable in this case uses the `<input>` element as its value. Template variables can be referred to anywhere in the component's template.

When the user types a new item in the `<input>` field and presses **Enter**, the `addItem()` method adds the value to the `allItems` array.
Pressing the **Enter** key also resets the value of `<input>` to an empty string. The template variable `#newItem` is used to access the value of the `<input>` element in the template.
Instead of pressing the **Enter** key, the user can also click the **Add** button, which calls the same `addItem()` method.

## Summary

By now you should have your basic list of to-dos displaying in your browser. That's great progress! Of course, we have a lot more to do. In the next article we will look at adding some styling to our application.

{{PreviousMenuNext("Learn_web_development/Core/Frameworks_libraries/Angular_getting_started","Learn_web_development/Core/Frameworks_libraries/Angular_styling", "Learn_web_development/Core/Frameworks_libraries")}}
