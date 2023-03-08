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

On the one side I absolutly love Flutter. On the other side, I absolutly hate it.

A couple of weeks ago I messaged a collegue of mine (greetings to you Nic if you are reading this) like I did a couple of times before and complained about the sheer amount of **boilerplate** code you have to write or **generate**.


# Code Generation

I absolutly hate it. You have SO MANY FILES in your project.

For example if you are using [Freezed](https://pub.dev/packages/freezed) your project will look like this:

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

Then, if you also want to use [Retrofit](https://pub.dev/packages/retrofit) you'll need another `.g.dart` file.
Same goes for [Serializing JSON](https://pub.dev/packages/json_serializable), the new [Riverpod Code Generation](https://pub.dev/packages/riverpod) and so on.


# Widget Trees

Lately, because of work I'm doing a lot of web development and the code just is so much cleaner.

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

At this point, it's still very much okay.

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

Now it's way more code. But let's take a look at a last example:

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

You see this? It's so much code.

Of course, this example could be simplified by splitting the code in seperate widgets, but that's the case with both languages / tools.

# Will I be using Flutter in the future?
Yes. Although I dont like writing much more code than I'm used to in other languages, I the developer experience is incredible. It's so easy to to create layouts and the hot reload is just amazing.