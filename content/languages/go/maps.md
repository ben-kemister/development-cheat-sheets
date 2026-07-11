---
title: Maps
date: "2026-07-12"
tags:
- go
- maps
---

Maps in Go are a built-in data structure that implements associative arrays, often referred to as hash tables or dictionaries in other languages. 
<!--more-->
They store data in key-value pairs, where each key must be unique and comparable.

A map is a reference type, meaning it points to an underlying data structure. Because they are reference types, when you 
pass a map to a function, the function receives a pointer to the same underlying data.

## Declaration and Initialization

You cannot use a map until it has been initialized. A declared but uninitialized map (a `nil` map) can be read from, 
but attempting to write to a `nil` map will cause a runtime panic.

### Using `make`
The `make` function is the most common way to initialize a map. It allocates the underlying data structure.

```textmate
// Initializing an empty map using make
// make(map[KeyType]ValueType)
scores := make(map[string]int)

// Initializing with an initial capacity for better performance if size is known
users := make(map[string]string, 100)
```


### Map Literals
You can also initialize a map with starting values using a map literal.

```textmate
// Initializing with specific data
capitals := map[string]string{
"France":  "Paris",
"Japan":   "Tokyo",
"Germany": "Berlin",
}
```

## Basic Operations

### Adding and Updating
Adding a new entry or updating an existing entry uses the same syntax.

```textmate
m := make(map[string]int)

// Adding new entries
m["apples"] = 5
m["oranges"] = 10

// Updating an existing entry
m["apples"] = 15
```


### Retrieving Values
You can access a value by providing its key. If the key does not exist, the map returns the zero value for the value type.

```textmate
count := m["apples"] // Returns 15
empty := m["bananas"] // Returns 0 (the zero value for int)
```


### Deleting Elements
The built-in `delete` function removes an element from the map. If the key does not exist, `delete` does nothing.

```textmate
delete(m, "oranges")
```


## The "Comma Ok" Idiom

Because retrieving a missing key returns a zero value, it can be difficult to distinguish between a key that is missing and a key that is actually stored with the zero value. To solve this, Go provides the "comma ok" idiom.

```textmate
val, ok := m["apples"]
if ok {
fmt.Printf("Found apples: %d\n", val)
} else {
fmt.Println("Apples not found")
}
```


## Iteration

You can iterate over a map using a `for range` loop. Note that **map iteration order is non-deterministic**; you should never rely on the order of elements when looping over a map.

```textmate
for key, value := range m {
fmt.Printf("%s: %d\n", key, value)
}

// You can also iterate over keys only
for key := range m {
fmt.Println("Key:", key)
}
```

> Note: If you need to iterate over a map in a specific order, you must first extract the keys into a slice, sort that slice, 
> and then iterate over the keys.

## Summary Table

| Operation           | Syntax                | Description                                              |
|:--------------------|:----------------------|:---------------------------------------------------------|
| **Initialize**      | `make(map[K]V)`       | Creates an empty, ready-to-use map                       |
| **Literal**         | `map[K]V{...}`        | Creates a map with predefined values                     |
| **Set/Update**      | `m[key] = value`      | Adds or updates a key-value pair                         |
| **Get**             | `val := m[key]`       | Retrieves value (returns zero-value if missing)          |
| **Check Existence** | `val, ok := m[key]`   | Returns the value and a boolean indicating if key exists |
| **Delete**          | `delete(m, key)`      | Removes the entry for the specified key                  |
| **Iterate**         | `for k, v := range m` | Loops through all key-value pairs (unordered)            |

## Handy Links

* [Go Language Specification - Maps](https://golang.org/ref/spec#Map_types) - The official technical definition of how maps work.
* [A Tour of Go](https://tour.golang.org/moretypes/12) - An interactive tutorial covering Go types.