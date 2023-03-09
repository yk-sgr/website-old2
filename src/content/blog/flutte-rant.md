---
author: Yannick Seeger
pubDatetime: 2023-03-08T23:49:24+02:00
title: Flutter Rant
featured: false
draft: false
tags:
  - flutter
  - rant
ogImage: ""
description:
  A rant about flutter.
---

I have a love-hate relationship with Flutter. On one hand, I love it, but on the other hand, I hate it.

A few weeks ago, I messaged my colleague Nic (if you're reading this, hello!) to complain about the sheer amount of boilerplate code that you have to write or generate.

# Code Generation

I hate it. There are so many files in your project. For example, if you use [Freezed](https://pub.dev/packages/freezed), your project will look like this:

```bash
blog.dart
blog.freezed.dart
blog.g.dart
post.dart
post.freezed.dart
post.g.dart
user.dart
user.freezed.dart
user.g.dart
comment.dart
comment.freezed.dart
comment.g.dart
```

If you also want to use [Retrofit](https://pub.dev/packages/retrofit) you'll need another `.g.dart` file.
The same goes for [Serializing JSON](https://pub.dev/packages/json_serializable), the new [Riverpod Code Generation](https://pub.dev/packages/riverpod) and so on.


# Widget Trees

Lately, I've been doing a lot of web development for work, and the code is so much cleaner:

```html
<div>
  <p>Hello World!</p>
</div>
```

```dart
Container(
  child: Text("Hello World!"),
)
```

So far, so good.

```html
<div class="flex flex-col">
  <div class="flex">
    <p>1.1</p>
    <p>1.2</p>
  </div>
  <div class="flex">
    <p>2.1</p>
    <p>2.2</p>
  </div>
</div>
```

```dart
Column(
  children: [
    Row(
      children: [
        Text("1.1"),
        Text("1.2"),
      ],
    ),
    Row(
      children: [
        Text("2.1"),
        Text("2.2"),
      ],
    ),
  ],
)
```

Now it's a lot more code. But let's take a look at one more example:

```html
<div class="flex flex-col">
  <div class="flex">
    <p class="text-red-500 text-2xl">1.1</p>
    <p class="text-red-500 text-2xl">1.2</p>
  </div>
  <div class="flex">
    <p class="text-red-500 text-2xl">2.1</p>
    <p class="text-red-500 text-2xl">2.2</p>
  </div>
</div>
```

```dart
Column(
  children: [
    Row(
      children: [
        Text(
          "1.1",
          style: TextStyle(
            color: Colors.red,
            fontSize: 20,
          ),
        ),
        Text(
          "1.2",
          style: TextStyle(
            color: Colors.red,
            fontSize: 20,
          ),
        ),
      ],
    ),
    Row(
      children: [
        Text(
          "2.1",
          style: TextStyle(
            color: Colors.red,
            fontSize: 20,
          ),
        ),
        Text(
          "2.2",
          style: TextStyle(
            color: Colors.red,
            fontSize: 20,
          ),
        ),
      ],
    ),
  ],
)
```

Look at all that boilerplate code...


Do you see this? It's an excessive amount of code. However, this example could be simplified by dividing the code into separate widgets, which is a common practice in both languages/tools.

As for whether I will continue to use Flutter in the future, the answer is yes. Even though I don't prefer writing significantly more code than what I'm accustomed to in other languages, the overall developer experience is remarkable. The process of creating layouts is simple, and the hot reload feature is simply amazing.