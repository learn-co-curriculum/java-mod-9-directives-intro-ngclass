# Directives Introduction and ngClass

## Learning Goals

- Give an overview of the different directives.
- Explain the ngClass directive.

## Introduction

There are two main types of directives in Angular:

1. Attribute directives, which do not change the structure of an HTML page (i.e.
   they don't change the DOM) and only impact the element they are associated
   with. These have the form `ng<DirectiveName>`, e.g. `ngClass` or `ngModel`
2. Structure directives, which do have the potential to change the DOM, as they
   can remove or add elements to your page. These have the form
   `*ng<DirectiveName>`, e.g. `*ngFor` or `*ngIf`

The most common built-in directives are:

1. `ngClass`: conditionally assign a class to an HTML attribute
2. `ngStyle`: conditionally assign a CSS style to an HTML attribute
3. `ngModel`: create a two-data binding between an HTML attribute and a value in
   your component's model

## ngClass

Consider our sender message component. We might want to change the background of
the "avatar" to a different color to indicate that the user is online. We cannot
do this with the traditional `class` CSS attribute because we want the
formatting to be a function of the user's status, which will be driven by our
application. This is where `ngClass` comes in handy, because it allows us to
specify a conditional expression and assign a CSS class based on the value of
that expression.

The `ngClass` syntax is as follows:

`<span [ngClass]="booleanExpression ? 'classIfTrue' : 'classIfFalse'">Sample Text</span>`

Both classes must be valid CSS classes from one of your stylesheets.

For our sender message component, it currently looks like this:

```html
<div class="container">
  <div class="row">
    <div class="col-1 p-3">
      <span class="badge bg-secondary"> {{message.sender.firstName[0]}} </span>
    </div>
    <div class="col-8 p-3 border rounded-5">
      <span>{{message.text}}</span>
    </div>
  </div>
</div>
```

The background is always the secondary color `bg-secondary`. To make it
conditional, we might change it like this:

```html
<div class="container">
  <div class="row">
    <div class="col-1 p-3">
      <span
        class="badge"
        [ngClass]="message.sender.isOnline ? 'bg-primary' : 'bg-secondary'"
      >
        {{message.sender.firstName[0]}}
      </span>
    </div>
    <div class="col-8 p-3 border rounded-5">
      <span>{{message.text}}</span>
    </div>
  </div>
</div>
```

Where the class is now conditionally either `bg-primary` or `bg-secondary` based
on the value of `message.sender.isOnline`.

For this to work, we do have to add `isOnline` as a new `boolean` attribute to
the `User` model in `user.model.ts`:

```typescript
export class User {
  constructor(public firstName: string, public isOnline?: boolean) {}
}
```

> Note that we're adding `isOnline` as an optional attribute, so that it doesn't
> break areas of our code where we don't need this attribute for our logic

With those 2 changes, your application won't look any different, since all your
existing users do not have the `isOnline` attribute, which means it evaluates to
`false` in our `ngClass` conditional styling.

To see this work, let's change one of our users in our thread component to make
it be online. We do this in `conversation-thread-component.component.ts`:

```typescript
import { Component, OnInit } from "@angular/core";
import { Message } from "src/app/message.model";

@Component({
  selector: "app-conversation-thread-component",
  templateUrl: "./conversation-thread-component.component.html",
  styleUrls: ["./conversation-thread-component.component.css"],
})
export class ConversationThreadComponentComponent implements OnInit {
  senderMessages: Message[] = [
    {
      sender: { firstName: "Ludovic", isOnline: true },
      text: "Message from Ludovic",
      conversationId: 1,
      sequenceNumber: 0,
    },
    {
      sender: { firstName: "Jessica" },
      text: "Message from Jessica",
      conversationId: 1,
      sequenceNumber: 1,
    },
  ];

  userMessages: Message[] = [
    {
      sender: { firstName: "Aurelie" },
      text: "Message from Aurelie",
      conversationId: 1,
      sequenceNumber: 2,
    },
  ];
  constructor() {}

  ngOnInit(): void {}
}
```

Now the message bubble for "Ludovic" should show with a "primary"
background-color to indicate that he is online:

![User Online Conditional Formatting](https://curriculum-content.s3.amazonaws.com/java-mod-8/ng-messaging-user-online.png)
