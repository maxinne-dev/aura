# **Aura API Client: Architecture and Coding Standards**

This document is the official guide for all developers contributing to the Aura API Client project. It outlines the architectural principles, coding practices, and testing strategies that must be followed to ensure the development of a high-quality, scalable, and maintainable application. Adherence to these standards is mandatory.

## **1\. Guiding Philosophy: Built for the Long Haul**

The Aura API Client is an ambitious project with a wide scope, targeting multiple platforms and supporting a complex feature set, including a full load-testing engine and end-to-end encryption.1 To manage this complexity and ensure the project's long-term success, we are committed to a disciplined approach founded on three pillars:

1. **Clean, Decoupled Architecture**: To allow for independent development, testing, and evolution of features.
2. **Consistent, Readable Code**: To lower the barrier for new contributors and simplify maintenance.
3. **Comprehensive, Automated Testing**: To guarantee reliability and build confidence in every release.

## **2\. Project Architecture: Feature-First Clean Architecture with Riverpod**

The project follows a strict, layered architecture based on the principles of Clean Architecture, adapted for a modern Flutter application using Riverpod for state management and dependency injection.2 This architecture is organized around features ("feature-first") to promote modularity and scalability.  
The architecture is composed of four distinct layers. The fundamental rule is the **Dependency Rule**: source code dependencies can only point inwards. Nothing in an inner layer can know anything at all about an outer layer.2

### **Layer Responsibilities**

#### **2.1. Presentation Layer**

* **Contains**: Widgets and Controllers (implemented as Notifier or AsyncNotifier from Riverpod).
* **Responsibility**: This layer is responsible for everything the user sees and interacts with. Widgets are simple, declarative representations of the application state. They should contain minimal logic. Controllers respond to user input, invoke business logic from the Application or Domain layer, and manage the UI's state.5 The UI is purely a function of an immutable state object exposed by its controller.4
* **Dependencies**: Depends only on the Application and/or Domain layers.

#### **2.2. Application Layer (Optional)**

* **Contains**: Service classes.
* **Responsibility**: This layer is a mediator that orchestrates complex operations. A service may coordinate calls to multiple repositories or perform business logic that doesn't neatly fit within a single controller or repository.5 For example, a  
  SyncService might use both a UserRepository and a CollectionRepository to perform a full data synchronization. This layer is optional; for simple features where a controller can call a repository directly, it is not needed.
* **Dependencies**: Depends only on the Domain layer.

#### **2.3. Domain Layer**

* **Contains**: Entities and abstract Repository interfaces.
* **Responsibility**: This is the core of the application, containing the enterprise-wide business rules and data structures.
    * **Entities**: These are immutable data classes representing the core objects of our application (e.g., ApiRequest, Workspace). They are created using the freezed package to ensure immutability.3
    * **Repository Interfaces (Abstract Classes)**: These define the contracts for what the data layer must implement (e.g., abstract class CollectionRepository { Future\<Either\<Failure, List\<Collection\>\>\> fetchCollections(); }).
* **Dependencies**: This layer is pure Dart. It has **zero dependencies** on Flutter, any specific database, or any network client. It is the most stable and independent layer of the application.6

#### **2.4. Data Layer**

* **Contains**: Concrete Repository implementations and Data Sources.
* **Responsibility**: This layer handles the implementation details of data persistence and retrieval.
    * **Repository Implementations**: These are concrete classes that implement the interfaces defined in the Domain layer. For example, CollectionRepositoryImpl will contain the logic to fetch collections from a data source.
    * **Data Sources**: These are the classes that talk directly to the outside world, such as a local database (DriftDatabase), a REST API client (Dio), or the platform's secure storage.2
* **Dependencies**: Depends on the Domain layer (to implement its interfaces) and external packages (like drift, dio).

This strict separation is what allows the application to be so flexible. For instance, we can have a DriftCollectionRepositoryImpl for local storage and later add a CloudCollectionRepositoryImpl for cloud sync. The rest of the application, which only depends on the abstract CollectionRepository, will not need to change at all. This directly fulfills the architectural goal of supporting multiple data backends.1

## **3\. Directory Structure**

To enforce the feature-first, layered architecture, all code within lib/ must follow this structure:  
```
lib/  
├── core/ \# Shared logic, utils, base classes, core providers  
│ ├── data/ \# Core data implementations (e.g., Drift database setup)  
│ ├── domain/ \# Core domain models (e.g., Failure class)  
│ └── presentation/ \# Shared widgets, theme data  
├── features/  
│ └──
```

## 4\. Core Coding Practices & Patterns

### 4.1. SOLID Principles in Dart

All code must adhere to the SOLID principles of object-oriented design.\[7, 8\]

*   **S \- Single Responsibility Principle (SRP)**: A class should have only one reason to change.  
*   *Bad*: A single \`RequestManager\` class that both builds the UI for a request and sends it over the network.  
*   *Good*: A \`RequestScreen\` widget (builds UI), a \`RequestController\` notifier (manages state), and a \`RequestRepository\` (sends the request). Each has one responsibility.\[9\]

*   **O \- Open/Closed Principle (OCP)**: Software entities should be open for extension, but closed for modification.  
*   *Bad*: An \`AuthenticationHandler\` with a giant \`if/else if\` block for different auth types. Adding a new auth type requires modifying this class.  
*   *Good*: An abstract \`AuthProvider\` class. We can add new authentication methods by creating new classes like \`OAuthProvider\` or \`ApiKeyProvider\` that implement the interface, without modifying existing code.\[9\]

*   **L \- Liskov Substitution Principle (LSP)**: Subtypes must be substitutable for their base types.  
*   *Bad*: A \`ReadOnlyRequest\` class that inherits from \`ApiRequest\` but throws an exception if you call a \`save()\` method.  
*   *Good*: Design more specific base classes. Perhaps have a \`Readable\` interface and a \`Writable\` interface instead of forcing inheritance that breaks contracts.\[7\]

*   **I \- Interface Segregation Principle (ISP)**: Clients should not be forced to depend on interfaces they do not use.  
*   *Bad*: A giant \`DataHandler\` interface with methods for \`fetchUsers()\`, \`fetchProducts()\`, and \`fetchOrders()\`. A class that only needs users is forced to implement methods for products and orders.  
*   *Good*: Separate interfaces: \`UserRepository\`, \`ProductRepository\`, \`OrderRepository\`. Classes implement only what they need.\[9\]

*   **D \- Dependency Inversion Principle (DIP)**: High-level modules should not depend on low-level modules. Both should depend on abstractions.  
*   This is the core of our Clean Architecture.  
*   *Bad*: \`RequestController\` directly instantiates and uses \`DriftDatabase\`. This tightly couples the UI logic to a specific database implementation.  
*   *Good*: \`RequestController\` depends on the abstract \`RequestRepository\`. The actual implementation (\`DriftRequestRepositoryImpl\`) is provided via dependency injection (Riverpod), inverting the control.\[10\]

### 4.2. State Management with Riverpod

*   **Use Modern Riverpod**: All new state management logic must use \`Notifier\` and \`AsyncNotifier\`. All providers must be generated using the \`riverpod\_generator\` (\`@riverpod\` annotation). The use of legacy providers like \`ChangeNotifierProvider\` or \`StateProvider\` is forbidden.\[11\]  
*   **Immutability is Key**: All state classes exposed by notifiers **must** be immutable. The \`freezed\` package will be used to generate these classes. State is never mutated directly; instead, a new state object is created (e.g., \`state \= state.copyWith(...)\`).\[3\]  
*   **\`watch\` vs. \`read\`**: Use \`ref.watch\` only within the \`build\` method of a widget to listen for changes and trigger rebuilds. Use \`ref.read\` inside callbacks (like \`onPressed\`) to get the current state of a provider without subscribing to it. Incorrect usage of \`watch\` in callbacks is a common source of bugs and performance issues.\[12\]  
*   **Provider Scoping**: Providers should be declared as close to their usage as possible, typically within the feature directory they belong to. Global providers should be rare and placed in \`lib/core/\`.

### 4.3. Error Handling

To ensure robustness, a unified error handling strategy is enforced.

*   **Return Types**: Methods in the Data and Application layers that can fail (e.g., network calls, database queries) **must not** throw exceptions directly. Instead, they must return a \`Future\<Either\<Failure, SuccessType\>\>\`, using the \`dartz\` package.\[3\] \`Failure\` is a custom class that can encapsulate different error types (e.g., \`ServerFailure\`, \`CacheFailure\`).  
*   **UI Handling**: In the Presentation layer, Riverpod's \`AsyncValue\` (\`AsyncData\`, \`AsyncLoading\`, \`AsyncError\`) is the standard way to handle the states of an asynchronous operation. A controller's \`build\` method will typically return a \`Future\` that resolves to the \`Either\` type, which can be easily mapped to \`AsyncValue\` using \`AsyncValue.guard\`.\[13\] This forces the UI to handle loading and error states explicitly.

### 4.4. Code Style & Linting

*   **Linting**: The project strictly follows the rules defined in the \`flutter\_lints\` package. The \`analysis\_options.yaml\` file is the source of truth for these rules.  
*   **Immutability by Default**: Use \`final\` for all local variables, class fields, and parameters that are not reassigned. Use \`const\` wherever possible, especially for widgets in the UI layer, to improve performance by preventing unnecessary rebuilds.

## 5\. The Testing Pyramid: A Strategy for Quality

A comprehensive and balanced testing suite is non-negotiable. Our strategy is based on the Testing Pyramid, which dictates the proportion of different types of tests we should write.\[14, 15\]

#### 5.1. Unit Tests (\~70%)  
*   **Scope**: Test a single function, method, or class (\`unit\`) in isolation.  
*   **Location**: Primarily for classes in the **Domain** and **Data** layers (Repositories, Use Cases, Entities, Models).  
*   **Tools**: \`flutter\_test\`, \`mocktail\` for mocking dependencies.\[16\]  
*   **Characteristics**: Very fast, cheap to write, and provide granular feedback. They form the foundation of our test suite.\[17\]

#### 5.2. Widget Tests (\~20%)  
*   **Scope**: Test a single widget. These tests verify that the widget's UI renders correctly for a given state and that interactions with the widget (taps, text input) trigger the correct callbacks or controller methods.  
*   **Location**: For widgets and screens in the **Presentation** layer.  
*   **Tools**: \`flutter\_test\` framework's \`WidgetTester\`. We can provide mock implementations of controllers and repositories to test the widget in isolation.  
*   **Characteristics**: Faster than integration tests but slower than unit tests. They ensure our UI components are correct and responsive.\[18\]

#### 5.3. Integration Tests (\~10%)  
*   **Scope**: Test a complete feature or a critical user flow from end-to-end. These tests run on a real device or emulator and interact with the app just like a real user.  
*   **Location**: These tests cover all layers of the application, from the UI down to the database and network.  
*   **Tools**: \`integration\_test\` package.  
*   **Characteristics**: Slow, potentially brittle, and expensive to write and maintain. We write them sparingly, focusing only on the most critical paths of the application (e.g., the full flow of sending a request and verifying the response).\[19\] They provide the highest level of confidence that the application works as a whole.
