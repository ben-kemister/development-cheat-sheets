---
title: "Postman"
date: 2024-05-29
tags: ["Postman", "UUID", "API", "Automation", "DynamicVariables", "DevTools"]
---

[Postman](https://www.postman.com/) is a comprehensive API (Application Programming Interface) platform which simplifies 
the process of sending HTTP requests and viewing server responses without requiring users to write code.
<!--more-->
 
# 🚀 Introduction to Postman

Postman is a comprehensive API (Application Programming Interface) platform developed by an American software company. 
It simplifies the process of sending HTTP requests and viewing server responses without requiring users to write code.

**Key Features of Postman:**
*   **API Client:** Easily send requests using various protocols like HTTP, REST, GraphQL, and gRPC.
*   **Collections:** Organize frequently used API requests into reusable sets.
*   **Dynamic Variables/Environments:** Store and manage environment-specific data (like API keys or server URLs) to ensure requests are flexible and reusable across different stages (e.g., Development, Staging, Production).
*   **Automated Testing:** Write JavaScript-based scripts to validate API performance, check response codes, and ensure data integrity.
*   **Mock Servers:** Simulate API behavior before the backend is fully built.

## ⚙️ Generating Requests with Dynamic Variables

Postman utilizes a built-in **Faker library** that enables the dynamic generation of variables, such as Universally Unique Identifiers (UUID).

### Available Variables

| Variable Name        | Description                      | Example Output                           | 
|:---------------------|:---------------------------------|:-----------------------------------------|
| **`{{guid}}`**       | A standard `uuid-v4` style GUID. | `611c2e81-2ccb-42d8-9ddc-2d0bfa65c1b4`   |
| **`{{timestamp}}`**       | The current UNIX timestamp in seconds. | `1562757107`, `1562757108`, `1562757109` |
| **`{{isoTimestamp}}`**       | The current ISO timestamp at zero UTC. | `2020-06-09T21:10:36.177Z`               |
| **`{{randomUUID}}`** | A random 36-character UUID.      | `6929bb52-3ab2-448a-9796-d6480ecad36b`   |

> **Note:** For use within Pre-request or Post-response scripts, variable names must be accessed using `pm.variables.replaceIn()`.

## 🛠️ UUID Implementation Methods

### 1. Generate variable on Every Request (Dynamic Variable)

This method is ideal when you require a unique, randomized UUID (v4) for every single API call.

*   **Action:** Directly insert the dynamic variable into the URL path or query parameters of the request.
*   **Example (URL Path):** `https://api.example.com/resources/{{randomUUID}}`
*   **Example (Query Parameter):** `https://api.example.com/items?id={{guid}}`

### 2. Reuse the Same Generated Variable (Pre-request Script)

If your workflow necessitates generating a single ID and using it across multiple subsequent calls, you must persist the ID using a Pre-request Script.

*   **Action:** Use the Pre-request Script tab to generate the UUID and save it as a Collection or Environment variable.
*   **Script Example:**
    ```javascript
    // Generate a UUID and save it to a custom variable 'currentId'
    pm.environment.set("currentId", pm.variables.replaceIn('{{randomUUID}}'));
    ```
*   **Reference in URL:** `https://api.example.com/resources/{{currentId}}`

### 3. Extract UUID from Previous Response (Tests Tab)

When the initial API endpoint (e.g., a "create" call) returns a new UUID in its JSON response, you can capture and store it for future interactions.

*   **Action:** Use the Tests tab on the initial request to parse the JSON response and save the ID as a persistent variable.
*   **Script Example:**
    ```javascript
    // Parse the JSON response and save the 'id' field to an environment variable
    const response = pm.response.json();
    pm.environment.set("userId", response.id);
    ```
*   **Reference in Subsequent URL:** `https://api.example.com/user/{{userId}}`

## 🔗 Further Reading and Resources

For more detailed information on Postman scripting and variable handling, consult the following documentation and community guides:

*   **Postman Dynamic Variables List:** [Postman Learning Center](https://learning.postman.com/docs/variables-list)
*   **Postman Random Data Docs:** [Postman Docs - Random Data](https://learning.postman.com/docs/tests-and-scripts/variables-list)
*   **Advanced Scenarios (Charles Desneuf Blog):** [Postman Dynamic Variables Guide](https://blog.charlesdesneuf.com)
*   **Official Postman Forum:** [Community Support](https://community.postman.com/)