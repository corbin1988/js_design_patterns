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

<img width="899" alt="Prototype_Pattern" src="https://github.com/corbin1988/js_design_patterns/assets/37128501/d75fea8f-8c86-4787-b3b0-b378edbcb99e">


#### Real World Use Examples

- **Photocopier Machine in an Office:** A photocopier machine serves as a prototype, and when multiple copies of a document are needed, users can simply duplicate the prototype to save time and resources, ensuring consistent copies.
- **Architectural Blueprint for Houses:** An architectural blueprint acts as a prototype for constructing houses. Instead of designing each house from scratch, builders replicate the blueprint, ensuring a consistent design while saving effort and time.
- **Recipe Book for a Chef:** In a kitchen, a chef's recipe book serves as a prototype for creating various dishes. Chefs can replicate recipes, adjusting ingredients as needed, to maintain a consistent cooking style and save time on recipe creation.

#### Backend Use Examples

- **Database Record Copying:** When creating new records in a database with similar attributes to existing ones, using the Prototype Pattern can involve cloning an existing record to create a new one, reducing the need for redundant database queries.
- **User Account Initialization:** When setting up new user accounts with similar roles or permissions, the Prototype Pattern can be employed to clone an existing user account, reducing the effort required to set up access permissions and preferences.
- **API Response Objects:** When designing APIs, response objects with similar structures can be created using the Prototype Pattern. Instead of constructing each response from scratch, a prototype object can be cloned and customized for different API endpoints.

#### Frontend Examples

- **Form Validation in Web Forms:** When creating forms with similar validation logic, the Prototype Pattern can be used to create a prototype validation object. New validation objects for different forms can then be created by cloning this prototype, ensuring consistent validation across the application.
- **Game Development (Web Games):** In web-based game development, the Prototype Pattern is commonly used for creating game entities. Entities with similar behaviors and attributes can be cloned from prototype entities, ensuring consistent gameplay elements.For example Entity and Components with (ECS)


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


#### API Response Example

```JS
// API Response Prototype
function APIResponse(status, data) {
    this.status = status;
    this.data = data;
}

// Prototype method to clone the API Response
APIResponse.prototype.clone = function() {
    return new APIResponse(this.status, this.data);
};

// Sample API Response Prototypes
const successResponsePrototype = new APIResponse('success', null);
const errorResponsePrototype = new APIResponse('error', null);

// API Response Factory
function createAPIResponse(status, data) {
    // Choose the appropriate prototype based on the status
    const prototype = status === 'success' ? successResponsePrototype : errorResponsePrototype;
    // Clone the prototype and customize as needed
    const response = prototype.clone();
    response.data = data;
    return response;
}

// Example Usage
const successResponse1 = createAPIResponse('success', { message: 'Data retrieved successfully.' });
const successResponse2 = createAPIResponse('success', { message: 'Data saved successfully.' });

const errorResponse1 = createAPIResponse('error', { message: 'Invalid input.' });
const errorResponse2 = createAPIResponse('error', { message: 'Internal server error.' });

console.log(successResponse1);
console.log(successResponse2);
console.log(errorResponse1);
console.log(errorResponse2);
```

#### Form Validation Example

```JS
// Validation Prototype
function FormValidationPrototype() {
    this.rules = {};
}

// Prototype method to clone the validation rules
FormValidationPrototype.prototype.clone = function () {
    const copy = new FormValidationPrototype();
    copy.rules = { ...this.rules };
    return copy;
};

// Sample Validation Rules
const commonValidationRules = new FormValidationPrototype();
commonValidationRules.rules = {
    username: {
        required: true,
        minLength: 3,
    },
    password: {
        required: true,
        minLength: 6,
    },
    email: {
        required: true,
        pattern: /\S+@\S+\.\S+/,
    },
};

// Function to create form validation objects
function createFormValidation() {
    // Clone the common validation rules prototype
    const validation = commonValidationRules.clone();
    // Additional or customized validation rules can be added here
    return validation;
}

// Example Usage
const loginFormValidation = createFormValidation();
// Customize validation rules for the login form
loginFormValidation.rules.username.maxLength = 10;

const registrationFormValidation = createFormValidation();
// Customize validation rules for the registration form
registrationFormValidation.rules.password.confirmation = true;

console.log(loginFormValidation.rules);
console.log(registrationFormValidation.rules);
```

### Builder Pattern

The Builder pattern separates the construction layer of a complex object from its representation layer, so that the same construction process can use different representations.

The builder pattern features a step-by-step construction of a complex object that can be built in different combinations or sequences with different meanings. Usually the user does not need to know the details of the construction and usually uses chain calls to carry out the construction process and finally calls the build method to generate the final object.

#### Example

Let’s assume a business scenario of a publisher’s book backend entry system. Books have four required information: title, author, price, and category; we want to create a book object to return to the backend.

```JS
class BookBuilder {
  constructor() {
    this.name = '';
    this.author = '';
    this.price = 0;
    this.category = '';
  }
  
  withName(name) {
    this.name = name;
    return this;
  }

  withAuthor(author) {
    this.author = author;
    return this;
  }

  withPrice(price) {
    this.price = price;
    return this;
  }

  withCategory(category) {
    this.category = category;
    return  this;
  }

  build() {
    return {
      name: this.name,
      author: this.author,
      prices: this.price,
      category: this.category
    }
  }
}

//Calling the builder class
const book = new BookBuilder()
  .withName("The Reckonings")
  .withAuthor('Lacy Johnson')
  .withPrice(31)
  .withCategory('Literature')
  .build();
```

### Factory Pattern

The Factory Design Pattern is a creational pattern in software design that provides an interface for creating objects in a super class, but allows subclasses to alter the type of objects that will be created. It falls under the category of design patterns that deal with object creation mechanisms, forming a basis for code that is more understandable, flexible, and maintainable.

#### Three main types of Factory Design Patterns

- **Simple Factory:** A simple factory is not technically a design pattern but a method for creating objects. It involves a single factory class responsible for creating objects based on the input parameters. While simple, it doesn't adhere strictly to the principles of other design patterns.

- **Factory Method:** The Factory Method pattern defines an interface for creating an object but leaves the choice of its type to the subclasses, creating an instance of one or more derived classes.

- **Abstract Factory:** The Abstract Factory pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes. It is often used to ensure the compatibility of created objects.

#### Example

```JS
// Product Interface
class Product {
  getProductInfo() {
    throw new Error("This method must be overridden by subclasses");
  }
}

// Concrete Product
class ConcreteProduct extends Product {
  getProductInfo() {
    return "Concrete Product";
  }
}

// Creator Interface
class Creator {
  createProduct() {
    throw new Error("This method must be overridden by subclasses");
  }

  operation() {
    const product = this.createProduct();
    return product.getProductInfo();
  }
}

// Concrete Creator
class ConcreteCreator extends Creator {
  createProduct() {
    return new ConcreteProduct();
  }
}

// Example Usage
const creator = new ConcreteCreator();
const result = creator.operation();
console.log(result); // Output: Concrete Product
```

- **Product:** Product is an interface or abstract class defining the product's interface.
- **ConcreteProduct:** ConcreteProduct is a concrete implementation of the product interface.
- **Creator:** Creator is an interface or abstract class declaring the factory method, which returns a Product object.
- **ConcreteCreator:** ConcreteCreator is a concrete implementation of the creator interface, providing the implementation for the factory method.


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


