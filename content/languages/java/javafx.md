---
title: JavaFX
tags:
- java
---

JavaFX is a software platform for creating and delivering desktop applications, as well as rich Internet applications (RIAs) that can run across a wide variety of devices. JavaFX is intended to replace Swing as the standard GUI library for Java SE.

<!--more-->
With the release of JDK 11 in 2018, Oracle has made JavaFX part of the OpenJDK under the [OpenJFX project](https://openjfx.io/).

OpenJFX is a collaborative effort by many individuals and companies with the goal of producing a modern, efficient, and fully featured toolkit for developing rich client applications.

## Input Events

### *Key typed* vs *Key pressed* and *key released*

"*Key typed*" events are higher-level and generally do not depend on the platform or keyboard layout. They are generated when a Unicode character is entered, and are the preferred way to find out about character input. In the simplest case, a key typed event is produced by a single key press (e.g., 'a'). Often, however, characters are produced by series of key presses (e.g., SHIFT + 'a').

"*Key pressed*" and "*key released*" events are lower-level and depend on the platform and keyboard layout.

For more information see the [Key Event JavaDoc](https://docs.oracle.com/javase/8/javafx/api/javafx/scene/input/KeyEvent.html).


## Binding Simple primitive Properties

When using Simple primitive properties such as `SimpleIntegerProperty` you will find that there is no implementation of 
`StringConverter` that works with these.

If you are doing a one way (read only) binding then you can use `Bindings.convert(ObservableValue<?> observableValue). 

Example:

```java
private final SimpleIntegerProperty indicesLength = new SimpleIntegerProperty();
private Label indiciesCountLabel = new Label();

indiciesCountLabel.textProperty().bind(Bindings.convert(indicesLength));

indicesLength.set(23);
```

## ListView

### Changing item display text

```java
@FXML
private ListView<Index> indiciesListView;


@FXML
public void initialize() {
    // Setup bindings
    indiciesListView.setCellFactory(param -> new IndexListCell());
}

/**
 * Format the display of the Index list
 */
private static class IndexListCell extends ListCell<Index> {
    @Override
    public void updateItem(Index index, boolean empty) {
        super.updateItem(index, empty);
        if (index != null) {
            setText(String.format("%s - %s", index.indexCode(), index.fullName()));
        }
    }
}
```


## TableView

### (Re)Applying Sorting after an update

If you change the underlying ```ObservableList```, then you can reapply the (column) sorting that the user may have done by calling ```tableView.sort()```.

A more complete example can be seen below:

```java
fxBeans.clear();
fxBeans.addAll(beans.stream()
    .map( bean -> new TableRowFxBean(bean)).collect(Collectors.toList()));

// Restore Sorting, if applied
tableView.sort();
```

