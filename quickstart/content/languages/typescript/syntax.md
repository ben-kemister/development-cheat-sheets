---
title: Basic Syntax
tags:
- typescript
---

This page contains information about the basics of TypeScript syntax.

# Describing your code with types

The general rule of thumb about using types is the more information you can provide the more the tooling will be able to help you.

## Variables

Simply add a semicolon after the variable name and then the type.

```typescript
let trackingNumber: string = 'FD1234567';
let createDate: Date = new Date();
```

## Functions and parameters

```typescript
function getItem(trackingNumber: string): object {
    // Do stuff in here...
    return null;
}

// Using a predefined type or interface
function getItemBetter(trackingNumber: string): InventoryItem {
    // Do stuff in here...
    return null;
}   
```

You can also define the structure of the return object **in line** as follows:

```typescript
function getItem(trackingNumber: string): {
    displayName: string;
    trackingNumber: string;
    createDate: Date;
    originalCost: number;
} {
    // Do stuff in here...
    return null;
}
```

## Casting to different Types

The `as` keyword allows you to cast between different types.

## The *any* type

The `any` type allows you to bypass TypeScript's inbuilt type checking allowing you to use the dynamic typing native to JavaScript.

Bewared that using the `any` type you are not using TypeScripts ability to help with typing checking, effectively opting out of type checking.

# Interfaces

Interfaces allow you to define the structure of a type in TypeScript.

Note that interface definitions do not end up in the compiled JavaScript code.

```typescript
interface InventoryItem {
    displayName: string;
    trackingNumber: string;
    createDate: Date;
    originalCost: number;
} 
```

## Method definitions

You can define method definitions within an interface in two ways:

```typescript
interface InventoryItem {
    displayName: string;
    trackingNumber: string;
    createDate: Date;
    originalCost: number;

    // addNote method definition #2
    addNote: (note: string) => string;
} 
```

## Optional properties

You can mark properties and methods as optional in your interface by using the question mark ( `?` ).

```typescript
interface InventoryItem {
    displayName: string;
    trackingNumber: string;
    createDate: Date;
    // Optional Property
    originalCost?: number;

    // Optional method
    addNote?: (note: string) => string;
} 
```

## Read only properties

You can mark properties of an object as being read only by using the `readonly` keyword.

TypeScript will throw an error if you try to change a readonly property to a new value after it has been defined.

```typescript
interface InventoryItem {
    displayName: string;
    // Read Only property
    readonly trackingNumber: string;
    createDate: Date;
    originalCost?: number;

    addNote?: (note: string) => string;
} 
```

# Enumerations (a.k.a. *enum*)

An enum type is a type which defines a set of known values.

```typescript
enum InventoryItemType {
    Computer,
    Furniture
}
```

By default TypeScript assigns the values of enums to numbers. You can override this default behavior and set the values to something else, such as Strings.

```typescript
enum InventoryItemType {
    Computer = 'computer',
    Furniture = 'furniture'
}
```

# Literal types

Literal Types act similarly to `enum`s in that you can define a restricted set of values for a particular property.

Literal Types tend require less code to setup than an `enum`.

```typescript
interface InventoryItem {
    displayName: string;
    // Literal Types
    inventoryType: 'computer' | 'furniture'
    readonly trackingNumber: string;
    createDate: Date;
    originalCost?: number;

    addNote?: (note: string) => string;
} 
```

# Multiple types (Union types)

You can define a property which can be one of many types (union types) using the pipe ( `|` ).

```typescript
let originalCost: number | string = 425;
orignalCost = 'Sooooo much money!';
```

You can also define a `type` so that you can reuse this union in multiple locations or definitions with a meaningful name.

```typescript
type Cost = number | string;
```

Be careful when assigning union types to another (single typed) variable as TypeScript will throw an error. In this case you need to do some type checking before the assignment.

```typescript
type Cost = number | string;

let originalCost: Cost;

if (typeof originalCost === 'number'){
    let cost: number = originalCost;
}
```

# Classes

Although it is a very common JavaSCript pattern to **define** properties in the objects constructor this is not allowed in TypeScript.

In TypeScript you must declare the class's properties at the class level for example:

```typescript
class InventoryStore {

    // You need to define a class's properties at this level in TypeScript
    _categories;
    _items;

    constructor(){
        this._categories = [];
        this._items = [];
    }
}
```
