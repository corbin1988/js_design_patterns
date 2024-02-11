# General Software Design Patterns

## Table of content

- [Introduction](#introduction)
- [Creational Patterns](#creational-patterns)
  - [Singleton Pattern](#singleton-pattern)
    - [Example A](#example-a)
    - [Example B](#example-b)
    - [Example C](#example-c)
  - [Prototype Pattern](#prototype-pattern)
    - [Example A](#example-a-1)
    - [Example B](#example-b-1)
    - [Example C](#example-c-1)
  - [Builder Pattern](#builder-pattern)
    - [Example A](#example-a-2)
    - [Example B](#example-b-2)
    - [Example C](#example-c-2)
- [Structural Patterns](#structural-patterns)
  - [Facade Pattern](#facade-pattern)
    - [Example A](#example-a-3)
    - [Example B](#example-b-3)
    - [Example C](#example-c-3)
  - [Proxy Pattern](#proxy-pattern)
    - [Example A](#example-a-4)
    - [Example B](#example-b-4)
    - [Example C](#example-c-4)
- [Behavioural Patterns](#behavioural-patterns)
  - [Iterator Pattern](#iterator-pattern)
    - [Example A](#example-a-5)
    - [Example B](#example-b-5)
    - [Example C](#example-c-5)
  - [Observer Pattern](#observer-pattern)
    - [Example A](#example-a-6)
    - [Example B](#example-b-6)
    - [Example C](#example-c-6)
  - [Mediator](#mediator)
    - [Example A](#example-a-7)
    - [Example B](#example-b-7)
    - [Example C](#example-c-7)
  - [State](#state)
    - [Example A](#example-a-8)
    - [Example B](#example-b-8)
    - [Example C](#example-c-8)
 
## Introduction

Design patterns originated in Object-Oriented Programming (OOP), notably in languages like Java and C++, to address recurring design challenges, enhancing code reusability, maintainability, and scalability. Over time, the concept transcended OOP and became applicable to a broader range of programming languages, including those not inherently object-oriented.

## Creational Patterns

Creational design patterns are like blueprints for creating objects in a way that's flexible, reusable, and organized. They help you manage the process of creating objects in your code. 

### Singleton Pattern

A singleton is a design pattern used in software development to ensure that a class has only one instance and provides a global point of access to that instance. It's like a manager overseeing a critical aspect of the system, and no matter how many times you request that manager, you always get the same instance. This pattern is often used when having multiple instances of a particular class could lead to issues, and a single, shared instance is desired for coordination and control.

<img width="979" alt="Singleton_Pattern" src="https://github.com/corbin1988/js_design_patterns/assets/37128501/c7781a0b-00b8-4dde-bd9d-d0a497a6e60c">

#### Real World Use Examples

- **Master Control Panel in a Power Plant:** A central control panel managed by operators to monitor and control critical systems in a power plant, ensuring unified control.
- **Master Copy of a Key:** A locksmith's master key that opens all locks within a building, ensuring a single controlled point of access.
- **Presidential Nuclear Code Card:** The unique card held by the President to authorize the use of nuclear weapons, ensuring singular control over a critical decision.

#### Backend Use Examples

- **User Session Manager:** A singleton managing user sessions, ensuring there is a single point of control for user authentication and authorization.
- **Database Connection Pool:** A single shared pool of database connections to ensure efficient and controlled access across the application.
- **Configuration Manager:** A singleton managing configuration settings for an application, providing a centralized point for configuration data.

### Frontend Use Examples

- **State Management in React/Angular/Vue:** A single state management instance, such as Redux store/Vuex/NGRX, to manage and centralize the state for a React/Angular/Vue application.
- **Theme Manager:** A singleton managing the application's theme, providing a consistent look and feel across various components.
- **Modal or Dialog Controller:** Controller managing the display and behavior of modals or dialogs within the application.

#### Presidential Nuclear Code Card Example

```JS
// Presidential Nuclear Code Card Singleton
var PresidentialNuclearCodeCard = (function () {
  var instance;
  var isCodeAuthorized = false;

  function createInstance() {
    return {
      // Public method to authorize the use of nuclear weapons
      authorizeNuclearCode: function () {
        if (!isCodeAuthorized) {
          isCodeAuthorized = true;
          console.log("Presidential nuclear code authorized. Proceed with caution.");
        } else {
          console.log("Presidential nuclear code is already authorized.");
        }
      },
      // Public method to deauthorize the use of nuclear weapons
      deauthorizeNuclearCode: function () {
        isCodeAuthorized = false;
        console.log("Presidential nuclear code deauthorized.");
      }
    };
  }

  return {
    // Public method to get the instance
    getInstance: function () {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// Usage
var presidentCodeCard = PresidentialNuclearCodeCard.getInstance();
presidentCodeCard.authorizeNuclearCode(); // Presidential nuclear code authorized. Proceed with caution.

var vicePresidentCodeCard = PresidentialNuclearCodeCard.getInstance();
vicePresidentCodeCard.authorizeNuclearCode(); // Presidential nuclear code is already authorized.

presidentCodeCard.deauthorizeNuclearCode(); // Presidential nuclear code deauthorized.
```

#### User Session Manager Example

```JS
const express = require('express');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

// User Session Manager Singleton
const UserSessionManager = (function () {
  let instance;
  const activeSessions = {};

  function createInstance() {
    return {
      // Public method to create a new user session
      createSession: function (userId) {
        const sessionId = generateSessionId();
        activeSessions[sessionId] = { userId, createdAt: new Date() };
        console.log(`User session created for user ID ${userId} with session ID ${sessionId}`);
        return sessionId;
      },
      // Public method to get user information from a session
      getSessionInfo: function (sessionId) {
        return activeSessions[sessionId] || null;
      },
      // Public method to end a user session
      endSession: function (sessionId) {
        delete activeSessions[sessionId];
        console.log(`User session ended for session ID ${sessionId}`);
      }
    };
  }

  // Private method to generate a unique session ID
  function generateSessionId() {
    return Math.random().toString(36).substring(2, 15) + Math.random().toString(36).substring(2, 15);
  }

  return {
    // Public method to get the instance
    getInstance: function () {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// Routes
app.post('/login', (req, res) => {
  const { userId } = req.body;

  // Create a new user session
  const sessionManager = UserSessionManager.getInstance();
  const sessionId = sessionManager.createSession(userId);

  res.json({ sessionId });
});

app.get('/user/:sessionId', (req, res) => {
  const { sessionId } = req.params;

  // Get user information from the session
  const sessionManager = UserSessionManager.getInstance();
  const sessionInfo = sessionManager.getSessionInfo(sessionId);

  if (sessionInfo) {
    res.json({ userId: sessionInfo.userId, createdAt: sessionInfo.createdAt });
  } else {
    res.status(404).json({ error: 'Session not found' });
  }
});

app.post('/logout/:sessionId', (req, res) => {
  const { sessionId } = req.params;

  // End the user session
  const sessionManager = UserSessionManager.getInstance();
  sessionManager.endSession(sessionId);

  res.json({ message: 'User logged out successfully' });
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

#### State For A Task Manager

```JS
// Task Manager State Singleton
const TaskManagerState = (function () {
  let instance;
  let state = {
    taskList: []
  };

  function createInstance() {
    return {
      // Public method to get the current state
      getCurrentState: function () {
        return { ...state };
      },
      // Public method to update the state
      updateState: function (newState) {
        state = { ...state, ...newState };
      }
    };
  }

  return {
    // Public method to get the instance
    getInstance: function () {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// Usage
const taskManagerState = TaskManagerState.getInstance();

// Initial state
console.log(taskManagerState.getCurrentState()); // { taskList: [] }

// Update the state by adding a new task
taskManagerState.updateState({
  taskList: [
    { id: 1, title: 'Complete assignment', completed: false },
    { id: 2, title: 'Read a book', completed: true }
  ]
});

// Get the updated state with tasks
console.log(taskManagerState.getCurrentState());
```

### Prototype Pattern

Prototype is a creational design pattern that lets you copy existing objects without making your code dependent on their classes. This pattern is particularly useful when the cost of creating an object is more expensive or complex than copying an existing one. For example, imagine constructing an object that requires extensive database queries, network requests, or intricate configuration. Prototypes help mitigate this expense by providing a blueprint or template for objects. Instead of repeatedly performing costly operations to create new objects, you can use an existing object, the prototype, as a basis. By copying the prototype, you replicate the object's structure and behavior without re-executing the expensive construction process. This not only saves resources but also streamlines the object creation, making it more efficient and less demanding on computational resources.

**Note**

In JavaScript, the prototype pattern is built into the language because JavaScript uses something called "prototypal inheritance." This means that every object in JavaScript is connected to a prototype object, which is like a blueprint. When you create a new object in JavaScript, it automatically inherits properties and methods from its blueprint, thanks to this prototypal inheritance. This natural connection makes implementing the prototype pattern straightforward in JavaScript.

#### Real World Use Examples

- **Photocopier Machine in an Office:** A photocopier machine serves as a prototype, and when multiple copies of a document are needed, users can simply duplicate the prototype to save time and resources, ensuring consistent copies.

- **Architectural Blueprint for Houses:** An architectural blueprint acts as a prototype for constructing houses. Instead of designing each house from scratch, builders replicate the blueprint, ensuring a consistent design while saving effort and time.

- **Recipe Book for a Chef:** In a kitchen, a chef's recipe book serves as a prototype for creating various dishes. Chefs can replicate recipes, adjusting ingredients as needed, to maintain a consistent cooking style and save time on recipe creation.

#### Backend Use Examples

- **Database Record Copying:** When creating new records in a database with similar attributes to existing ones, using the Prototype Pattern can involve cloning an existing record to create a new one, reducing the need for redundant database queries.
- **User Account Initialization:** When setting up new user accounts with similar roles or permissions, the Prototype Pattern can be employed to clone an existing user account, reducing the effort required to set up access permissions and preferences.
- **API Response Objects:** When designing APIs, response objects with similar structures can be created using the Prototype Pattern. Instead of constructing each response from scratch, a prototype object can be cloned and customized for different API endpoints.

#### Frontend Examples

#### Architectural Blueprint Example

```JS
// Architectural Blueprint (Prototype)
function HouseBlueprint() {
    this.structure = [];
    this.windows = 0;
    this.doors = 0;
}

// Prototype method to clone the blueprint
HouseBlueprint.prototype.clone = function () {
    const copy = new HouseBlueprint();
    copy.structure = [...this.structure];
    copy.windows = this.windows;
    copy.doors = this.doors;
    return copy;
};

// Builder using the Prototype Pattern
const houseBuilder = {
    constructHouse: function (blueprint) {
        const newHouse = blueprint.clone();
        // Additional construction logic can be added here if needed
        return newHouse;
    }
};

// Example usage
const basicHouseBlueprint = new HouseBlueprint();
basicHouseBlueprint.structure = ['Living Room', 'Bedroom', 'Kitchen'];
basicHouseBlueprint.windows = 5;
basicHouseBlueprint.doors = 3;

const house1 = houseBuilder.constructHouse(basicHouseBlueprint);
const house2 = houseBuilder.constructHouse(basicHouseBlueprint);

console.log(house1.structure); // Output: [ 'Living Room', 'Bedroom', 'Kitchen' ]
console.log(house2.windows);    // Output: 5
```


#### Example B
#### Example C

### Builder Pattern
#### Example A
#### Example B
#### Example C

## Structural Patterns


Structural design patterns act like handy guides that simplify design tasks. They help arrange classes and objects to build bigger, more intricate structures, all while keeping the system flexible and efficient. These patterns provide solutions for organizing and combining different parts of your code, making it clearer, more reusable, and adaptable. 

### Facade Pattern
#### Example A
#### Example B
#### Example C

### Proxy Pattern
#### Example A
#### Example B
#### Example C

## Behavioural Patterns

Behavioral design patterns are concerned with the interaction and responsibility of objects. They focus on defining how objects collaborate, communicate, and share responsibilities to achieve a more understandable and adaptable system. These patterns guide the dynamics of your software, shaping how different components work together to accomplish specific tasks. Imagine them as scripts that not only direct the interactions between actors but also outline their roles and responsibilities in the overall performance of your software play.

### Iterator Pattern
#### Example A
#### Example B
#### Example C

### Observer Pattern
#### Example A
#### Example B
#### Example C

### Mediator
#### Example A
#### Example B
#### Example C

### State
#### Example A
#### Example B
#### Example C


