---
title: JavaFX
---

# JavaFX

JavaFX is a software platform for creating and delivering desktop applications, as well as rich Internet applications (RIAs) that can run across a wide variety of devices. JavaFX is intended to replace Swing as the standard GUI library for Java SE.

With the release of JDK 11 in 2018, Oracle has made JavaFX part of the OpenJDK under the [OpenJFX project](https://openjfx.io/).

OpenJFX is a collaborative effort by many individuals and companies with the goal of producing a modern, efficient, and fully featured toolkit for developing rich client applications.

# Input Events

## *Key typed* vs *Key pressed* and *key released*

"*Key typed*" events are higher-level and generally do not depend on the platform or keyboard layout. They are generated when a Unicode character is entered, and are the preferred way to find out about character input. In the simplest case, a key typed event is produced by a single key press (e.g., 'a'). Often, however, characters are produced by series of key presses (e.g., SHIFT + 'a').

"*Key pressed*" and "*key released*" events are lower-level and depend on the platform and keyboard layout.

For more information see the [Key Event JavaDoc](https://docs.oracle.com/javase/8/javafx/api/javafx/scene/input/KeyEvent.html).


# TableView

## (Re)Applying Sorting after an update

If you change the underlying ```ObservableList```, then you can reapply the (column) sorting that the user may have done by calling ```tableView.sort()```.

A more complete example can be seen below:

{% highlight java %}

    fxBeans.clear();
    fxBeans.addAll(beans.stream()
        .map( bean -> new TableRowFxBean(bean)).collect(Collectors.toList()));
    
    // Restore Sorting, if applied
    tableView.sort();
{% endhighlight %}

