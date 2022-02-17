---
tool: jest
name: Jest
tags: 
 - javascript
 - testing
 - frameworks
--- 

[Jest](https://jestjs.io/en/) is a JavaScript Testing Framework with a focus on simplicity. It is compatible with many frameworks (including node) and TypeScript.
<!--more-->
# Mocks

You can get Jest to create an automatic mock for a module simply by adding `jest.mock('./moduleToMock')` to your test module/file.

With automatic mocks Jest replaces the ES6 class with a mock constructor, and replaces all of its methods with mock functions that always return `undefined`.

See the [Jest mocks page for more details](https://jestjs.io/docs/en/es6-class-mocks).

## Manual Mocks

Manual mocks are used to *stub out* functionality with mock data. For example, instead of accessing a remote resource like a website or a database, you might want to create a manual mock that allows you to use fake data.

For more details see the [Jest manual mock page](https://jestjs.io/docs/en/manual-mocks).

### Mock Folder Structure

```
.
├── config
├── __mocks__
│   └── fs.js (For mocking node modules)
├── models
│   ├── __mocks__
│   │   └── user.js
│   └── user.js
├── node_modules
└── views
```
