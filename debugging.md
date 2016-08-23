---
layout: page
title: Debugging Flutter Apps
sidebar: home_sidebar
permalink: /debugging/
---

There are a wide variety of tools and features to help debug Flutter
applications.

* TOC Placeholder
{:toc}

## The Dart Analyzer

Before running your applications, test your code with `flutter
analyze`. This tool (which is a wrapper around the `dartanalyzer`
tool) will analyze your code and help you find possible mistakes.
If you're using the
[Flutter plugin for Atom](https://atom.io/packages/flutter), this
is already happening for you.

The Dart analyzer makes heavy use of type annotations that you put in
your code to help track problems down. You are encouraged to use them
everywhere (avoiding `var`, untyped arguments, untyped list literals,
etc) as this is the quickest and least paintful way of tracking down
problems.

## Dart Observatory (statement-level single-stepping debugger and profiler)

If you started your application on an Android device using `flutter
start`, then, while it is running, you can open the Web page at
[http://localhost:8181/](http://localhost:8181/) to connect to your
application directly with a statement-level single-stepping debugger.
If you're using Atom, you can also debug your application using the
built-in debugger provided by the aforementioned Flutter plugin.

Observatory also supports profiling, examining the heap, etc. For more
information on Observatory, see
[Observatory's documentation](https://dart-lang.github.io/observatory/).

If you use Observatory for profiling, make sure to run your
application in release mode, by passing `--no-checked` to the `flutter
start` command. Otherwise, the main thing that will appear on your
profile will be the checked-mode asserts verifying the framework's
various invariants (see "Checked mode assertions" below).

### `debugger()` statement

When using the Dart Observatory (or another Dart debugger, such as the
debugger in the Atom IDE), you can insert programmatic breakpoints
using the `debugger()` statement. To use this, you have to put `import
'dart:developer';` at the top of the relevant file.

The `debugger()` statement takes an optional `when` argument which you
can specify to only break when a certain condition is true, as in:

<!-- import 'dart:developer'; -->
<!-- skip -->
```dart
void someFunction(double offset) {
  debugger(when: offset > 30.0);
  // ...
}
```

## `print` and `debugPrint` with `flutter logs`

The Dart `print()` function will output to the system console, which
you can view using `flutter logs` (which is basically a wrapper around
`adb logcat`).

If you output too much at once, then Android sometimes discards some
log lines. To avoid this, you can use `debugPrint()`, from Flutter's
`services` library. This is a wrapper around `print` which throttles
the output to a level that avoids being dropped by Android's kernel.

Many classes in the Flutter framework have useful `toString`
implementations. By convention, these output a single line usually
including the `runtimeType` of the object, typically in the form
`ClassName(more information about this instance...)`. Some classes
that are used in trees also have `toStringDeep`, which returns a
multiline description of the entire subtree from that point. Some
classes that have particularly ~~verbose~~ helpful `toString`
implementations have a corresponding `toStringShort` which returns
only the type or some other very brief (one or two word) description
of the object.

## Checked mode assertions

During development, you are highly encouraged to use Dart's "checked"
mode, sometimes referred to as "debug" mode. This is the default if
you use `flutter run`. In this mode, the Dart `assert` statement is
enabled, and the Flutter framework uses this to perform many runtime
checks verifying that invariants aren't being violated.

When an invariant is violated, it is reported to the console, with
some context information to help with tracking down the source of the
problem.

To turn off checked mode, and use release mode, run your application
using `flutter run --no-checked`.

## Dumping the application state

Each layer of the Flutter framework provides a function to dump its
current state to the console (using `debugPrint`).

### Widget layer

To dump the state of the Widgets library, call
[`debugDumpApp()`](http://docs.flutter.io/flutter/widgets/debugDumpApp.html).
You can call this more or less any time that the application is not in
the middle of running a build phase (i.e. anywhere not inside a
`build()` method), so long as the application has built at least once
(i.e. any time after calling `runApp()`).

For example, this application:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(
    new MaterialApp(
      home: new AppHome(),
    ),
  );
}

class AppHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new Material(
      child: new Center(
        child: new FlatButton(
          onPressed: () {
            debugDumpApp();
          },
          child: new Text('Dump App'),
        ),
      ),
    );
  }
}
```

...will output something like this (the precise details will vary on
the version of the framework, the size of the device, and so forth):


```
I/flutter : WidgetFlutterBinding - CHECKED MODE
I/flutter : RenderObjectToWidgetAdapter<RenderBox>([GlobalObjectKey RenderView(707742879)]; renderObject: RenderView)
I/flutter :  └MaterialApp(state: _MaterialAppState(859106034))
I/flutter :    └AnimatedTheme(duration: 200ms; state: _AnimatedThemeState(863664596; ThemeDataTween(ThemeData(ThemeBrightness.light Color(0xff2196f3) etc...) → null)))
I/flutter :      └Theme(ThemeData(ThemeBrightness.light Color(0xff2196f3) etc...))
I/flutter :        └CheckedModeBanner()
I/flutter :          └CustomPaint(renderObject: RenderCustomPaint)
I/flutter :            └MediaQuery(MediaQueryData(Size(411.4, 683.4), Orientation.portrait))
I/flutter :              └LocaleQuery(null)
I/flutter :                └DefaultTextStyle(inherit: true; color: Color(0xd0ff0000); family: "monospace"; size: 48.0; weight: 900; align: right; decoration: double Color(0xffffff00) TextDecoration.underline)
I/flutter :                  └AssetVendor(bundle: MojoAssetBundle@485678190(); devicePixelRatio: 3.5; state: _AssetVendorState(516395298; bundle: _ResolutionAwareAssetBundle@654688423()))
I/flutter :                    └DefaultAssetBundle()
I/flutter :                      └Title(color: Color(0xff2196f3))
I/flutter :                        └Navigator([GlobalObjectKey _MaterialAppState(859106034)]; state: NavigatorState(224175532))
I/flutter :                          └Overlay([GlobalKey 625702218]; state: OverlayState(914946327; entries: [OverlayEntry@575242037(opaque: false), OverlayEntry@192955945(opaque: false)]))
I/flutter :                            └Stack(renderObject: RenderStack)
I/flutter :                              ├_OverlayEntry([GlobalKey 238044853]; state: _OverlayEntryState(863631796))
I/flutter :                              │ └IgnorePointer(renderObject: RenderIgnorePointer)
I/flutter :                              │   └ModalBarrier()
I/flutter :                              │     └Semantics(container: true; checked: null; label: "null"; renderObject: RenderSemanticAnnotations)
I/flutter :                              │       └GestureDetector()
I/flutter :                              │         └RawGestureDetector(state: RawGestureDetectorState(833036356; gestures: tap; behavior: opaque))
I/flutter :                              │           └_GestureSemantics(renderObject: RenderSemanticsGestureHandler)
I/flutter :                              │             └Listener(listeners: down; behavior: opaque; renderObject: RenderPointerListener)
I/flutter :                              │               └ConstrainedBox(BoxConstraints(biggest); renderObject: RenderConstrainedBox)
I/flutter :                              └_OverlayEntry([GlobalKey 388965355]; state: _OverlayEntryState(889389336))
I/flutter :                                └_ModalScope([GlobalKey 328026813]; state: _ModalScopeState(362869520))
I/flutter :                                  └Focus([GlobalObjectKey MaterialPageRoute(560156430)]; state: _FocusState(48930702))
I/flutter :                                    └Semantics(container: true; checked: null; label: "null"; renderObject: RenderSemanticAnnotations)
I/flutter :                                      └_FocusScope(this scope has focus)
I/flutter :                                        └RepaintBoundary(renderObject: RenderRepaintBoundary)
I/flutter :                                          └IgnorePointer(renderObject: RenderIgnorePointer)
I/flutter :                                            └_MaterialPageTransition(animation: CurvedAnimation(⏭); state: _AnimatedState(483936073))
I/flutter :                                              └Transform(renderObject: RenderTransform)
I/flutter :                                                └Opacity(opacity: 1.0; renderObject: RenderOpacity)
I/flutter :                                                  └PageStorage([GlobalKey 265300])
I/flutter :                                                    └_ModalScopeStatus(active)
I/flutter :                                                      └AppHome()
I/flutter :                                                        └Material(MaterialType.canvas; elevation: 0; state: _MaterialState(373867105))
I/flutter :                                                          └AnimatedContainer(duration: 200ms; has background; state: _AnimatedContainerState(26175381; has background))
I/flutter :                                                            └Container(bg: BoxDecoration())
I/flutter :                                                              └DecoratedBox(renderObject: RenderDecoratedBox)
I/flutter :                                                                └Container(bg: BoxDecoration(backgroundColor: Color(0xfffafafa)))
I/flutter :                                                                  └DecoratedBox(renderObject: RenderDecoratedBox)
I/flutter :                                                                    └NotificationListener<LayoutChangedNotification>()
I/flutter :                                                                      └InkFeatures([GlobalKey ink renderer]; renderObject: RenderInkFeatures)
I/flutter :                                                                        └DefaultTextStyle(inherit: false; color: Color(0xdd000000); size: 14.0; weight: 400; baseline: alphabetic; height: 1.4285714285714286x)
I/flutter :                                                                          └Center(renderObject: RenderPositionedBox)
I/flutter :                                                                            └FlatButton(dirty; state: _FlatButtonState(608093273))
I/flutter :                                                                              └Container(BoxConstraints(88.0<=w<=Infinity, h=36.0); margin: EdgeInsets(8.0, 8.0, 8.0, 8.0); padding: EdgeInsets(0.0, 8.0, 0.0, 8.0))
I/flutter :                                                                                └Padding(renderObject: RenderPadding relayoutSubtreeRoot=up1)
I/flutter :                                                                                  └ConstrainedBox(BoxConstraints(88.0<=w<=Infinity, h=36.0); renderObject: RenderConstrainedBox relayoutSubtreeRoot=up2)
I/flutter :                                                                                    └Padding(renderObject: RenderPadding relayoutSubtreeRoot=up3)
I/flutter :                                                                                      └DefaultTextStyle(inherit: false; color: Color(0xdd000000); size: 14.0; weight: 500; baseline: alphabetic)
I/flutter :                                                                                        └InkWell(state: _InkResponseState<InkResponse>(741510572))
I/flutter :                                                                                          └GestureDetector()
I/flutter :                                                                                            └RawGestureDetector(state: RawGestureDetectorState(576641993; gestures: tap; behavior: opaque))
I/flutter :                                                                                              └_GestureSemantics(renderObject: RenderSemanticsGestureHandler relayoutSubtreeRoot=up4)
I/flutter :                                                                                                └Listener(listeners: down; behavior: opaque; renderObject: RenderPointerListener relayoutSubtreeRoot=up5)
I/flutter :                                                                                                  └Container(padding: EdgeInsets(0.0, 8.0, 0.0, 8.0))
I/flutter :                                                                                                    └Padding(renderObject: RenderPadding relayoutSubtreeRoot=up6)
I/flutter :                                                                                                      └Center(renderObject: RenderPositionedBox relayoutSubtreeRoot=up7)
I/flutter :                                                                                                        └Text("Dump App")
I/flutter :                                                                                                          └RichText(renderObject: RenderParagraph relayoutSubtreeRoot=up8)
```

This is the "flattened" tree, showing all the widgets projected
through their various build functions. (This is the tree you obtain if
you call `toStringDeep` on the root of the widget tree.) You'll see a
lot of widgets in there that don't appear in your application's
source, because they are inserted by the framework's widgets' build
functions. For example,
[`InkFeatures`](http://docs.flutter.io/flutter/material/InkFeatures-class.html)
is an implementation detail of the
[`Material`](http://docs.flutter.io/flutter/material/Material-class.html)
widget.

Since the `debugDumpApp()` call is invoked when the button changes
from being pressed to being released, it coincides with the
[`FlatButton`](http://docs.flutter.io/flutter/material/FlatButton-class.html)
object calling
[`setState()`](http://docs.flutter.io/flutter/widgets/State/setState.html)
and thus marking itself dirty. That is why if you look at the dump you
will see that specific object marked "dirty". You can also see what
gesture listeners have been registered; in this case, a single
GestureDetector is listed, and it is listening only to a "tap" gesture
("tap" is the output of a `TapGestureDetector`'s `toStringShort`
function).

If you write your own widgets, you can add information by overriding
[`debugFillDescription()`](http://docs.flutter.io/flutter/material/Widget/debugFillDescription.html).
Add strings to the method's argument, and call the superclass method.
This function is what the `toString` method uses to fill in the
widget's description.

### Rendering layer

If you are trying to debug a layout issue, then the Widgets layer's
tree may be insufficiently detailed. In that case, you can dump the
rendering tree by calling
[`debugDumpRenderTree()`](http://docs.flutter.io/flutter/rendering/debugDumpRenderTree.html).
As with `debugDumpApp()`, you can call this more or less any time
except during a layout or paint phase. As a general rule, calling it
from a [frame
callback](http://docs.flutter.io/flutter/scheduler/Scheduler/addPersistentFrameCallback.html)
or an event handler is the best solution.

To call `debugDumpRenderTree()`, you need to add `import
'package:flutter/rendering.dart';` to your source file.

The output for the tiny example above would look something like this:

```
I/flutter : RenderView
I/flutter :  │ window size: Size(411.4, 683.4) (in device pixels)
I/flutter :  │ device pixel ratio: 3.5 (device pixels per logical pixel)
I/flutter :  │ configuration: Size(411.4, 683.4) (in logical pixels)
I/flutter :  │
I/flutter :  └─child: RenderCustomPaint
I/flutter :    │ creator: CustomPaint ← CheckedModeBanner ← Theme ← AnimatedTheme ← MaterialApp ← [root]
I/flutter :    │ parentData: <none>
I/flutter :    │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :    │ size: Size(411.4, 683.4)
I/flutter :    │
I/flutter :    └─child: RenderStack
I/flutter :      │ creator: Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← DefaultAssetBundle ← AssetVendor ← DefaultTextStyle ← LocaleQuery ← MediaQuery ← CustomPaint ← ⋯
I/flutter :      │ parentData: offset=Offset(0.0, 0.0)
I/flutter :      │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │ size: Size(411.4, 683.4)
I/flutter :      │
I/flutter :      ├─child 1: RenderIgnorePointer
I/flutter :      │ │ creator: IgnorePointer ← _OverlayEntry-[GlobalKey 238044853] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← DefaultAssetBundle ← AssetVendor ← DefaultTextStyle ← LocaleQuery ← ⋯
I/flutter :      │ │ parentData: not positioned; offset=Offset(0.0, 0.0)
I/flutter :      │ │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │ │ size: Size(411.4, 683.4)
I/flutter :      │ │ ignoring: false
I/flutter :      │ │ ignoringSemantics: implicitly false
I/flutter :      │ │
I/flutter :      │ └─child: RenderSemanticAnnotations
I/flutter :      │   │ creator: Semantics ← ModalBarrier ← IgnorePointer ← _OverlayEntry-[GlobalKey 238044853] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← DefaultAssetBundle ← AssetVendor ← ⋯
I/flutter :      │   │ parentData: offset=Offset(0.0, 0.0)
I/flutter :      │   │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │   │ size: Size(411.4, 683.4)
I/flutter :      │   │
I/flutter :      │   └─child: RenderSemanticsGestureHandler
I/flutter :      │     │ creator: _GestureSemantics ← RawGestureDetector ← GestureDetector ← Semantics ← ModalBarrier ← IgnorePointer ← _OverlayEntry-[GlobalKey 238044853] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← ⋯
I/flutter :      │     │ parentData: offset=Offset(0.0, 0.0)
I/flutter :      │     │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │     │ size: Size(411.4, 683.4)
I/flutter :      │     │
I/flutter :      │     └─child: RenderPointerListener
I/flutter :      │       │ creator: Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← Semantics ← ModalBarrier ← IgnorePointer ← _OverlayEntry-[GlobalKey 238044853] ← Stack ← Overlay-[GlobalKey 625702218] ← ⋯
I/flutter :      │       │ parentData: offset=Offset(0.0, 0.0)
I/flutter :      │       │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │       │ size: Size(411.4, 683.4)
I/flutter :      │       │ behavior: opaque
I/flutter :      │       │ listeners: down
I/flutter :      │       │
I/flutter :      │       └─child: RenderConstrainedBox
I/flutter :      │           creator: ConstrainedBox ← Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← Semantics ← ModalBarrier ← IgnorePointer ← _OverlayEntry-[GlobalKey 238044853] ← Stack ← ⋯
I/flutter :      │           parentData: offset=Offset(0.0, 0.0)
I/flutter :      │           constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :      │           size: Size(411.4, 683.4)
I/flutter :      │           additionalConstraints: BoxConstraints(biggest)
I/flutter :      │
I/flutter :      └─child 2: RenderSemanticAnnotations
I/flutter :        │ creator: Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← DefaultAssetBundle ← AssetVendor ← ⋯
I/flutter :        │ parentData: not positioned; offset=Offset(0.0, 0.0)
I/flutter :        │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :        │ size: Size(411.4, 683.4)
I/flutter :        │
I/flutter :        └─child: RenderRepaintBoundary
I/flutter :          │ creator: RepaintBoundary ← _FocusScope ← Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← ⋯
I/flutter :          │ parentData: offset=Offset(0.0, 0.0)
I/flutter :          │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :          │ size: Size(411.4, 683.4)
I/flutter :          │
I/flutter :          └─child: RenderIgnorePointer
I/flutter :            │ creator: IgnorePointer ← RepaintBoundary ← _FocusScope ← Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← ⋯
I/flutter :            │ parentData: offset=Offset(0.0, 0.0)
I/flutter :            │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :            │ size: Size(411.4, 683.4)
I/flutter :            │ ignoring: false
I/flutter :            │ ignoringSemantics: implicitly false
I/flutter :            │
I/flutter :            └─child: RenderTransform
I/flutter :              │ creator: Transform ← _MaterialPageTransition ← IgnorePointer ← RepaintBoundary ← _FocusScope ← Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← Stack ← ⋯
I/flutter :              │ parentData: offset=Offset(0.0, 0.0)
I/flutter :              │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :              │ size: Size(411.4, 683.4)
I/flutter :              │ transform matrix:
I/flutter :              │   [0] 1.0,0.0,0.0,0.0
I/flutter :              │   [1] 0.0,1.0,0.0,0.0
I/flutter :              │   [2] 0.0,0.0,1.0,0.0
I/flutter :              │   [3] 0.0,0.0,0.0,1.0
I/flutter :              │ origin: null
I/flutter :              │ alignment: null
I/flutter :              │ transformHitTests: true
I/flutter :              │
I/flutter :              └─child: RenderOpacity
I/flutter :                │ creator: Opacity ← Transform ← _MaterialPageTransition ← IgnorePointer ← RepaintBoundary ← _FocusScope ← Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← ⋯
I/flutter :                │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :                │ size: Size(411.4, 683.4)
I/flutter :                │ opacity: 1.0
I/flutter :                │
I/flutter :                └─child: RenderDecoratedBox
I/flutter :                  │ creator: DecoratedBox ← Container ← AnimatedContainer ← Material ← AppHome ← _ModalScopeStatus ← PageStorage-[GlobalKey 265300] ← Opacity ← Transform ← _MaterialPageTransition ← ⋯
I/flutter :                  │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                  │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :                  │ size: Size(411.4, 683.4)
I/flutter :                  │ decoration:
I/flutter :                  │   <no decorations specified>
I/flutter :                  │
I/flutter :                  └─child: RenderDecoratedBox
I/flutter :                    │ creator: DecoratedBox ← Container ← DecoratedBox ← Container ← AnimatedContainer ← Material ← AppHome ← _ModalScopeStatus ← PageStorage-[GlobalKey 265300] ← Opacity ← ⋯
I/flutter :                    │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                    │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :                    │ size: Size(411.4, 683.4)
I/flutter :                    │ decoration:
I/flutter :                    │   backgroundColor: Color(0xfffafafa)
I/flutter :                    │
I/flutter :                    └─child: RenderInkFeatures
I/flutter :                      │ creator: InkFeatures-[GlobalKey ink renderer] ← NotificationListener<LayoutChangedNotification> ← DecoratedBox ← Container ← DecoratedBox ← Container ← AnimatedContainer ← Material ← AppHome ← _ModalScopeStatus ← ⋯
I/flutter :                      │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                      │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :                      │ size: Size(411.4, 683.4)
I/flutter :                      │
I/flutter :                      └─child: RenderPositionedBox
I/flutter :                        │ creator: Center ← DefaultTextStyle ← InkFeatures-[GlobalKey ink renderer] ← NotificationListener<LayoutChangedNotification> ← DecoratedBox ← Container ← DecoratedBox ← Container ← AnimatedContainer ← Material ← ⋯
I/flutter :                        │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                        │ constraints: BoxConstraints(w=411.4, h=683.4)
I/flutter :                        │ size: Size(411.4, 683.4)
I/flutter :                        │ alignment: FractionalOffset(0.5, 0.5)
I/flutter :                        │
I/flutter :                        └─child: RenderPadding relayoutSubtreeRoot=up1
I/flutter :                          │ creator: Padding ← Container ← FlatButton ← Center ← DefaultTextStyle ← InkFeatures-[GlobalKey ink renderer] ← NotificationListener<LayoutChangedNotification> ← DecoratedBox ← Container ← DecoratedBox ← ⋯
I/flutter :                          │ parentData: offset=Offset(148.7, 315.7)
I/flutter :                          │ constraints: BoxConstraints(0.0<=w<=411.4, 0.0<=h<=683.4)
I/flutter :                          │ size: Size(114.0, 52.0)
I/flutter :                          │ padding: EdgeInsets(8.0, 8.0, 8.0, 8.0)
I/flutter :                          │
I/flutter :                          └─child: RenderConstrainedBox relayoutSubtreeRoot=up2
I/flutter :                            │ creator: ConstrainedBox ← Padding ← Container ← FlatButton ← Center ← DefaultTextStyle ← InkFeatures-[GlobalKey ink renderer] ← NotificationListener<LayoutChangedNotification> ← DecoratedBox ← Container ← ⋯
I/flutter :                            │ parentData: offset=Offset(8.0, 8.0)
I/flutter :                            │ constraints: BoxConstraints(0.0<=w<=395.4, 0.0<=h<=667.4)
I/flutter :                            │ size: Size(98.0, 36.0)
I/flutter :                            │ additionalConstraints: BoxConstraints(88.0<=w<=Infinity, h=36.0)
I/flutter :                            │
I/flutter :                            └─child: RenderPadding relayoutSubtreeRoot=up3
I/flutter :                              │ creator: Padding ← ConstrainedBox ← Padding ← Container ← FlatButton ← Center ← DefaultTextStyle ← InkFeatures-[GlobalKey ink renderer] ← NotificationListener<LayoutChangedNotification> ← DecoratedBox ← ⋯
I/flutter :                              │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                              │ constraints: BoxConstraints(88.0<=w<=395.4, h=36.0)
I/flutter :                              │ size: Size(98.0, 36.0)
I/flutter :                              │ padding: EdgeInsets(0.0, 8.0, 0.0, 8.0)
I/flutter :                              │
I/flutter :                              └─child: RenderSemanticsGestureHandler relayoutSubtreeRoot=up4
I/flutter :                                │ creator: _GestureSemantics ← RawGestureDetector ← GestureDetector ← InkWell ← DefaultTextStyle ← Padding ← ConstrainedBox ← Padding ← Container ← FlatButton ← ⋯
I/flutter :                                │ parentData: offset=Offset(8.0, 0.0)
I/flutter :                                │ constraints: BoxConstraints(72.0<=w<=379.4, h=36.0)
I/flutter :                                │ size: Size(82.0, 36.0)
I/flutter :                                │
I/flutter :                                └─child: RenderPointerListener relayoutSubtreeRoot=up5
I/flutter :                                  │ creator: Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← InkWell ← DefaultTextStyle ← Padding ← ConstrainedBox ← Padding ← Container ← ⋯
I/flutter :                                  │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                                  │ constraints: BoxConstraints(72.0<=w<=379.4, h=36.0)
I/flutter :                                  │ size: Size(82.0, 36.0)
I/flutter :                                  │ behavior: opaque
I/flutter :                                  │ listeners: down
I/flutter :                                  │
I/flutter :                                  └─child: RenderPadding relayoutSubtreeRoot=up6
I/flutter :                                    │ creator: Padding ← Container ← Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← InkWell ← DefaultTextStyle ← Padding ← ConstrainedBox ← ⋯
I/flutter :                                    │ parentData: offset=Offset(0.0, 0.0)
I/flutter :                                    │ constraints: BoxConstraints(72.0<=w<=379.4, h=36.0)
I/flutter :                                    │ size: Size(82.0, 36.0)
I/flutter :                                    │ padding: EdgeInsets(0.0, 8.0, 0.0, 8.0)
I/flutter :                                    │
I/flutter :                                    └─child: RenderPositionedBox relayoutSubtreeRoot=up7
I/flutter :                                      │ creator: Center ← Padding ← Container ← Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← InkWell ← DefaultTextStyle ← Padding ← ⋯
I/flutter :                                      │ parentData: offset=Offset(8.0, 0.0)
I/flutter :                                      │ constraints: BoxConstraints(56.0<=w<=363.4, h=36.0)
I/flutter :                                      │ size: Size(66.0, 36.0)
I/flutter :                                      │ alignment: FractionalOffset(0.5, 0.5)
I/flutter :                                      │
I/flutter :                                      └─child: RenderParagraph relayoutSubtreeRoot=up8
I/flutter :                                        │ creator: RichText ← Text ← Center ← Padding ← Container ← Listener ← _GestureSemantics ← RawGestureDetector ← GestureDetector ← InkWell ← ⋯
I/flutter :                                        │ parentData: offset=Offset(0.0, 10.0)
I/flutter :                                        │ constraints: BoxConstraints(0.0<=w<=363.4, 0.0<=h<=36.0)
I/flutter :                                        │ size: Size(66.0, 16.0)
I/flutter :                                        ╘═╦══ text ═══
I/flutter :                                          ║ TextSpan:
I/flutter :                                          ║   inherit: false
I/flutter :                                          ║   color: Color(0xdd000000)
I/flutter :                                          ║   size: 14.0
I/flutter :                                          ║   weight: 500
I/flutter :                                          ║   baseline: alphabetic
I/flutter :                                          ║   "Dump App"
I/flutter :                                          ╚═══════════
```

This is the output of the root `RenderObject` object's `toStringDeep`
function.

When debugging layout issues, the key fields to look at are the `size`
and `constraints` fields. The constraints flow down the tree, and the
sizes flow back up.

For example, in the dump above you can see that the window size,
`Size(411.4, 683.4)`, is used to force all the boxes down to the
[`RenderPositionedBox`](http://docs.flutter.io/flutter/rendering/RenderPositionedBox-class.html)
to be the size of the screen, with constraints of
`BoxConstraints(w=411.4, h=683.4)`. The `RenderPositionedBox`, which
the dump says was created by a
[`Center`](http://docs.flutter.io/flutter/widgets/Center-class.html)
widget (as described by the `creator` field), sets its child's
constraints to a loose version of this: `BoxConstraints(0.0<=w<=411.4,
0.0<=h<=683.4)`. The child, a
[`RenderPadding`](http://docs.flutter.io/flutter/rendering/RenderPadding-class.html),
further inserts these constraints to ensure there is room for the
padding, and thus the
[`RenderConstrainedBox`](http://docs.flutter.io/flutter/rendering/RenderConstrainedBox-class.html)
has a loose constraint of `BoxConstraints(0.0<=w<=395.4,
0.0<=h<=667.4)`. This object, which the `creator` field tells us is
probably part of the
[`FlatButton`](http://docs.flutter.io/flutter/material/FlatButton-class.html)'s
definition, sets a minimum width of 88 pixels on its contents and a
specific height of 36.0. (This is the `FlatButton` class implementing
the Material Design rules regarding button dimensions.)

The inner-most `RenderPositionedBox` loosens the constraints again,
this time to center the text within the button. The
[`RenderParagraph`](http://docs.flutter.io/flutter/rendering/RenderParagraph-class.html)
picks its size based on its contents. If you now follow the sizes back
up the chain, you'll see how the text's size is what influences the
width of all the boxes that form the button, as they all take their
child's dimensions to size themselves.

Another way to notice this is by looking at the "relayoutSubtreeRoot"
part of the descriptions of each box, which essentially tells you how
many ancestors depend on this element's size in some way. Thus the
`RenderParagraph` has a `relayoutSubtreeRoot=up8`, meaning that when
the `RenderParagraph` is dirtied, eight ancestors also have to be
dirtied because they might be affected by the new dimensions.

If you write your own render objects, you can add information to the
dump by overriding
[`debugDescribeSettings()`](http://docs.flutter.io/flutter/rendering/RenderObject/debugDescribeSettings.html).
Add strings to the method's argument, and call the superclass method.

### Layers

If you are trying to debug a compositing issue, you can use
[`debugDumpLayerTree()`](http://docs.flutter.io/flutter/rendering/debugDumpLayerTree.html).
For the example above, it would output:

```
I/flutter : TransformLayer
I/flutter :  │ creator: [root]
I/flutter :  │ offset: Offset(0.0, 0.0)
I/flutter :  │ transform:
I/flutter :  │   [0] 3.5,0.0,0.0,0.0
I/flutter :  │   [1] 0.0,3.5,0.0,0.0
I/flutter :  │   [2] 0.0,0.0,1.0,0.0
I/flutter :  │   [3] 0.0,0.0,0.0,1.0
I/flutter :  │
I/flutter :  ├─child 1: OffsetLayer
I/flutter :  │ │ creator: RepaintBoundary ← _FocusScope ← Semantics ← Focus-[GlobalObjectKey MaterialPageRoute(560156430)] ← _ModalScope-[GlobalKey 328026813] ← _OverlayEntry-[GlobalKey 388965355] ← Stack ← Overlay-[GlobalKey 625702218] ← Navigator-[GlobalObjectKey _MaterialAppState(859106034)] ← Title ← ⋯
I/flutter :  │ │ offset: Offset(0.0, 0.0)
I/flutter :  │ │
I/flutter :  │ └─child 1: PictureLayer
I/flutter :  │
I/flutter :  └─child 2: PictureLayer
```

This is the output of calling `toStringDeep` on the root `Layer` object.

The transform at the root is the transform that applies the device
pixel ratio; in this case, a ratio of 3.5 device pixels for every
logical pixel.

The `RepaintBoundary` widget, which creates a `RenderRepaintBoundary`
in the render tree, creates a new layer in the layer tree. This is
used to reduce how much needs to be repainted.

### Semantics

You can also obtain a dump of the Semantics tree (the tree presented
to the system accessibility APIs) using
[`debugDumpSemanticsTree()`](http://docs.flutter.io/flutter/rendering/debugDumpSemanticsTree.html).
To use this, you have to have first enable accessibility, e.g. by
enabling a system accessibility tool or the `SemanticsDebugger`
(discussed below).

For the example above, it would output:

```
I/flutter : SemanticsNode(0; Rect.fromLTRB(0.0, 0.0, 411.4, 683.4))
I/flutter :  ├SemanticsNode(1; Rect.fromLTRB(0.0, 0.0, 411.4, 683.4))
I/flutter :  │ └SemanticsNode(2; Rect.fromLTRB(0.0, 0.0, 411.4, 683.4); canBeTapped)
I/flutter :  └SemanticsNode(3; Rect.fromLTRB(0.0, 0.0, 411.4, 683.4))
I/flutter :    └SemanticsNode(4; Rect.fromLTRB(0.0, 0.0, 82.0, 36.0); canBeTapped; "Dump App")
```

<!-- this tree is bad, see https://github.com/flutter/flutter/issues/2476 -->

## Visual debugging

You can also debug a layout problem visually, by setting
[`debugPaintSizeEnabled`](http://docs.flutter.io/flutter/rendering/debugPaintSizeEnabled.html)
to `true`. This is a boolean from the `rendering` library. It can be
enabled at any time and affects all painting while it is true. The
easiest way to set it is at the top of your `void main()` entry point.

When it is enabled, all boxes get a bright teal border, padding (from
widgets like `Padding`) is shown in faded blue with a darker blue box
around the child, alignment (from widgets like `Center` and `Align`)
is shown with yellow arrows, and spacers (from widgets like
`Container` when they have no child) are shown in gray.

The
[`debugPaintBaselinesEnabled`](http://docs.flutter.io/flutter/rendering/debugPaintBaselinesEnabled.html)
does something similar but for objects with baselines. The alphabetic
baseline is shown in bright green and the ideographic baseline in
orange.

The
[`debugPaintPointersEnabled`](http://docs.flutter.io/flutter/rendering/debugPaintPointersEnabled.html)
flag turns on a special mode whereby any objects that are being tapped
get highlighted in teal. This can help you determine whether an object
is somehow failing to correctly hit test (which might happen if, for
instance, it is actually outside the bounds of its parent and thus not
being considered for hit testing in the first place).

If you're trying to debug compositor layers, for example to determine
whether and where to add `RepaintBoundary` widgets, you can use the
[`debugPaintLayerBordersEnabled`](http://docs.flutter.io/flutter/rendering/debugPaintLayerBordersEnabled.html)
flag, which outlines each layer's bounds in orange, or the
[`debugRepaintRainbowEnabled`](http://docs.flutter.io/flutter/rendering/debugRepaintRainbowEnabled.html)
flag, which causes layers to be overlayed with a rotating set of
colors whenever they are repainted.

All of these flags only work in checked mode. In general, anything in
the Flutter framework that starts with "`debug...`" will only work in
checked mode.

## Debugging animations

The easiest way to debug animations is to slow them way down. To do
that, set the
[`timeDilation`](http://docs.flutter.io/flutter/scheduler/timeDilation.html)
variable (from the `scheduler` library) to a number greater than 1.0,
for instance, 50.0. It's best to only set this once on app startup. If
you change it on the fly, especially if you reduce it while animations
are running, it's possible that the framework will observe time going
backwards, which will probably result in asserts and generally will
interfere with your efforts.

## Debugging performance problems

To see why your application is causing relayouts or repaints, you can
set the
[`debugPrintMarkNeedsLayoutStacks`](http://docs.flutter.io/flutter/rendering/debugPrintMarkNeedsLayoutStacks.html)
and
[`debugPrintMarkNeedsPaintStacks`](http://docs.flutter.io/flutter/rendering/debugPrintMarkNeedsPaintStacks.html)
flags respectively. These will log a stack trace to the console any
time a render box is asked to relayout and repaint. You can use the
`debugPrintStack()` method from the `services` library to print your
own stack traces on demand, if this kind of approach is useful to you.

### Measuring app startup time

To gather detailed information about the time it takes for your Flutter app to start, you can run
the `flutter run` command with the `trace-startup` and `profile` options.

```
$ flutter run --trace-startup --profile
```
The trace output is saved as a JSON file called `start_up_info.json` under the `build` directory
of your Flutter project. The output lists the elapsed time from app startup to these trace
events (captured in microseconds):

+ Time to enter the Flutter engine code.
+ Time to render the first frame of the app.
+ Time to initialize the Flutter framework.
+ Time to complete the Flutter framework initialization.

For example:

```
{
  "engineEnterTimestampMicros": 96025565262,
  "timeToFirstFrameMicros": 2171978,
  "timeToFrameworkInitMicros": 514585,
  "timeAfterFrameworkInitMicros": 1657393
}
```

## PerformanceOverlay

To get a graphical view of the performance of your application, set
the `showPerformanceOverlay` argument of the
[`MaterialApp`](http://docs.flutter.io/flutter/material/MaterialApp/MaterialApp.html)
constructor to true. The
[`WidgetsApp`](http://docs.flutter.io/flutter/widgets/WidgetsApp-class.html)
constructor has a similar argument. (If you're not using `MaterialApp`
or `WidgetsApp`, you can get the same effect by wrapping your
application in a stack and putting a widget on your stack that was
created by calling
[`new PerformanceOverlay.allEnabled()`](http://docs.flutter.io/flutter/widgets/PerformanceOverlay/PerformanceOverlay.allEnabled.html).)

This will show two graphs. The top one is the time spent by the GPU
thread, the bottom one is the time spent by the CPU thread. The white
lines across the graphs show 16ms increments along the vertical axis;
if the graph ever goes over one of these lines then you are running at
less than 60Hz. The horizontal axis represents frames. The graph is
only updated when your application paints, so if it is idle the graph
will stop moving.

This should always be done in release mode, since in checked mode
performance is intentionally sacrificed in exchange for expensive
asserts that are intended to aid development, and thus the results
will be misleading.

## Material grid

When developing applications that implement [Material
design](https://www.google.com/design/spec/material-design/introduction.html),
it can be helpful to overlay a [Material design baseline
grid](https://www.google.com/design/spec/layout/metrics-keylines.html)
over the application to help verify alignments. To that end, the
[`MaterialApp`
constructor](http://docs.flutter.io/flutter/material/MaterialApp/MaterialApp.html)
has a `debugShowGrid` argument which, when set to `true` in checked
mode, will overlay such a grid.

You can also overlay such a grid on non-Material applications by using
the
[`GridPaper`](http://docs.flutter.io/flutter/widgets/GridPaper-class.html)
widget directly.
