# Domain-Driven Design (DDD)

This README provides an overview of Domain-Driven Design (DDD), a philosophy and set of practices aimed at accelerating software projects that deal with complex domains. It emphasizes placing the primary focus on the **domain and domain logic**, basing complex domain designs on a **model**.

DDD is particularly suited for projects that require a sophisticated approach to the design of domain logic, allowing complex software to evolve rather than ossify prematurely into high-maintenance legacy systems. It is oriented toward modern Agile Development Processes, assuming iterative development and a close relationship between developers and domain experts.

---

## 1. Core Concepts of DDD

At its heart, DDD revolves around several fundamental concepts:

### 1.1. The Domain Model
The **domain model** is a selectively simplified and consciously structured form of knowledge, an interpretation of reality that abstracts relevant aspects and ignores extraneous detail. It's not merely a diagram, but the underlying idea the diagram conveys, the rigorously organized and selective abstraction of knowledge held by domain experts.

The utility of a model in DDD is threefold:
*   **Dictates Design:** The model dictates the form of the software's core design, providing an intimate link between the model and its implementation, ensuring analysis applies to the final product.
*   **Backbone of Language:** It serves as the backbone of a language used by all team members, enabling communication without translation between developers and domain experts.
*   **Distilled Knowledge:** The model is the team's agreed-upon way of structuring domain knowledge, capturing how the team chooses to think about the domain.

### 1.2. Knowledge Crunching
**Knowledge crunching** is a collaborative process where a team of developers and domain experts work together to draw in information and "crunch" it into a useful, formalized model. This process is iterative, with early versions or prototypes feeding experience back into the team to refine the model. Unlike the old waterfall method, it emphasizes feedback and continuous accumulation of knowledge, preventing a shallow understanding of the domain.

### 1.3. Ubiquitous Language (UL)
The **Ubiquitous Language** is a language structured around the domain model, used relentlessly by all team members in all communication, including speech, diagrams, documents, and especially the code.

*   **Purpose:** It bridges the linguistic divide between domain experts and developers, preventing inaccuracies, disconnects, and "schisms" where terms are used differently. It is more than a lowest common denominator; it's a model-based language that provides a shared vocabulary for discussing tasks, functionality, and design decisions.
*   **Cultivation:** Persistent use of the language exposes its weaknesses, forcing the team to experiment and find alternatives to awkward terms or structures. Changes in the language lead to updates in class diagrams and code (renaming classes, methods).
*   **Benefits:** It makes the model integral to everything developers and domain experts do, bringing diagrams and code to life through discussion. It also helps in organizing large-scale structures and Bounded Contexts.

### 1.4. Model-Driven Design (MDD)
**Model-Driven Design** is the approach that discards the traditional dichotomy between an "analysis model" (for understanding only) and a separate "design". Instead, MDD seeks a single model that serves both purposes well.

*   **Core Principle:** A portion of the software system literally reflects the domain model, making the mapping obvious. The implementation is "slavishly" tied to this single model.
*   **Why it's crucial:** If the design doesn't map to the conceptual domain model, the model loses value, and software correctness becomes suspect. MDD reduces errors and makes the design (and code) more communicative.
*   **Iterative Process:** Achieving this requires an iterative loop of modeling and design; if a model is impractical for implementation, or doesn't faithfully express key concepts, a new one must be sought.
*   **Programmers as Modelers:** This approach implies that programmers are actively involved in modeling, as changing code implicitly changes the model. Any technical person contributing to the model must spend time "touching the code".

### 1.5. Deep Models
A **deep model** is an incisive expression of the primary concerns and most relevant knowledge of domain experts, discarding superficial aspects and naïve interpretations. It's found by immersing oneself in the domain, continuously crunching knowledge, and iteratively refactoring until the model truly fits the needs. Deep models enhance versatility, simplicity, and explanatory power.

---

## 2. Building Blocks of a Model-Driven Design

Part II of DDD introduces a core set of object-oriented domain modeling practices, or "building blocks," that bridge the gap between models and running software.

### 2.1. Layered Architecture
A **Layered Architecture** partitions a complex program into distinct layers, allowing concentration on different parts of the design in isolation while maintaining intricate interactions.
*   **Standard Layers:**
    *   **User Interface (UI) Layer:** Responsible for showing information and interpreting user commands.
    *   **Application Layer:** Defines the software's jobs, coordinates tasks, and delegates work to the domain layer, but contains no business rules. It's kept thin.
    *   **Domain Layer (Model Layer):** The **heart of business software**, responsible for representing business concepts, situation, and rules. State reflecting the business situation is controlled here.
    *   **Infrastructure Layer:** Provides generic technical capabilities (e.g., message sending, persistence, drawing widgets) supporting higher layers.
*   **Crucial Separation:** The sharp distinction and isolation of the **domain layer** is crucial for enabling Model-Driven Design, allowing the model to evolve richly and clearly.

### 2.2. Entities
**Entities** are objects defined by their continuity and identity, rather than solely by their attributes. Even if an Entity's attributes change, it remains the same Entity if its identity is preserved (e.g., a `Customer` with an ID number).

### 2.3. Value Objects
**Value Objects** measure, quantify, or describe a thing in the domain, and are identified by the composition of their attributes, not an inherent identity. They are typically **immutable** after creation, meaning their observable state never changes. (e.g., a `Date`, `Money` amount, or `Address`).

Domain Model:
───────────────────────────────────────────
│               Domain Model              │
│ ┌───────────────────┐                   │
│ │      Entity       │                   │
│ │   ┌───────────┐   │     ┌───────────┐ │
│ │   │ Identity  │───┼───▶│ Entity     │ │
│ │   └───────────┘   │     └───────────┘ │
│ │   ┌───────────┐   │                   │
│ │   │ValueObject│───┤                   │
│ │   └───────────┘   │                   │
│ └───────────────────┘                   │
│                                         │
│ ┌───────────────────┐                   │
│ │   Domain Event    │                   │
│ └───────────────────┘                   │
───────────────────────────────────────────

### 2.4. Services
A **Service** is an operation offered as an interface that stands alone in the model, without encapsulating state like Entities or Value Objects.
*   **Purpose:** Services represent important domain concepts that are activities or transformations, rather than things (e.g., "funds transfer").
*   **Characteristics:**
    1.  Relates to a domain concept not natural to an Entity or Value Object.
    2.  Interface defined in terms of other domain model elements, using Ubiquitous Language operation names, domain objects as parameters/results.
    3.  Is **stateless**, meaning any client can use any instance without regard to its history.

Domain Service Integration:
─────────────────────────────────────────────────────────
│  ┌───────────────────┐                                 │
│  │  Domain Service   │                                 │
│  └─────┬─────────────┘                                 │
│        │                                               │
│        ▼                                               │
│ ┌───────────────────────────────────────────────┐      │
│ │               Aggregate Root                  │      │
│ │ ┌───────────┐     ┌───────────┐   ┌─────────┐ │      │
│ │ │ Entity    │───▶│ValueObject│──▶│ Entity  │ │      │
│ │ └───────────┘     └───────────┘   └─────────┘ │      │
│ └───────────────────────────────────────────────┘      │
─────────────────────────────────────────────────────────

### 2.5. Aggregates
An **Aggregate** is a cluster of associated objects treated as a unit for the purpose of data changes.
*   **Root:** External references are restricted to one member of the Aggregate, designated as the "**root**".
*   **Invariants:** A set of consistency rules applies within the Aggregate's boundaries. This helps maintain transactional integrity (e.g., a `Purchase Order` and its `Line Items`).

─────────────────────────────────────────────────────
│ Aggregate                                          │
│ ┌───────────────────────────────────────────────┐  │
│ │               Aggregate Root                  │  │
│ │ ┌───────────┐     ┌───────────┐   ┌─────────┐ │  │
│ │ │ Entity    │───▶ │ValueObject│──▶│ Entity  │ │  │
│ │ └───────────┘     └───────────┘   └─────────┘ │  │
│ │     ▲                                         │  │
│ └─────┼─────────────────────────────────────────┘  │
│       │                                           │
│   ┌───┴───────────┐                               │
│   │ Repository    │                               │
│   └───────────────┘                               │
─────────────────────────────────────────────────────

### 2.6. Factories
**Factories** are separate objects responsible for creating instances of complex objects and Aggregates.
*   **Purpose:** They encapsulate the knowledge needed to assemble complex objects, providing an interface that reflects client goals and an abstract view of the created object, freeing clients from knowing concrete classes.
*   **Role in Domain Layer:** While they may not have a direct conceptual meaning in the domain model itself, Factories are a necessary part of the domain design to manage complex construction.

### 2.7. Repositories
A **Repository** provides a practical means of acquiring references to pre-existing domain objects (specifically, Aggregate roots).
*   **Purpose:** It encapsulates the underlying persistence technology and query strategy, decoupling the application and domain design from these technical details.
*   **Benefits:** Simplifies client code, allows flexible queries, and communicates design decisions about object access. It lifts the burden of data storage and retrieval from the client, letting the client focus on the model.

Domain Model & Infrastructure (Hexagonal):
─────────────────────────────────────────────────────────────────────────────
│    Application Layer                                                      │
│ ┌─────────────────────┐                                                   │
│ │ Domain Application  │───────────┐                                       │
│ └─────────────────────┘           │                                       │
│ ┌─────────────────────┐           ▼                                       │
│ │    Domain Model     │ ┌───────────────────┐                             │
│ │  ┌───────────┐      │ │Repository (Port)  │                             │
│ │  │Aggregate  │◀─────┼─┼───────────────────┼───────────┐                 │
│ │  └───────────┘      │ │Adapter (Infra)    │           │                 │
│ └─────────────────────┘ └───────────────────┘           │                 │
│                                                         ▼                 │
│ ┌───────────────┐      ┌─────────────┐       ┌───────────────────┐        │
│ │ User Interface│      │ REST API    │       │  Database         │        │
│ └───────────────┘      └─────────────┘       └───────────────────┘        │
─────────────────────────────────────────────────────────────────────────────

---

## 3. Strategic Design

Strategic Design deals with situations arising in complex systems, larger organizations, and interactions with external/legacy systems. It provides principles for managing complexity beyond individual objects, often requiring decisions at the team level or between teams.

### 3.1. Bounded Context
A **Bounded Context** explicitly defines the context within which a particular model applies.
*   **Purpose:** It sets boundaries in terms of team organization, specific application parts, code bases, and database schemas. This clarifies what needs to be consistent within the boundary and avoids confusion when different models coexist.
*   **Problem it solves:** Prevents "model fragmentation" (different parts of the system using subtly conflicting interpretations of the model) and "false cognates" (different terms for the same concept, or same term for different concepts).

Bounded Contexts (Strategic View):
───────────────────────────────────────────────────────────
│ Organisation                                             │
│ ┌─────────────────┐ ┌─────────────────┐ ┌───────────────┐│
│ │Sales Context    │ │Inventory Context│ │Payment Context││
│ │ ┌─────────────┐ │ │ ┌─────────────┐ │ │ ┌───────────┐ ││
│ │ │ DomainModel │ │ │ │ DomainModel │ │ │ │DomainModel│ ││
│ │ └─────────────┘ │ │ └─────────────┘ │ │ └───────────┘ ││
│ └─────────────────┘ └─────────────────┘ └───────────────┘│
│                                                          │
│            (Context Mapping & Integration)               │
│  ┌───────────────────────────────────────────────────┐   │
│  │Shared Kernel | Partnership | Anti-Corruption Layer│   │
│  └───────────────────────────────────────────────────┘   │
───────────────────────────────────────────────────────────

### 3.2. Context Map
A **Context Map** is a global overview that identifies each model in play on a project and defines its Bounded Context, including implicit models of non-object-oriented subsystems. It describes the points of contact and explicit translation for any communication or sharing between contexts.
*   **Purpose:** Provides a clear, shared view for managers and team members of the ongoing conceptual subdivision of the software model and design. The names of the Bounded Contexts become part of the Ubiquitous Language.

### 3.3. Relationships Between Bounded Contexts
DDD offers various patterns for relating different Bounded Contexts:

*   **Continuous Integration:** Within a single Bounded Context, all code and implementation artifacts are merged frequently, with automated tests flagging fragmentation quickly. This is coupled with relentless exercise of the Ubiquitous Language for conceptual integration.
*   **Shared Kernel:** Teams agree to share a designated subset of the domain model (and its associated code/database design). This shared part has special status and requires consultation before changes. It reduces duplication and eases integration where full model synchronization is too costly.
*   **Customer/Supplier Development Teams:** Establishes a clear customer/supplier relationship where the downstream team plays the "customer" role to the upstream team. Automated acceptance tests are jointly developed and run by the upstream team as part of their continuous integration, freeing the upstream team to make changes without fear of breaking downstream.
*   **Conformist:** The downstream team slavishly adheres to the model of the upstream team, eliminating the complexity of translation. This is useful when the upstream team is not interested in collaboration, or when integrating with large, off-the-shelf components with extensive interfaces.
*   **Anticorruption Layer (ACL):** A translation layer that insulates a new system's model from the corruption of an existing, often legacy or external, system's model. It is a mechanism for translating conceptual objects and actions from one model and protocol to another. Often involves FAÇADES and ADAPTERS.
*   **Separate Ways:** When two functional parts have no significant relationship or interaction, they are declared to have no connection at all, allowing developers to find simple, specialized solutions within a small scope. This avoids unnecessary integration costs.
*   **Open Host Service (OHS):** When a subsystem needs to be integrated with many others, a public protocol is defined, offering access to its capabilities as a set of Services. This allows multiple consumers to integrate without requiring a custom translator for each.
*   **Published Language:** Used when translation between Bounded Contexts is complex, or external systems are involved. It involves using a well-documented, shared language (often an industry standard like XML) as a common medium of communication, translating into and out of this language as necessary.

### 3.4. Distillation
**Distillation** is the process of separating the components of a mixture to extract the essence, in this case, the most valuable and useful part of the domain model.
*   **Purpose:** Aids team members in grasping the overall design, facilitates communication by identifying a manageable core for the Ubiquitous Language, guides refactoring, and focuses work on high-value areas.
*   **Patterns for Distillation:**
    *   **Core Domain:** The distinctive and central part of the model that differentiates the application and makes it valuable. Top talent should be applied here, with investment in other parts justified by their support for the Core.
    *   **Generic Subdomains:** Cohesive subdomains that are not the primary motivation for the project (e.g., accounting, time zones). These can be factored into general models, placed in separate packages, and are candidates for off-the-shelf solutions or outsourcing.
    *   **Cohesive Mechanisms:** Partitioning a conceptually cohesive mechanism (like a formalism or a well-documented algorithm) into a separate lightweight framework, exposing its capabilities with an Intention-Revealing Interface. This allows the expressive domain model to focus on "what" while the mechanism handles the "how".
    *   **Domain Vision Statement:** A short (3-7 pages) description of the Core Domain and its value proposition, written early and revised to guide resource allocation and modeling choices.
    *   **Highlighted Core:** Flagging the elements of the Core Domain within the primary model repository (e.g., UML diagrams, code comments) to make it effortless for developers to identify.
    *   **Segregated Core:** Physically moving related core classes into a new, named package that reflects the core concept, refactoring code to sever non-direct expressions and making references explicit.
    *   **Abstract Core:** Expressing the most fundamental concepts and relationships of the Core Domain in a pure, rigorous form, often as code.

### 3.5. Large-Scale Structure
A **Large-Scale Structure** is a language or set of high-level concepts and rules that establishes a pattern of design for an entire system, allowing it to be discussed and understood in broad strokes.
*   **Purpose:** Guides design, aids understanding, and helps coordinate independent work across multiple teams or Bounded Contexts.
*   **Key Aspect: Evolving Order:** Structures should not be imposed upfront but should evolve iteratively as understanding deepens and system complexity increases.
*   **Examples of Structures:**
    *   **System Metaphor:** A concrete analogy that captures the imagination of team members and guides design decisions (e.g., "payroll processing is like an assembly line").
    *   **Responsibility Layers:** Identifying natural strata in the domain and casting them as broad abstract responsibilities, with objects/aggregates/modules fitting neatly into one layer. Dependencies typically flow downwards. Common layers include:
        *   **Potential Layer:** Capabilities or resources available to the business (e.g., ships, equipment).
        *   **Operations Layer:** Activities, past, current, and planned (e.g., Cargo, Route Specification, Itinerary).
        *   **Decision Support Layer:** Tools for planning, decision making, or automation based on lower layers (e.g., a Router).
        *   **Policy Layer:** Rules and goals that constrain behavior in other layers.
    *   **Knowledge Level:** A distinct set of objects that describes and constrains the structure and behavior of the basic (operational) model. It allows parts of the model to be "plastic" or configurable by users/super-users, separating concrete operational data from abstract rules or types.
    *   **Pluggable Component Framework:** For very mature models with multiple interoperating applications, defining a central hub and interfaces that components plug into. This enables independent development and smooth integration of immense systems.

---

## 4. Key Practices and Principles in DDD

Beyond the core concepts and patterns, DDD is supported by several practices and principles:

*   **Iterative Development:** DDD thrives in an iterative environment, allowing for continuous refinement of the model and design.
*   **Close Collaboration:** Continuous, high-bandwidth communication between developers and domain experts is essential for knowledge crunching and model refinement.
*   **Continuous Refactoring:** This isn't just about code cleanup; it's about continuously improving the design to reflect deeper domain insights. Automated unit tests are crucial for safe experimentation.
*   **Hands-on Modelers:** Technical people who contribute to the model must spend time "touching the code". Programmers are inherently modelers in a Model-Driven Design, and their refactoring should strengthen the model.
*   **Intention-Revealing Interfaces:** Design elements (classes, methods) are named to describe their effect and purpose, without exposing their internal implementation details. This reduces cognitive overload for client developers.
*   **Assertions:** Stating post-conditions of operations and invariants of classes/Aggregates, often codified as automated unit tests. This clarifies meaning and makes object behavior predictable.
*   **Side-Effect-Free Functions:** Operations designed to return results without causing observable side effects. This simplifies understanding by reducing the "tangle of cause and effect" in complex designs.
*   **Conceptual Contours:** Decomposing design elements into cohesive units based on the "important divisions in the domain," aligning the model with the consistent aspects of the business.
*   **Standalone Classes:** Classes that can be fully understood and tested without reference to others (except primitives and basic libraries). This reduces mental burden and improves module clarity.

---

## 5. Benefits of Adopting DDD

*   **Accelerates Complex Projects:** Provides a systematic way to deal with complicated domains, leading to faster development.
*   **Rich Functionality:** Enables the creation of software that delivers deep and compelling functionality, closely aligned with user needs and business objectives.
*   **Improved Communication:** Fosters clarity and precision in communication among team members and with domain experts through the Ubiquitous Language.
*   **Maintainable and Evolving Software:** Designs become easier to modify and extend, preventing the software from becoming a high-maintenance legacy.
*   **Knowledge Accumulation:** Promotes continuous learning within the team, building deep domain knowledge over time.
*   **Leverage and Predictability:** A well-designed model allows powerful new features to unfold naturally and ensures consistent, predictable behavior.
*   **Higher Quality Software:** Leads to more robust, reliable software because the design is a direct outgrowth of a carefully considered domain model.

---

## 6. Challenges and Considerations

While powerful, DDD is not without its challenges:

*   **Skill Requirements:** It requires strong design and modeling skills, and may not be suitable for simple projects or unsophisticated teams due to the learning curve and initial overhead.
*   **Team Dynamics:** Requires commitment and coordination within the team, as well as disciplined adherence to the chosen strategic patterns. Lack of hands-on involvement from modelers can lead to impractical designs.
*   **Framework Pitfalls:** Improper application of technical frameworks can obscure the domain model, making it difficult to maintain the connection between code and model.
*   **Complexity of Integration:** Integrating different Bounded Contexts or external systems can be complex, requiring careful design of translation layers or adoption of shared models.
*   **Cost of Refactoring:** Deep refactorings towards a deeper model or new large-scale structures can be expensive and disruptive, especially in large systems.
*   **Over-Engineering Risk:** The pursuit of "supple design" or "declarative style" can lead to over-engineering if not driven by actual domain complexity and needs.
*   **Sustaining the Culture:** Maintaining a domain-driven design culture, especially through organizational changes, requires continuous effort and commitment.

---

## 7. Conclusion & Next Steps

Domain-Driven Design provides a comprehensive framework for developing complex software systems. By prioritizing a deep understanding of the problem domain and reflecting that understanding in the software's design and code, teams can build powerful, flexible, and maintainable applications that truly serve user needs.

**To get started or deepen your team's DDD adoption:**

1.  **Assess Your Current State:** Draw a **Context Map** of your current systems and identify existing Bounded Contexts. Analyze the Ubiquitous Language used on your project.
2.  **Identify Your CORE DOMAIN:** Work with domain experts to define the most valuable and distinctive parts of your system. Consider writing a **Domain Vision Statement**.
3.  **Prioritize Knowledge Crunching:** Encourage close collaboration between developers and domain experts to continuously refine the understanding of the domain.
4.  **Practice Ubiquitous Language:** Make a conscious effort to use the agreed-upon domain terms in all discussions, documents, and code.
5.  **Embrace Iterative Refactoring:** Don't aim for perfect upfront design. Continuously refactor your code to reflect deepening insights into the domain model.
6.  **Apply Building Blocks Judiciously:** Understand and apply patterns like **Layered Architecture**, **Entities**, **Value Objects**, **Services**, **Aggregates**, **Factories**, and **Repositories** to build a clean domain layer.
7.  **Consider Strategic Patterns:** For larger systems, explore **Bounded Contexts**, **Context Maps**, and large-scale structures like **Responsibility Layers** to manage complexity.

Remember, DDD is a journey of discovery and continuous improvement. It requires discipline, learning, and a willingness to adapt your understanding and design as new insights emerge.

---

## 8. Glossary

*   **AGGREGATE:** A cluster of associated objects treated as a unit for data changes, with external references restricted to a designated "root".
*   **ANALYSIS PATTERN:** A group of concepts representing a common construction in business modeling, potentially spanning multiple domains.
*   **ANTICORRUPTION LAYER (ACL):** A translation layer that insulates a new system's model from the corruption of another system's (often legacy or external) model.
*   **ASSERTION:** A statement of what a design element must do, independently of how it does it, ideally implementable as part of the program (e.g., invariants, post-conditions).
*   **BOUNDED CONTEXT:** The explicitly defined context within which a particular model applies, ensuring consistency and clear relationships with other models.
*   **COHESIVE MECHANISM:** A conceptually cohesive mechanism, often a formalism or well-documented algorithm, factored into a separate lightweight framework.
*   **COMPOSITE:** A design pattern where objects can be composed into tree structures to represent part-whole hierarchies, allowing clients to treat individual objects and compositions uniformly.
*   **CONFORMIST:** A strategy where a downstream team slavishly adheres to the model of an upstream team to eliminate translation complexity.
*   **CONTEXT MAP:** A global overview that identifies each model's BOUNDED CONTEXT on a project and describes their points of contact and relationships.
*   **CONTINUOUS INTEGRATION:** A process of frequently merging all code and other implementation artifacts within a BOUNDED CONTEXT, using automated tests to flag fragmentation quickly, coupled with constant exercise of the UBIQUITOUS LANGUAGE.
*   **CORE DOMAIN:** The distinctive part of the model, central to the user’s goals, that differentiates the application and makes it valuable.
*   **CUSTOMER/SUPPLIER DEVELOPMENT TEAMS:** A relationship where a downstream team acts as a customer to an upstream team, negotiating requirements and validating interfaces via automated acceptance tests.
*   **DECLARATIVE DESIGN:** A programming form where a precise description of properties controls the software, acting as an executable specification.
*   **DEEP MODEL:** An incisive expression of the primary concerns of the domain experts and their most relevant knowledge, avoiding superficial or naïve interpretations.
*   **DISTILLATION:** A process of separating model components to extract the essence (CORE DOMAIN) in a more valuable and useful form.
*   **DOMAIN:** A sphere of knowledge, influence, or activity relevant to the application.
*   **DOMAIN EXPERT:** A project member with deep knowledge of the application's domain, not necessarily a software developer.
*   **DOMAIN LAYER:** The manifestation of the domain model, containing the design and implementation of business logic, isolated from other concerns.
*   **DOMAIN VISION STATEMENT:** A short description of the CORE DOMAIN and its value proposition, ignoring non-distinguishing aspects.
*   **ENTITIES:** Objects defined by their identity, continuity, and lifecycle, rather than just their attributes.
*   **EVOLVING ORDER:** The principle that large-scale structure should emerge and adapt through an iterative development process, rather than being fixed upfront.
*   **FACTORIES:** Objects responsible for creating complex objects or Aggregates, encapsulating their intricate assembly logic.
*   **GENERIC SUBDOMAINS:** Cohesive subdomains that are not the project's primary motivation, often common business concepts that can be generalized or sourced externally.
*   **HIGHLIGHTED CORE:** The practice of flagging elements of the CORE DOMAIN within the primary model repository to make them easily identifiable.
*   **IMMUTABLE:** A characteristic of an object whose observable state never changes after creation.
*   **IMPLICIT CONCEPT:** A concept necessary for understanding a model or design but never explicitly mentioned.
*   **INTENTION-REVEALING INTERFACE:** A design where element names (classes, methods) clearly convey their purpose and effect, allowing clients to use them without needing to understand their internal implementation.
*   **INVARIANT:** An assertion that must always be true, except during transient states (e.g., during method execution or uncommitted transactions).
*   **KNOWLEDGE LEVEL:** A distinct set of objects that describe and constrain the structure and behavior of the basic (operational) model, enabling customization by users or super-users.
*   **LARGE-SCALE STRUCTURE:** A set of high-level concepts or rules that establish a design pattern for an entire system, providing a language for broad understanding.
*   **LAYERED ARCHITECTURE:** A system partition that isolates concerns, notably a distinct Domain Layer, from UI, Application, and Infrastructure.
*   **MODEL-DRIVEN DESIGN (MDD):** The approach where a single model directly drives both analysis and design, with the implementation closely reflecting the model.
*   **MODULES:** Organizing units (like packages or namespaces) that reflect conceptual cohesion in the domain model and are part of the Ubiquitous Language.
*   **OPEN HOST SERVICE (OHS):** A pattern where a subsystem defines and publishes a protocol (set of SERVICES) for integration by many other subsystems, allowing a flexible, one-to-many integration.
*   **PLUGGABLE COMPONENT FRAMEWORK:** A large-scale structure where applications interoperate based on shared abstractions defined by a central hub and component interfaces, often seen in mature domains.
*   **PUBLISHED LANGUAGE:** A well-documented, shared language (often an industry standard) used as a common medium for information exchange between different Bounded Contexts.
*   **REPOSITORIES:** Mechanisms that provide a practical means of retrieving and managing the lifecycle of persistent domain objects (specifically Aggregate roots), encapsulating persistence technology.
*   **RESPONSIBILITY LAYERS:** A large-scale structure that stratifies the domain into broad abstract responsibilities, with conceptual dependencies flowing downwards (e.g., Potential, Operations, Decision Support, Policy).
*   **SEPARATE WAYS:** A strategy where two Bounded Contexts are declared to have no connection, allowing specialized solutions with minimal or no data transfer, when integration benefit is low.
*   **SEGREGATED CORE:** A refactoring approach that physically moves core domain classes into a separate package to improve visibility and focus.
*   **SERVICES:** Operations in the domain model that represent an activity or transformation and do not encapsulate state.
*   **SHARED KERNEL:** A designated subset of the domain model (and its code/DB design) that two teams agree to share, requiring explicit coordination for changes.
*   **SIDE-EFFECT-FREE FUNCTION:** An operation that returns a result without causing any observable state changes (side effects).
*   **SPECIFICATION:** A predicate-like Value Object that states a constraint on another object's state, used for validation, selection (querying), or building-to-order.
*   **STRATEGY (AKA POLICY):** A design pattern where the varying part of a process or a rule is factored into a separate "strategy" object, allowing different algorithms or policies to be substituted.
*   **SUPPLE DESIGN:** A design that is a pleasure to work with, easy to change and combine, leveraging a deep model to allow natural, clear, and flexible expressions.
*   **SYSTEM METAPHOR:** A concrete analogy that captures the central theme of a whole design and provides a shared understanding among team members.
*   **UBIQUITOUS LANGUAGE:** A language structured around the domain model, relentlessly used by all team members in all communication (speech, writing, code) to connect all activities with the software.
*   **VALUE OBJECTS:** Objects that measure, quantify, or describe a thing, identified by their attributes, and are typically immutable.

---

## 9. References

*   **Evans03.pdf:** Evans, Eric. "Domain-Driven Design: Tackling Complexity in the Heart of Software." (Final Manuscript, April 15, 2003).

---