---
layout: page
title: Flutter FAQ
sidebar: home_sidebar
permalink: /faq/
---

* Placeholder for TOC
{:toc}

## What does Flutter do?

Flutter gives developers an easy and productive way to build and deploy
cross-platform, high-performance mobile apps on both Android and iOS.

Flutter gives users beautiful, fast, and jitter-free app experiences.

## What does Flutter provide?

Flutter has four main components:

* a heavily optimized, mobile-first 2D rendering engine (with excellent support
for text)
* a functional-reactive framework (optional, you can bring-your-own
framework)
* a set of Material Design widgets (optional, you can bring-your-own
widgets)
* command-line tools and a plugin for Atom

## What makes Flutter unique?

Flutter is different than most other options for building cross-platform
mobile apps because Flutter uses neither WebView nor the OEM widgets
that shipped with the device. Instead, Flutter uses its own high-performance
rendering engine to draw widgets.

Flutter also offers developers a highly productive and fast development
experience, fast runtime and engine performance, and beautifully designed
widgets that make for beautiful apps.

## Why would I want to invest in learning Flutter?

Flutter is an easy way to use a single codebase to deliver beautiful mobile
apps that run on both Android and iOS. Flutter gives developers quick
edit/debug cycles for an enjoyable low-friction workflow.

## What are Flutter's guiding principles?

We believe that:

* In order to reach every potential user, developers need to target multiple
mobile platforms.
* HTML and WebViews as they exist today make it challenging to
consistently hit high frame rates and deliver high-fidelity experiences, due to
automatic behavior (scrolling, layout) and legacy support.
* Today, it's too
costly to build the same app multiple times: it requires different teams,
different code bases, different workflows, different tools, etc.
* Developers want
an easier, better way to use a single codebase to build mobile apps for multiple
target platforms, and they don't want to sacrifice quality, control, or
performance.

We are focused on three things:

* _Control_ - Developers deserve access to, and control over, all layers of the
system. Which leads to:
* _Performance_ - Users deserve perfectly fluid, responsive,
jank-free apps. Which leads to:
* _Fidelity_ - Everyone deserves precise, beautiful,
delightful mobile app experiences.

## How is Flutter related to Sky?

Sky was the codename of an earlier version of Flutter.

## What devices and OS versions does Flutter run on?

Mobile operating systems: Android Jelly Bean, v16, 4.1.x or newer, and
iOS 8 or newer.

Mobile hardware: 64-bit iOS devices (for example, iPhone 5S and newer),
and ARM Android devices.

We support developing Flutter apps with Android and iOS devices, as
well as with Android emulators and the iOS simulator.

We test on a variety of low-end to high-end phones (excluding tablets)
but we don't yet have an official device compatibility guarantee.
We do not offer support for tablets or have tablet-aware layouts.

## What technology is Flutter built with?

Flutter is built with C, C++, Dart, Skia (a 2D rendering engine),
[Mojo IPC](https://github.com/domokit/mojo), and
Blink's text rendering system. See this [architecture diagram](https://docs.google.com/presentation/d/1cw7A4HbvM_Abv320rVgPVGiUP2msVs7tfGbkgdrTy0I/edit#slide=id.gbb3c3233b_0_162) for a better
picture of the main components.

## How does Flutter run my code on Android?

The engine's C/C++ code is compiled with Android's NDK, and the majority of the
framework and application code is running as native code
compiled by the Dart compiler.

## How does Flutter run my code on iOS?

The engine's C/C++ code is compiled with LLVM, and any Dart code is AOT-compiled
into native code. The app runs using the native instruction set (no interpreter
is involved).

## Does Flutter run on the web?

No. We do not plan to provide a web version of Flutter.

## What operating systems can I use to build a Flutter app?

Flutter supports development on Linux and Mac. Windows support is planned.

## What kinds of apps can I build with Flutter?

Flutter is optimized for 2D mobile apps that want to run on both Android and
iOS. Apps that use Material Design are particularly well suited for Flutter.

## What kind of app performance can I expect?

You can expect excellent performance. Flutter is
designed to help developers easily achieve a constant 60fps. Flutter apps run
via natively compiled code – no interpreters are involved.

## Can I use Flutter to build desktop apps?

We are focused on mobile-first use cases. However, Flutter is open source and we
encourage the community to use Flutter in a variety of interesting ways.

## Can I use Flutter inside of my existing native app?

Yes, you can embed a Flutter view in your existing Android or iOS app.
You can learn more about this at [[docs coming soon]]. If you want to do this,
we encourage you to email our mailing list:
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## Can I access native services and APIs like sensors and local storage?

Yes. Flutter gives developers out-of-the-box access to _some_ services
and APIs from the operating system. However, we want to avoid the
"lowest common denominator" problem with most cross-platform APIs, so we
do not intend to build cross-platform APIs for all native services and APIs.

We encourage developers to use Flutter's asynchronous message passing system to
create your own integrations with
[platform and third-party APIs](/platform-services/). Developers
can expose as much or as little of the platform APIs as they need, and build
layers of abstractions that are a best fit for their project.

If you want to do this, we encourage you to email our
mailing list:
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## Can I interop with my mobile platform's default programming language?

Yes, though this feature is a work in progress.

We are building a _message pipe_ between Flutter and the host
operating system. You can use this message pipe to send and receive
string data (typically JSON data) between your Flutter code
and Java (on Android) and ObjectiveC (on iOS).

Learn more about
[accessing platform and third-party services in Flutter](/platform-services/).

Here is an
[example project](https://github.com/flutter/flutter/tree/master/examples/hello_services)
that shows how to use the message pipe to access geolocation functionality
on Android. (An equivalent example project for iOS is in development.)

This is an evolving part of the system, and we're very interested
to learn how teams want to interop between
Flutter and the OS and third-party SDKs. We encourage you to email our
mailing list to let us know what you're building, and if you have
questions:
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## Does Flutter come with a framework?

Yes! Flutter ships with a functional-reactive style framework, inspired by
React. However, Flutter's framework is designed to be optional and layered.
Developers can choose to use only parts of the framework, or a different
framework.

## Does Flutter come with widgets?

Yes! Flutter ships with a set of high quality Material Design widgets, layouts,
and themes. You can see a collection of those widgets at [[docs coming soon]]. Of
course, these widgets are optional. Flutter is designed to make it easy to
create your own widgets, or customize the existing widgets.

## Why does Flutter not use the host platform's native widgets?

We are hoping the end-result will be higher quality apps. If we reused
the OEM widgets, the quality and performance of Flutter apps would be
limited by the quality of those widgets.

In Android, for example, there's a hard-coded set of gestures and fixed
rules for disambiguating them. In Flutter, you can write your
own gesture recognizer that is a first-class participant in the
[gesture system](/gestures/). Moreover, two widgets
authored by different people can coordinate to disambiguate gestures.

## Can I extend and customize the bundled widgets?

Absolutely. Flutter's widget system was designed to be easily customizable. You
can see an example of that at [[docs coming soon]].

## Does Flutter come with a testing framework?

Flutter apps are tested with the
[test package](https://pub.dartlang.org/packages/test). Learn more about
[testing with Flutter](/testing/).

## Does Flutter come with a dependency injection framework or solution?

Not at this time. Please share your ideas at
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## Does Flutter come with a reflection/mirrors system?

Not at this time. Because Flutter apps are pre-compiled for iOS, and binary size
is always a concern with mobile apps, we disabled dart:mirrors. We are curious
what you might need reflection/mirrors for – please let us know at
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## How do I do internationalization (i18n), localization (l10n), and accessibility (a11y) in Flutter?

Flutter has basic support for accessibility on iOS and Android, though this
feature is a work in progress.

Flutter developers are encouraged to use the
[intl package](https://pub.dartlang.org/packages/intl) for
internationalization and localization.

We encourage you to email
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com)
with your questions regarding these features.

## How do I write parallel and/or concurrent apps for Flutter?

Flutter supports isolates. Isolates are separate heaps in Flutter's VM, and they
are able to run in parallel (usually implemented as separate threads). Isolates
communicate by sending and receiving asynchronous messages. Flutter does not
currently have a shared-memory parallelism solution, although we are evaluating
solutions for this.

Check out an
[example of using isolates with Flutter](https://github.com/flutter/flutter/blob/master/examples/layers/services/isolate.dart).

## Can I use JSON/XML/protobuffers/etc with Flutter?

Absolutely. There are libraries in
[pub.dartlang.org](https://pub.dartlang.org) for JSON, XML,
protobufs, and many other utilities and formats.

## How do I write well-styled code for Flutter?

The code in the flutter repos follow our
[opinionated style guide](https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo).

Developers are not required to use this style guide for their own app
code, though.

## Does Flutter use my system's OEM widgets?

No. Flutter provides a set of Material Design widgets, managed and rendered by
Flutter's framework and engine.

## How do I deploy a Flutter app?

Flutter apps are most commonly deployed via the mobile platform's store (such as
Apple's App Store and Google's Play Store).

Our command-line tools can build an `.ipa` and `.apk` file for you.
To learn more, run `flutter build --help`.

## What if I don't want to build my app with Material Design?

Flutter's widget system is designed to be extensible, and it's supported and
possible to create your own set of widgets using our base classes.

## Does Flutter work with any editors or IDEs?

We are building a
[Flutter plugin for Atom](https://atom.io/packages/dartlang). Today, it
can syntax highlight, code
complete, refactor, launch apps, create new apps from a template, show type
hierarchies, jump to definition, and more.

There is also a supported
[Dart plugin for JetBrains editors](https://plugins.jetbrains.com/plugin/6351)
like IntelliJ and WebStorm, though this plugin does not have any specific
Flutter features.

## Can I build 3D (OpenGL) apps with Flutter?

Today we don't support for 3D via OpenGL ES or similar. We have long-term plans
to expose an optimized 3D API, but right now we're focused on 2D.

## How big is the Flutter engine?

In November 2015, we measured the size of a minimal Flutter app, bundled as
an APK, to be approximately 8MB. For this simple app that used Material Design
widgets, the core engine is approximately 5MB, the framework + app code is
approximately 400kb, necessary Java code is 330k, and there is approximately
2.5MB of ICU data. We are working to get this smaller.

## My app has a Slow Mode banner/ribbon in the upper right. Why am I seeing that?

By default `flutter run` command uses the debug build configuration.
The debug configuration enables type checking and asserts.
These checks help you catch errors early during development but impose
a runtime cost.  The "slow mode" banner indicates that these checks are enabled.
You can run your app without these checks by using either `--profile` or `--release`
flag to `flutter run`.

If you are using the Flutter plugin for Atom, you can disable Slow Mode by selecting
either profile or release build configuration.  

## Where can I get support?

If you think you've encountered a bug, please file it in our
[issue tracker](https://github.com/flutter/flutter/issues). We
encourage you to use [Stack Overflow](https://stackoverflow.com/) for "HOWTO"
type questions. For discussions,
please join our mailing list at
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com).

## How do I get involved?

Flutter is open source, and we encourage you to contribute. You can start by
simply filing issues for feature requests and bugs in our
[issue tracker](https://github.com/flutter/flutter/issues). You
should also join our mailing list at
[flutter-dev@googlegroups.com](mailto:flutter-dev@googlegroups.com) and let us
know how you're using Flutter and what you'd like to do with it. If you're
interested in contributing code, you can start by reading our
[Contributing guide](https://github.com/flutter/flutter/blob/master/CONTRIBUTING.md).

## Should I build my next production app with Flutter?

Flutter is still being developed and is not yet at
1.0. While lower levels of the system are stabilizing, we continue to improve
parts of the system based on user feedback.

Flutter is used inside of Google, but those apps are not yet deployed to
external users.

So really, it is up to you. Please let us know if you released an app
built with Flutter to users. We'd love to what you're building!

## I heard Apple rejects apps built with third-party frameworks, is that true? Will Apple reject my Flutter app?

We can't speak for Apple, but Apple's policies have changed, and they have
allowed apps built with systems like Flutter. Of course, Apple is ultimately
in charge of their ecosystem, but our goal is to continue to do everything we
can to ensure Flutter apps can be deployed into Apple's App Store.

## What language is Flutter written in?

We looked at a lot of languages and runtimes, and ultimately adopted Dart for
the framework and widgets. The underlying graphics framework and the Dart
virtual machine are implemented in C/C++.

## Why did Flutter choose to use Dart?

The primary criteria we used to pick a programming language were the following:

* _Predictable, high performance_. With Flutter, we want to empower developers
  to create fast, fluid user experiences. In order to achieve that, we need to
  be able to run a significant amount of end-developer code during every
  animation frame. That means we need a language that both delivers high
  performance and delivers predictable performance, without periodic
  pauses that would cause dropped frames.

* _Developer productivity_. One of Flutter's main value propositions is that it
  saves engineering resources by letting developers create apps for both iOS and
  Android with the same codebase. Using a highly productive language
  accelerates developers further and makes Flutter more attractive.

* _Object-orientation_. For Flutter, we want a language that's suited to
  Flutter's problem domain: creating visual user experiences. The industry has
  multiple decades of experience building user interface frameworks in
  object-oriented languages. While we could use a non-object-oriented language,
  this would mean reinventing the wheel to solve several hard problems.

* _Fast allocation_. The Flutter framework uses a functional-reactive style
  programming model, whose performance depends heavily on the underlying memory
  allocator efficiently handling small, short-lived allocations. The
  functional-reactive style was developed in languages with this property and
  does not work efficiently in languages that lack this facility.

Dart scores highly on all four dimensions. In addition, we have the opportunity
to work closely with the Dart community, which is actively investing resources
in improving Dart for use in Flutter. For example, when we
adopted Dart, the language did not have an ahead-of-time toolchain for producing
native binaries, which is instrumental in achieving predictable, high
performance, but now the language does because the Dart team built it for
Flutter. Similarly, the Dart VM has previously been optimized for throughput
but the team is now optimizing the VM for latency, which is more important for
Flutter's workload.

## Can Flutter run any Dart code?

Flutter should be able to run most Dart code that does not import (transitively,
or directly) dart:mirrors or dart:html.

## Who works on Flutter?

Flutter is an open source project. Currently, the bulk of the development is done
by engineers at Google. If you're excited about Flutter, we encourage you to join
the community and contribute to Flutter!
