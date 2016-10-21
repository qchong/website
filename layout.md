---
layout: page
title: Dealing with box constraints in Flutter
sidebar: home_sidebar
permalink: /layout/
---

You may be directed to this page if the framework detects a problem
involving box constraints.

* Placeholder for TOC
{:toc}

## Introduction

In Flutter, widgets are rendered by render boxes. Render boxes are given
constraints by their parent, and size themselves within those
constraints. Constraints consist of minimum and maximum widths and
heights; sizes consist of a specific width and height.

Generally, there are three kinds of boxes, in terms of how they handle
their constraints:

- Those that try to be as big as possible.
  For example, the boxes used by `Center` and `Block`.
- Those that try to be the same size as their children.
  For example, the boxes used by `Transform` and `Opacity`.
- Those that try to be a particular size.
  For example, the boxes used by `Image` and `Text`.

Some widgets, for example `Container`, vary from type to type based on
their constructor arguments. In the case of `Container`, it defaults
to trying to be as big as possible, but if you give it a `width`, for
instance, it tries to honor that and be that particular size.

Others, for example `Row` and `Column` (flex boxes) vary based on the
constraints they are given, as described below in the "Flex" section.

The constraints are sometimes "tight", meaning that they leave no room
for the render box to decide on a size (e.g. if the minimum and
maximum width are the same, it is said to have a tight width). The
main example of this is the `App` widget, which is contained by the
`RenderView` class: the box used by the child returned by the
application's `build` function is given a constraint that forces it to
exactly fill the application's content area (typically, the entire
screen). Many of the boxes in Flutter, especially those that just take a
single child, will pass their constraint on to their children. This
means that if you nest a bunch of boxes inside each other at the root
of your application's render tree, they'll all exactly fit in each
other, forced by these tight constraints.

Some boxes _loosen_ the constraints, meaning the maximum is maintained
but the minimum is removed. For example, `Center`.

Unbounded constraints
---------------------

In certain situations, the constraint that is given to a box will be
_unbounded_, or infinite. This means that either the maximum width or
the maximum height is set to `double.INFINITY`.

A box that tries to be as big as possible won't function usefully when
given an unbounded constraint, and in checked mode, such a combination
will throw an exception that points to this file.

The most common cases where a render box finds itself with unbounded
constraints are within flex boxes (`Row` and `Column`), and **within
scrollable regions** (mainly `Block`, `ScollableList<T>`, and
`ScrollableMixedWidgetList`).

In particular, `Block` tries to expand to fit the space available in
its cross-direction (i.e. if it's a vertically-scrolling block, it
will try to be as wide as its parent). If you nest a vertically
scrolling `Block` inside a horizontally scrolling `Block`, the inner
one will try to be as wide as possible, which is infinitely wide,
since the outer one is scrollable in that direction.

Flex
----

Flex boxes themselves (`Row` and `Column`) behave differently based on
whether they are in a bounded constraints or unbounded constraints in
their given direction.

In bounded constraints, they try to be as big as possible in that
direction.

In unbounded constraints, they try to fit their children in that
direction. In this case, you cannot set `flex` on the children to
anything other than 0 (the default). In the widget library, this
means that you cannot use `Flexible` when the flex box is inside
another flex box or inside a scrollable. If you do, you'll get an
exception message pointing you at this document.

In the _cross_ direction, i.e. in their width for `Column` (vertical
flex) and in their height for `Row` (horizontal flex), they must never
be unbounded, otherwise they would not be able to reasonably align
their children.
