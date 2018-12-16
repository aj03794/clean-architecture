#### Paradigm Overview
- Structured, Object Oriented, Functional
- These align with the 3 big concerns of architecture: function, separation of concerns, and data managemenet

#### Structured Programming
`Testing shows the presence, not absence, of bugs`

Proofs of incorrectness can be appied only to `provable` programs.
- A program that is not provable - cannot be deemed correct nomatter how many tests are applied to it

- Ability to create falsifiable units of programming make structured programming valuable today.
- This is why we consider `functional decomposition` to be one of our best practices

Software architects strive to define modules, components, and services that are easily falsifiable (testable).

#### Object-Oriented Programming
- Encapsulation, Inheritance, and Polymorphism were implementable in C.

- Before polymorphism, a typical calling tree contained: main functions called high-level functions, which called mid-level functions, which called low-level functions

![alt text](Images/image1.png)

For main to call one of high-level functions it had to mention the name of the module that contained that function
- In C, this was a `#include`
- In Java, it was an `import` statement
- In C#, it was a `using` statement

- Presents few options
- Flow control was dictated by the behavior of the system
- Source code dependencies were dictated by that flow of control

![alt text](Images/image2.png)
- Module HL1 calls the F() function in module ML1. The fact that it calls this function through an interface is a source code contrivance
- At runtime, the interface doens't exist. HL1 simply calls F() within ML1

- Note, that the source code dependency (the inheritance relationship) between ML1 and the interface I points in the opposite direction compared to the control flow.  This is called dependency inversion
- Implications for the software architect are profound

- The fact that OO languages provide safe and convenient polymorphism means that `any source code dependency, no matter where it is, can be inverted`

- Looking back at `image1` and its many source code dependencies, any of those dependencies can be turned around by inserting an interface between them

- With this approach, software architects working in systems written in OO languages have `absolute` control over the direction of all source code dependencies in the system. They do not have to align those dependencies with the flow of control
- No matter which module does the calling and which module is called, the software architect can point the source code dependency in either direction.

- As an example, you can rearrange the source code dependenices of your system so that the database and UI depend on the business rules, rather than the other way around.
![alt text](Images/image2.png)
- This means that the UI and the database can be plugins to the business rules.  The source code of the business rules never mentions the UI or the database.

- As a result, the business rules, the UI, and the database can be compiled into three separate components or deployment units (jar files, DLL, gem files, etc) that have the same dependencies as the source code.  The component containing the business rules will not depened on the components containing the UI and database.
- The business rules can be `deployed independently` of the UI and the database. Changes to the UI or the database need not have any effect on the business rules. These components can be deployed separately and independently.
- When the source code of a component changes, only that component needs to be redeplyoed.  This is `independent deployability`.
- If the modules can be deployed independently, then they can be developed independently by different teams. That's `independent developability`

- OO is the ability, through polymorphism, to gain absolute control over every source code dependency in the system.  Allows architects to create a plugin architecture, in in which modules that contain high-level policies are independent of modules that contain low-level details.
- Low-level details are relegated to plugin modules that can be deployed and developed independently from the modules that contain high-level policies

#### Functional Programming
- Variable in funcftional `languages do not vary`

##### Immutability and Architecture
- All race conditions, deadlock conditions, and concurrent update problems are due to mutable variables.
- You cannot have a race condition or a concurrent update problem if no variable is ever updated

- All problems that we face in concurrent applications - all probelms we face in applications that require multiple threads, and multiple processors - cannot happen if there are no mutable variables

##### Segregation of Mutability
- Create mutable and immutable components, separate the two
- Immutable components perform their tasks in a purely functional way without using any mutable variables
- Immutable components communicate with one or more other components that are not purely functional

![alt text](Images/image4.png)

Since mutating state exposes those components to all problems of concurrency, it is common practice to use some kind of `transactional memory` to protect mutable variables from concurrent updates and race conditions
- Transactional memory treats variables in memory the same way a database treats records on disk.
- It protects those variables with a transaction or retry-based scheme

- Well structured applications will be segregated into those components that do not mutate variables and those that do
- This is supported by use of appropriate disciplines to protect those mutated variables

- Use as much immutability as possible
- Avoid mutation

##### Conclusion
- Structured programming is discipline imposed upon direct transfer of control
- Object oriented programming is disciplien imposed upon indirect transfer of control
- Functional programming is discipline imposed upon variable assignment

Each of these rstricts some aspect of the way we write code.
- We have learned `what not to do`


### Design Principles
- Good software systems begin with clean code

SOLID Principles
- Tell us how to arrange functions and data structures into classes and hwo those classes should be interconnected.
- Use of the word `class` does not imply that these principles are applicable only to OO software.
- A class is simply a coupled grouping of functions and data
- Every software system has such groupings, whether they are called classes or not

Goal of the principles is the creation of mid-level software structures that:
- Tolerate change
- Are easy to understand
- Are the basis of components that can be used in many software systems

Term `mid-level` refers to the fact that these principles are applied at the module level. Applied just above the level of the code and help to define the kinds of software structures used within modules and components

S: SRP - The Single Responsibility Principle
- Each software module has one, and only one, reason to change

O: OCP - The Open-Closed Principle
- For software systems to be easy to change, they must be designed to allow the behavior of those systems to be changed by adding new code, rather than changing existing code

L: LSP - Liskov Subsitution Principle
- Build software systems from interchangeable parts, those parts must adhere to a contract that allows those parts to be subsituted one for another

I - ISP - Interface Segregation Principle
- Avoid depending on things that they don't use

D - DIP - Dependency Inversion Principle
- The code that implements high-level policy should not depend on the code that implements low-level details
- Details should depend on policies

### SRP: The Single Responsibility Principle
- May be least well understood
- Does `NOT` mean that every module should just do one thing. There is a principle like that, it is called a function.
- Historically described as `A module should have one, and only one, reason to change`
- Software systems are changed to satisfy users and stakeholders; those users and stakeholders are the `reason to change`

- Principle can be rephrased as `A module should be responsible to one, and only one, user or stakeholder`

- Words `user` and `stakeholder` aren't really the right words
- There will likely be more than one user or stakeholder who wants the system changed in the same way
- We're really referring to a group - one or more people who require that change
- Group referred to as an `actor`

- Final version of SRP is : `A module should be responsbile to one, and only one, actor`

- What is meant by `module`?
- Simpliest definiton is just a source file
- Usually that definiton works fine
- Some languages and development environments don't use source files to contain their code. In these cases a module is just a cohesive set of functions and data structures

- Word `cohesive` implies the SRP.
- Cohesion is the force that binds together the code responsible to a single actor

###### Symptom 1: Accidental Duplication

Example: 
- Example is the `Employee` class from a payroll application
- It has 3 methods: calculatePay(), reportHours(), and save()
![alt text](Images/image5.png)

This class violates the SRP because those 3 methods are responsible to 3 very different actors
- The `calculatePay()` method is specified by the accounting department, which reports to the CFO
- The `reportHours()` method is specified and used by the human resources department which reports to the COO
- The `save()` method is specified by the DBAs who report to the CTO

- By putting the source code for these 3 methods into a single `Employee` class, the developers have coupled each of these actors to the others
- This coupling can cause acitons of the CFO's team to affect some that the COO's team depends on

For example:
- Suppose `calculatePay()` function and the `reportHours()` function share a common algorithm for calculating non-overtime hours
- Suppose developers, who are careful not to duplicate code, put that algorithm into a function named `regularHours()`
![alt text](Images/image6.png)
- Suppose CFO's team decides that the way non-overtime hours are calculated need to be tweaked. In contrast, the COO's team in HR does not want that particular tweak
- A developer is asked to make change, and sees the convenient `regularHours()` function called by the `calculatePay()` method
- Developer does not notice that the function is also called by `reportHours()` function
- Developer makes required change and carefully tests it. CFO's team validates that the new function works as desired, and the system is deployed
- COO's team doesn't know that is happening.
- HR personnel continue to use reports generated by the `reportHours()` function, but now they contain incorrect numbers. Eventually the problem is discovered and COO is livid b/c bad data has cost his budget millions of dollars

Thes types of problems occur because we put code that different actors depend on into close proximity.  SRP says `separate the code that different actors depend on`


###### Symptom 2: Merges

- Merges are common in source files that contain may different methods
- Situation is especially likely if those methods are responsible to different actors

Example:
- CTO's team of DBAs decide that there should be a single schema change to the `Employee` table of the database.
- Suppose also that COO's team of HR clerks decide that they need a change in the format of the hours report

- Two different developers (possibly from different teams) check out the `Employee` class and begin to make changes. Changes collide. The result is a merge

- Merge puts both the CTO and the COO at risk. It's not inconceivable that the CFO could be affected as well

- Many other symptoms that could be investigated, but they all involve multiple people changing the same source file for different reasons

- The key to avoid this problem is to `separate code that supports different actors`

###### Solutions

- Different solutions, each moves functions into different classes
- Most obvious way is to separate the data from the functions
- The 3 classes share access to `EmployeeData` which is a simple data structure with no methods
- Each class holds only the source code necessary for its particular function
- 3 classes are not allowed to know about each other.
- Accidental duplication is avoided this way
![alt text](Images/image7.png)

- Downside of this solution is that developers now have 3 classes that they have to instantiate and track. Common solution to this dilemma is to use the `Facade` pattern.

![alt text](Images/image8.png)

- `EmployeeFacade` contains very little code.
- Reponsible for instantiating and delegating to the classes with the functions.

- Some developers prefer to keep the most important business rules closer to the data.
- This can be done by keeping the most important method in the original `Employee` class and then using that class as a `Facade` for the lesser functions

![alt text](Images/image9.png)

- May object to these solutions on the basis that every class would contain just one function.
- That is not the case
- Number of functions required to calculate pay, generate a report, or save the data is likely o be large in each case
- Each of those classes would have many `private` methods in them

- Each of these classes that contain such a family of methods is a `scope`.
- Outside of that `scope`, no one knows that the private members of the family exist

###### Conclusion
- The SRP is about functions and classes
- It reappears in a different form at two more levels
- At `level of components`, it becomes the `Common Closure Principle`
- At `architectural level`, it becomes the `Axis of Change` responsible for the creation of `Architectural Boundaries`


### OCP: The Open-Closed Principle

`A software architect should be open for extension but closed for modification`

- Behavior of a software artifact ought to be extendible without having to modify original artifact

- This is the most fundamental reason that we study software architecture
- If simple extensions to the requirements force massive changes to the software, then the architects of that software system have engaged in a spectacular failure

- Guides design of class and modules
- Takes on an even greater significance when we consider the level of architectural components

###### A Thought Experiment

- Image system that displays a financial summary on a webpage
- Data on the page is scrollabe, and negative numbers are rendered in red

- Stakeholders ask that this information be turned into reprot to be printed on a black and white printer
- Appropriately paginated, page headers, page footers, and column labels
- Negative numbers should be surrounded by paraentheses

- Some new code must be written. But how much old code will have to change

- Good software architecture would reduce amount of changed code to barest minimum, ideally zero

- How to do this?
- By properly separating the thigs that change for different reasons (SRP)
- Then organizing the dependencies between those things properly (the dependency inversion principle)

- By applying SRP, we might come up with the data flow in the picture below.
- Some analysis procedure inspects the financial data and produces reportable data, which is then formatted appropriately by the two reporter processes

![alt text](Images/image10.png)

- Essential insight here is that generating the report involves two separate responsibilites
- Calculation of reported data, and presentation of that data in to web and printer-friendly form

- Having made this separtion, we need to organize source code dependencies to ensure that changes to one of those responsibilites do not cause changes in the other.
- New organziation should ensure that the behavior can be extended without undo modification

- We can accomplish by paritioning the processes into classes, and separating those classes into components (as shown by the double lines in the diagram below)
- The component at the upper left is the `Controller`.
- Upper right is the `Interactor`
- Lower right is the `Database`
- At the lower left, there are four components that represent the `Presenters` and the `Views`

![alt text](Images/image11.png)

- Classes marked with `<I>` are interacts
- Those marked with `<DS>` are structures
- Open arrowheads are `using` relationships
- Cloed arrowheads are `implements` or `inheritance` relationships

- First thing to notice is that all the dependencies are `source code` dependencies
- An arrow pointing from class A to class B means that the source code of class A mentions the name of B, but class B mentiosn nothing about class A
- Thus `FinancialDataMapper` knows about `FinancialDataGateway` through an `implements` relatipnship
- But `FinancialGateway` knows nothing at all about `FinancialDataMapper`

- The next thing to notice is that each double line is crossed `in one direction only`.
- This means that all component relationships are unidirectional as show in figure below.
- These arrows point toward the components that we want to protect from change

![alt text](Images/image12.png)

- `If component A should be protected from changes in component B, then component B should depend on component A`

- We want to protect the `Controller` from the changes in the `Presenters`
- We want to protect the `Presenters` from the changes in `Views`
- We want to protect the `Interactor` from changes in `anything`

- The `Interactor` is in the position that best conforms to the `OCP`
- Changes to the `Database`, or the `Controller`, or the `Presenters`, or the `Views`, will have no impact on the `Interactor`

- Why should the `Interactor` hold this position?
- Because it contains the `business rules`
- `Interactor` contains the highest-level policies of the application
- All the other components are dealing with peripheral concerns
- The `Interactor` deals with the central concern

- Even though the `Controller` is periperhal to `Interactor`, it is central to `Presenters` and `Views`
- While `Presenters` may be peripheral to `Controller` they are central to the `Views`

- This creates a hiearchy of protection based on the notion of `level`
- `Interactors` are the highest-level concept, so they are the most protected
- `Views` are amoung lowest-level concepts, so they are the least protected
- `Presenters` are higher level than the `Views`, but are lower level than the `Controller` or the `Interactor`

- This is how OCP works at the architectural level.
- Architects seaprate functionality based on how, when, and when it changes, and then organize that separated functionality into a hierarchy of components.
- Higher-level components in hierarchy are portected from chnages made to lower-level components

###### Directional Control
- Much of complexity in previous class design showing earlier (image11) was to make sure dependencies between components pointed in the correct direction

- The `FinancialDataGateway` interface between `FinancialReportGenerator` and `FinancialDataMapper` exists to invert the dependency that would otherwise have pointed from the `Interactor` component to the `Database` component
- Same is true for `FinancialReporterPresenter` and the two `View` interfaces

###### Information Hiding
- `FinancialReportRequester` interface serves a different purpose
- It is there to protect `FinancialReportController` from knowing too much about the internals of the `Interactor`
- If interface not there, the `Controller` would have transitive dependencies (`Controller` would depend on `FinancialEntities`) on the `FinancialEntities`

- Transitive dependencies are a violation of the general principle that software entities should not depend on things they don't directly use

- Even though our first priority is to protect `Interactor` from changes to the `Controller`, we also want to protect the `Controller` from changes to the `Interactor` by hiding the internals of the `Interactor

###### Conclusion
- The OCP is one of the driving forces behind the architecture of the systems
- Goal is to make system easy to extend without incurring a high impact of change
- Goal is accomplished by partitioning system into components
- Arrange those components into a dependency hierarchy that protects higher-level components from changes in lower-level components


### LSP: The Liskov Subsitution Principle

```
“What is wanted here is something like the following substitution property: If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T”
``` 
Excerpt From: Robert C. Martin. “Clean Architecture: A Craftsman's Guide to Software Structure and Design (Robert C. Martin Series).” iBooks. 

###### Guiding the Use of Interitance

- Imagine we have a class named `License`, as show in Figure 9.1.
- This class has a method named `calcFee()` which is called by the `Billing` Application
- There are two `subtypes` of `License`: `PersonalLicense` and `BusinessLicense`
- They use different algorithms to calc license fee

![alt text](Images/Figure-9.1.png)

- This design conforms to `LSP` because the behavior of the `Billing` application does not depend, in any way, on which of the two subtypes is used.
- Both subtypes are subsitutable for the `License` type

###### The Square/Rectangle Problem

- Canonical example of violation of LSP is famed `square/rectangle` problem

![alt text](Images/Figure-9.2.png)

- In this example, `Square` is not a proper subtype of `Rectangle` because the height and width of the `Rectangle` are independently mutable
- In contract, height and width of the `Square` must change together
- Since `User` believes it is communicating with a `Rectangle`, it could easily confused

```
Rectangle r = …

r.setW(5);

r.setH(2);

assert(r.area() == 10);
```
Excerpt From: Robert C. Martin. “Clean Architecture: A Craftsman's Guide to Software Structure and Design (Robert C. Martin Series).” iBooks.

- If the code produced a `square`, then the assertion would fail

- Way to defend against this kind of `LSP` violation is to add mechanisms to the `User` (like an `if` statement) that detects whether `Rectangle` is actually a `square`
- Since the behavior of the `User` depends on the type it uses, those types are not subsitutable

###### LSP and Architecture

- Interfaces in question can be of many forms
- Java interface, Ruby classes, or set of services that all respond to the same REST interface

- In these situations (and more) the LSP is applicable because there are users who depend on well-defined interaces, and on the subsitutability of the implementations of those interfaces

###### Example LSP Violation

- Building an aggregator for many taxi dispatch services
- Customers use website to find most appropriate taxi to use (regardless of taxi company)
- Once customer makes decision, system dispatches chosen taxi by using a RESTful service

- URI for restful dispatch service is part of the information contained in the driver database
- Once system has chosen a driver appropriate for the customer, it gets that URI from the driver record and then uses it to dispatch the driver

- Suppose `Driver Bob` has dispatch URI that looks like:

```
purplecab.com/driver/Bob
```

- System will append the dispatch information onto this URI and send it with a PUT

```
purplecab.com/driver/Bob
    /pickupAddress/24 Map St.
    /pickupTime/153
    /distination/ORD
```

- This means that all dispatch services, for all different companies, must confrom to the same REST interface
- Must treat `streetAddress`, `pickupTime`, and `destination` fields identically

- Suppose Acme taxi company hired some programmers who didn't read spec very carefully
- They abbreviated the destination field to just `dest`
- What happens to architecture of the system?

- Need to add a special case
- Dispatch request for Acme driver would have to be constructed using a different set of rules from all other drives

- Simplest way to accomplish this goal would be to add `if` statement to the module that constructed the dispatch command:
```
if (driver.getDispatchUri().startsWith("acme.com"))...
```

- No architect should allow such a construction to exist in the system
- Putting the word `acme` into the code creates an opportunity for all kinds of horrible and mysterious errors (not to mention security breaches)

- What if Acme bought Purple Taxi company
- What if merged companies maintained the separated brands and the separate websites, but unified all of the original companies' system?
- Would have have to add another if statement for `purple`?

- Architect would have to insulate the system from bugs like this by creating some kind of dispatch command creation module that was driven by a configuration database keyed by the dispatch URI
- May look something like:

![alt text](Images/image13.png)

- Architect has had to add a significant and complex mechanism to deal with the fact that the interfaces of the restful services are not all subsitutable

###### Conclusion
- LSP can and should be extended to every level of architecture
- Simple violation of subsitutability can cause a system's archtitecture to be polluted with a signficant amount of extra mechanisms

# READ ISP SECTION

### DIP - The Dependency Inversion Principle

- Most flexible systems are those in which source code dependencies refer only to abstractions, not to concretions

- In a language like Java, this means that the `use`, `import`, and `include`, statements should only refer to source modules containing `interfaces`, `abstract classes`, or some other kind of abstract declaration
- `Nothing concrete should be depended on`

- Same rules apply for dynamically typed languages like `Ruby` or `Python`.
- Source code dependencies should not refer to concrete modules
- In these languages `it is any module in which the functions being called are implemented`

- `Violatile` concrete elements of our system we want to avoid depending on
- Those are the modules that we are actively developing, and that are undergoing frequent change

###### Stable Abstractions

- Every change to an abstract interface corresponds to a change to its concrete implementations
- Changes to concrete implementations do not always (or even usually) require changes to interfaces that they implement
- Interfaces are less volatile than implementations

- Designers and architects work hard ot reduce volatility of interfaces
- Try to find ways to add functionality to implementations without making changes to interfaces

- Implication is that stable software architectures are those that avoid depending on volatile concretions and that favor the use of stable abstract interfaces.
- Boils down to set of very specific coding practices:

`Don't refer to volatile concrete classes`
- Refer to abstract interfaces instead
- Rule applies in all languages, whether statically or dynamically typed
- Puts severe constrains on creation of object and generally enforces use of `Abstract Factories`

`Don't derive from volatile concrete classes`
- Corollary (follows from one already proven) to previous rule
- In statically typed languages, inheritance is the strongest, most rigid, of all source code relationships; it should be used with great care
- In dynamically typed languages, inheritance is less of a problem, but it is still a dependency - caution is always wisest choice

`Don't override concrete functions`
- Concrete functions often require source code dependencies.
- Wehn you override those functions, you do not eliminiate those dependecies - you `inherit` them
- To manage those dependencies, you should make the function abstract and create multiple implementations

`Never mention the name of anythign concrete and volatile`
- This is just a restatement of the principle itself

###### Factories
- Comply with these rules, creation of volatile concrete objects requires special handling
- In virtually all languages, the creation of an object requires a source code dependency on the concrete definition of that object

- In most object-oriented languages such as `Java`, we use an `Abstract Factory` to manage this undesirable dependency

- Diagram in Figure 11.1 shows strucutre
- The `Application` uses the `ConcreteImpl` through the `Service` interface
- However, the `Application` must somehow create instances of the `ConcreteImpl`
- To do this w/o creating a source code dependency on the `ConcreteImpl`, the `Application` calls the `makeSvc` method of the `ServiceFactoryImpl` class which derives from the `ServiceFactory`
- That implementation instantiates the `ConcreteImpl` and returns it as a `Service`

![alt text](Images/Figure-11.1.png)

- The curved line is the architectural boundary
- Separates the abstract from the concrete 
- All source code dependencies cross that curved line pointing in the same direction, towards the abstract side

- The curved line divides the system into two components: one abstract and the other concrete
- The abstract component contains all of the `high-level business rules` of the application
- The concrete implementation contains all the `implementation details` that those `business rules` manipulate

- Flow of control crosses the curved line in opposite direction of the source code dependencies
- `Source code dependencies are inverted against the flow of control` - which is why we refer to this principle as `Dependency Inversion`

###### Concrete Components

- Concrete component in Figure 11.1 contains a single dependency, so it violates DIP, so it violates the DIP.
- This is typical
- DIP violations cannot be entirely removed
- But they can be gathered into a small number of concrete components and kept separate form the rest of the system

- Most systems contain at least one such concrete component
- Often called `main` because it contains the `main` function (this is the function that is invoked by the operation system when the application is first started up)
- The `main` function would instantiate the `ServiceFactoryImpl` and place that instance in a global variable of type `ServiceFactory`
- The `Application` would then access the factory through the global variable

###### Conclusion
- DIP shows up again and again 
- Most visible organizing principle in our architecture diagrams.
- The curved line in Figure 11.1 will become architectural boundaries in later chapters
- The way dependencies cross that curved line in one direection, toward abstract entities, will become a new rule that we will call the `Dependency Rule`

### Component Principles

- If SOLID principles tell us how to arrange bricks into walls and rooms, then component principles tell us how to arrange the rooms in buildings

#### Components

- Components are the units of deployment
- Smallest entities that can be deployed as part of a system
- In Java, they are jar files
- In Ruby, they are gem files
- In .Net, they are DLLs
- In complied languages, they are aggregations of binary files
- In interpreted languages, they are aggregations of source files

- Components can be linked together into a single executable
- Or they can be aggregated into a single archive, such as a `.war` file
- Or they can be independently deployed as separate dynamically loaded plugins, such as a `.jar`, `.dll`, or `.exe`

#### Component Cohesion

- Which classes belong in which components?
- Important decision

Three Important Principles
- REP: The Reuse/RElease Equivalence Principle
- CCP: The Common Closure Principle
- CRP: The Common Reuse Principle

###### The Reuse/Release Equivalence Principle

`Granule of reuse is the granule of release`

- Living in the age of software reuse - a fulfillment of one of the oldest promises of the object-oriented model

- The `REP` is a principle that seems obvious, at least in hindsight
- People who want to reuse software components cannot, and will not, do so unless those components are tracked through a release process and are given release numbers

- Without release numbers, there would be no way to ensure that all reused components are compatible with each other
- Also reflects that software devs needs to know when new releases are coming and what changes those new releases bring

- This principle means classes and modules that are formed into a component must belong to a cohesive group
- Components cannot consist of a random hodgepodge of classes and modules
- Must be some overarching theme or purpose that those modules are shared

- Classes and modules that are grouped together into a component should be `releasable` together
- Them sharing the same version number and same release tracking and are include under the same release documentation, should make sense to author and to users

- This is `weak advice`
- Because it is hard to precisely explain the glue that holds the classes and modules together into a single component
- Although advice is weak, violations are easy to detect - they don't "make sense"


###### The Common Closure Principle

`Gather into components those classes that change for the same reason and at the same times. Separate into different components those classes that change at different times and for different reasons`

- This is just the `Single Responsibility Principl` restated for components
- A component should not have multiple reasons to change

- Maintainability is usually more important than reusability
- If code must change, would rather all changes occur in one component rather than being distributed across many components
- If changes confined to a single component, then only need to redeploy one component

- CCP prompts us to gather together in one place all the classes that are likely to change for the same reasons.
- If two classes are so tightly bound, either physically or conceptually, that they always change together, then they belong in the same component
- Minimizes workload related to releasing, revalidating, and reploying the software

- Closely associated with Open Closed Principle
- OCP states classes should be closed for modification but open for extension
- B/c closure is not 100% attainable, closure must be strategic
- Design classes such that they are closed to most common kinds of changes that we expect or have experienced

- CCP amplifies this lesson by gathering together into the same component those classes that are closed to the same types of changes
- When a requirement comes along, that change has a good chance of being restricted to a minimal number of components

######## Similarity with SRP

- CCP is the component form of SRP
- SRP tells us to separate methods into different classes, they if they change for different reasons.
- CCP tells us to separate classes into different components, if they change for different reasons
- Both can be summarized by the following:

```
Gather together those things that change at the same times and for the same reasons. Separate those things that change at different times for different reasons
```

###### The Common Reuse Principle

```
Don't force users of a component to depend on things they don't need
```

- Another principle that helps us decide which classes and modules should be placed into a component
- States that classes and modules that tend to be reused together belong in the same component

- Classes seldom reused in isolation
- Typically, reusable classes collaborate with other classes that are part of the reusable abstraction
- These classes belong together in the same component
- In such a component, we expect to see classes that have lots of dependencies on each other

- Simple example might be a container class and its associated iterators.
- These classes are resued together because they are tightly coupled to each other
- Thus they ought to be in same component

- Tells us more than which classes to put together into a component
- Tells us which classes not to keep together in a component
- When one component uses another, a dependency is created between component
- Perhaps the `using component` uses only one class within the `used component`, but that doesn't weaken the dependency
- The `using component` still depends on the `used component`

- B/c of that dependency, every time the `used` component is changed, the `using` component will likely need corresponding changes
- Even if no changes necessary to the usng component, it will likely still need to be recompiled, revalidated, and redeployed
- This is true even if the `using` component doesn't care about the change made in the used component

- When we depend on a component, we want to make sure we depend on every class in that component
- We want to make sure that the classes that we put into a component are inseparable, that it is impossible to depend on some and not on others
- Otherwise, we will be redeploying more components than is necessary, and wasting significant effort

######## Relation to ISP

- CRP is the generic version of ISP
- ISP advises us not to depend on classes that have method we don't use
- CRP advises us not to depend on components that have classes we don't use

`Don't depend on things you don't need`

###### Tension Diagram for Component Cohesion

- The 3 cohesion principles tend to fight each other
- The REP and CCP are inclusive principles
- Both tend to make components larger
- CRP is an exlusive principle, driving components to be smaller
- It is the tension that good architects seek to resolve

- Figure 13.1 is a tension diagram that shows how 3 principles of cohesion interact with each other
- Edges of diagram describe cost of abandoning the principle on the opposite vertex

![alt text](Images/Figure-13.1.png)

- Architect who focuses on just REP and CRP will find that too many components are impaced when simple changes are made
- If architect focuses too strongly on CCP and REP will cause too many unneeded releases to be generated

- Good architect finds a position in the tension triange that meets the current concerns of the development team, but is also aware that those concerns will change over time
- For example, early in development, the CCP is much more important than the REP, because develop-ability is more important than reuse

- Projects tend to start on right hand side of the triange, where only sacrifice is reuse
- As project matures, and other projects begin to draw from it, the project will slide over to left
- Component structure of a project can vary with time and maturity
- It has more to do with the way that project is developed and used, than with what the project actually does

###### Conclusion

- Once thought cohesion was simply the attribute that a module performs one, and only one, function
- 3 principles of component cohesion describe a complex variety of cohesion
- Must consider opposing forces involved with reusability and develop-ability
- Balancing forces is nontrivial
- Balance is usually dynamic (changes with time)
- Composition of components will likely evolve with time as focus on project changes from develop-ability to reusability


#### Component Coupling

###### The Acyclic Dependencies Principle

`Allow no cycles in the component dependency graph`

- `Morning after syndrome` occurs in development where many devs are modifying the same source files
- Two solutions to this problem: `the weekly build` and the second is the Acyclic Dependencies Principle (ADP)

###### The Weekly Build

- Used to be common in medium-sized projects
- Devs ignore each other for first 4 days of weeks
- Work on private copies of code, don't worry about integrating their work on a collective bassis
- On Friday, they integrate all their changes and build the system
- Integration burden quickly grows and creeps towards beginning of week

- As duty cycle of development versus integration decreases, efficiency of team decreases too
- Eventually biweekly build is declared
- Works for a time, but integration time continues to grow with project size

- Leads to a crisis
- To maintain efficiency, the build schedule has to be continually lengthened
- BUT Lengthening build schedule increases project risks
- Integration and testing becomes increasingly harder to do, and the team loses the benefit of rapid feedback

###### Eliminating Dependency Cycles

- Solution to problem is to partition development environment into releasable components
- Components become smaller units of work that can be the responsibility of a single dev or team of devs
- When developers get a component working, they release it for use by the other devs
- Give it a release number and move it into a directory for other teams to use
- Continue to modify their component in their own private areas
- Everyon else uses released version

- As new releases of a component are made available, other teams can decide whether they will immediately adopt the new release

- No team is at the mercy of the others
- Changes to one component do not need to have an immediate affect on other teams
- Each team can decide when to adapt its own components to new release of the components
- No single point when all devs must come together and integrate everything they are doing

- Simple and rational process and widely used
- To work successfully, you must manage the dependency structure of the components
- There can be no cycles

- Consider Figure 14.1
- Shows typical structure of components assembled into an application
- Structure is a `directed graph`.
- Components are `nodes`
- Dependency relationships are `directed edges`

![alt text](Images/Figure-14.1.png)

- Regardless of which component you begin it, it is impossible to follow the dependency relationships and wind up back at the component
- This structure has no cycles
- It is a `directed acyclic graph` (DAG)

- Consider what happens when the team responsible for the `Presenters` makes a new release of their component
- It is easy to find out who is affected by this release; you just follow the dependency arrow backwards
- `View` and `Main` will both be affected
- Devs currently working on those components will have to decide when they should integrate their work with the new release of `Presenters`

- When `Main` is released, it has utterly on effect on any of the other components in the system
- They don't know about `Main`, and they don't care when it changes
- Impact of releasing `Main` is relatively small

- When devs working on `Presenters` component would like to a run a test of that component, they just need to build their verison of `Presenters` with the versions of `Interactors` and `Entities` that they are currently using
- None of the other components in the system need to be involved
- Devs working on `Presenters` have relatively little work to do to set up a test, and relatively few variables to consider

- When it is time to release whole system, the process proceeds from the bottom up
- `Entities` component is compiled, tested, and released
- Then same is done for `Database` and `Interactors`
- Those components are followed by `Presenters`, `Views`, `Controllers`, and then `Authorizer`
- `Main` goes last
- Know how to build system because we understand the dependencies between its parts


###### Effect of a Cycle in the Component Dependency Graph

- Suppose new requirement forces us to change one of classes in `Entities` such that it makes use of a class in `Authorizer`
- `User` class in `Entities` uses the `Permissions` class in `Authorizer`
- This creates a dependency cycle (Figure 14.2)

- This creates some immediate problems
- Devs working on `Database` component know tha to release it, the component must be compatible with `Entities`
- However, the `Database` component must now also be compatible with `Authorizer`
- But `Authorizer` depends on `Interactors`
- This makes `Database` much mroe difficult to release
- `Entities`, `Authorizer`, and `Interactors` have, in effect, become one large component - which means that all of the devs working on any of these components will experience the dreaded `morning after syndrome`
- They will be stepping over one another b/c they must all use exactly the same release of one another's components

![alt text](Images/Figure-14.2.png)

- This is just part of the trouble
- COnsider what happens when we want to test the `Entities` component
- We must build and integrate with `Authorizer` and `Interactors`
- This level of coupling between components is problematic

- You may have wondered why you have to include so many different libraries, and so much of everybody else's stuff, just to run a single unit test of one of your classes
- You will probably discover that there are cycles in the dependency graph
- Such cycles make it very difficult to isolate components
- Unit testing and releasing become very difficult and error prone
- Build issues grow geometrically with the number of modules

- When there are cycles in the dependency graph, it can be very difficult to work out the order in which you must build the components
- There probably is no correct order
- This can lead to nasty problems in languages like Java that read their declarations from binary files

###### Breaking the Cycle

- Always possible to break a cycle of components and reinstate the dependency graph as a DAG
- Two primary mechanisms

1. Apply Dependency Inversion Principle (DIP). In case of Figure 14.3, we could create an interface that has the methods that `User` needs. Could then put that interface into `Entities` and inherit it into `Authorizer`.  This inverts the dependency between `Entities` and `Authorizer`, thereby breaking the cycle

![alt text](Images/Figure-14.3.png)

2. Create a new component that both `Entities` and `Authorizer` depend on. Move the class(es) that they both depend on into that new component

![alt text](Images/Figure-14.4.png)

###### The "Jitters"

- Second solution implies that the component structure is volatile in the presense of changing requirement
- As the application grows, the component dependency structure jitters and grows
- Dependency structure must always be monitored for cycles
- Wehn cycles occur, they must be broken somehow
- Sometimes this means creating new components, making the dependency structure grow


###### Top-Down Design

- Component structure `cannot` be designed from the top down
- It is not one of the first things about the system that is designed, but rather evolves as the system grows and changes

- Come to expect that large-grained compositions, like components, will also be high-level `functional` decompositions

- When we see a large-grained grouping such as a compoonent dependency structure, we believe that components ought to somehow represent the functions of the system
- Yet this does not seem to be an attribute of component dependency diagrams

- In fact, component dependency diagrams have very little to do with describing the function of the application
- Instead, they are a map to `buildability` and `maintainability` of the application
- This is why they aren't designed at the beginning of the project
- There is no software to build or maintain, so there is no need for a build and maintenance map
- As more and more modules accumulate in the early stages of implementation and design, there is a growing need to manage the dependencies so that the project can be developed w/o the `morning after syndrome`
- Want to keep changes as localized as possible, so we start paying attention to SRP and CCP and collocate classes that are likely to change together

- One of overriding concerns with this dependency structure is the isolation of volatility.
- Don't want components that hange frequenly and for capricious reasons to affect components taht ought to be stable
- For exampple, cosmetic changes to GUI shouldn't impact business rules
- Don't want the addition or modification of reports to have an impact on our highest-level policies
- Component dependency graph is created and molded by architects to protect stable high-value components from volatile components

- As app grows, start to become concerned about creating reusable elements
- Now CRP begins to influence composition of the components
- As cycles appear, ADP is applied and the component dependency graph jitters and grows

- If we tried to design the component dependency structure before we designed any classes, we would likely fail badly
- We would not know much about common closure
- We would be unaware of any reusable elements
- We would almost certainly create components that produced dependency cycles
- The component dependency structure grows and evolves with the logical design of the system

###### The Stable Dependencies Principle

`Depend in the direction of stability`

- Designs cannot be static
- Some volatility is necessary if the design is to be maintained
- By conforming to Common Closure Principle (CCP), we create components that are sensitive to certain kinds of change but immune to others
- Some of these components are designed to be volatile
- We `expect` them to change

- Any component that we expect to be volatile should not be depended on by a component that is difficult to change
- Otherwise, the volatile component will also be difficult to change

- A module that you have designed to be easy to change can be made difficult to change by someone else who hangs a dependency on it
- Not a line of source code in module needs to change, yet module suddenly becomes more challenging to change
- By conforming to the Stable Dependenies Principle (SDP), we ensure that modules that are intended to be easy to change are not depended on by modules that are harder to change

###### Stability

- Stability has nothing to do with frequency of change

- It is releated to the amount of work required to make a change
- For example, a standing penny is not stable because it requires very little work to topple it. On other hand, a table is very stable because it takes a considerable amount of effort to turn it over

- How does this relate to software?
- One sure way to make a software component difficult to change is to make lots of other software components depend on it
- A component with lots of incoming dependencies is very stable because it requires a great deal of work to reconcile any changes with all the dependent components

- The diagram in Figure 14.5 shows x, which is a stable component
- 3 components depend on x, so it has 3 good reasons not to change
- We say that x is responsible to those 3 components
- Conversely, x depends onnothing, so it has not external influence to make it change
- We say it is independent

![alt text](Images/Figure-14.5.png)

- Figure 14.6 shows Y which is a very unstable component
- No other components depend on Y, so we say that it is irresponsibile
- Y also has 3 components that it depends on, so changes may come from 3 external sources
- Y is dependent

![alt text](Images/Figure-14.6.png)

###### Stability Metrics

- How can we measure stability of a component?
- One way is to count number of dependencies that enter and leave a component
- These counts allow us to calculate the positional stability of the component

- `Fan-In`: Incoming dependencies. This metric identifies the number of classes outside this component that depend on classes within the component
- `Fan-Out`: Outgoing dependencies. This metric identifies the number of classes inside this component that depend on classes outside the component

- `I`: Instability: I = `Fan-out`, (`Fan-in` + `Fan-out`). This metric has range [0, 1]. I = 0 indicates maximally stable component. I = 1 indicates maximally unstable component

- The `Fan-in` and `Fan-out` metrics are calculated by counting number of `classes` outside the component in question that have dependencies with classes inside the component in question.
- Consider Figure 14.7

![alt text](Images/Figure-14.7.png)

- Want to calculate stability of coomponent `Cc`
- 3 classes outside `Cc` that depend on
- So `Fan=in` = 3
- One clouses outside `Cc` that classes in `Cc` depend on
- `Fan-out` = 1
- `I` = 1/4

- `I` metric is easiest to calculate when you have organized your source doe such that there is one class in each source file

- When the `I` = 1, it means no  other components depend on this component (`Fan-In` = 0) and this component depends on other components (`Fan-out > 0`)
- This situation is unstable as a component can get; it is irresponsible and dependent

- When `I` = 0, means that component is depended on by other components (`Fan-in`>0), but does not iteself depend on any other components (`Fan-out`=0), such a component is `responsible` and `independent`
- As stable as it can get
- Its dependents make it hard to change the component
- It has no dependencies that might force it to change

- Stable Dependencies Principle (SDP) says that `I` metric of a component should be larger than the `I` metric of the compnents that it depends on
- `I` metrics should `decrease` in the direction of dependency

###### Not All Components Should Be Stable

- If all components in a system were maximally stable, the system would be unchangeable
- Not desirable situation

- Want to design our component structure so that some components are unstable and some are stable
- Figure 14.8 shows an ideal configuration for a system with 3 components

- Changeable components are on top and depend on a stable component at the bottom
- Putting unstable components at the bottom of a diagram is a useful convetion because any arrow that points `up` is violating the SDP (and ADP)

![alt text](Images/Figure-14.8.png)

- Diagram in 14.9 shows how the SDP can be violated

![alt text](Images/Figure-14.9.png)

- `Flexible` is a component that we have designed to be easy to change
- We want `Flexible` to be unstable
- However, a dev, working in the component named `Stable`, has hung a dependency on `Flexible`
- Violates the SDP b/c the `I` metric for `Stable` is much smaller than the `I` metric for `Flexible`
- As a result, `Flexible` will no longer be easy to change
- A change to `Flexible` will force us to deal with `Stable` and all its dependents

- To fix this problem, we somehow have to break the dependence of `Stable` on `Flexible`
- Why does this dependency exist
- Assume there is a class `c` within `Flexible` that another class `U` within `Stable` needs to use (Figure 14.10)

![alt text](Images/Figure-14.10.png)

- We can fix this by employing DIP
- Create interface class called `US` and put it ina  component named `UServer`
- Make sure that this interface declares all methods that `U` needs to use
- Make `c` implement this interface as show in Figure 14.11
- Breaks dependency of `Stable` on `Flexible`, and forces both components to depend on UServer
- UServer is very stable (`I=0`), and `Flexible` retains its necessary instability (`I=1`)
- All dependencies now flow in the direction of `decreasing I`

![alt text](Images/Figure-14.11.png)

######## Abstract Components

- May find it strange that we would create a component (`UService`) that contains nothing but an interface
- Such a components contains no executable code
- Turns out, however, that this is a very common, and necessary, tatic when using statically typed languages like Java and C#
- These abstract components are very stable and therefore are ideal targets for less stable components to depend on

- When using dynamically typed languages like Ruby or Python, these abstract components don't exist at all, nor do the dependencies taht would have targeted them
- Dependency structures in these languages are much simpler b/c dependecy inversion does not require the declaration or the inheritance of interfaces


###### The Stable Abstractions Principle

`A component should be as abstract as it is stable`

####### Where Do We Put High-Level Policy

- Some software in the system should not change very often
- This software represents high-level architecture and policy decision
- Don't want business and architectural decisions to be volatile
- Software that encapsulates high-level policies of system should be placed into stable components (I=0)
- Unstable components (I=1) should contain only software that is volatile - software that we want to be able to quickly and easily change

- However, if high-level policies are placed into stable components, then source code that represents those policies will be difficult to change
- This could make architecture inflexible
- How can a component that is maximally stable (I=0) be flexible enough to withstand change?
- Answer is found in OCP (open-closed principle)
- Principle tells us that it is possible and desirable to create classes that are flexible enough to be extended without modification
- What kinds of classes conform to this pinciple? `Abstract` classes

######## Introducing Stable Abstractions Principle

- SAP sets up a relationship between stability and abstractness
- Says that a stable component should also be abstract so that its stability does not prevent it from being extended
- Says unstable component should be concrete since it its instability allows the concrete code within to be easily changed
- If a component is to be stable, it should consist of interfaces and abstract classes so that it can be extended
- Stable components that are extensible are flexible and do not overly constrain the architecture

- Stable Abstractions Principle (SAP) and Stable Dependencies Principle (SDP) combined amount to the Dependency Inversion Principle (DIP) for components
- True b/c SDP says dependencies should run in the direction of stability
- SAP says that stability implies abstraction
- `Dependencies run in the direction of abstraction`

- The DIP, however, is the principle that deals with classes - and with classes there are no shades of gray
- Either a class is abstract or it is not

Defintion for abstract class
```
Abstract classes are classes that contain one or more abstract methods. An abstract method is a method that is declared, but contains no implementation. Abstract classes may not be instantiated, and require subclasses to provide implementations for the abstract methods.
```

- Combination of SDP and SAP deals with components, and allows that a component can be partially abstract and partially stable

######## Measuring Abstraction
- The `A` metric is a measure of the abstractness of a component
- Its value is simply the ratio of interfaces and abstract classes in a component to the total number of classes in the component

- `Nc`: Number of classes in component
- `Na`: Number of abstract classes and interfaces in the component
- `A`: Abstractness: `A = Na/Nc`

######## The Main Sequence

- Relationship between stability (I) and abstractness(A)
- Graph with `A` on vertical axis and `I` on horizontal axis (Figure 14.12)
- If we plot two `good` kinds of components on this graph, we find that compnents that are maximally stable and abstract are at upper left (0, 1)
- Components that are maximally stable and concrete are at lower left (1, 0)

![alt text](Images/Figure-14.12.png)

- Not all components fall into one of these two positions
- Components often have `degrees of abstraction` and stability
- Common for abstract class to derive from another abstract class
- Derivative is an abstraction that has a dependency
- Though it is maximally abstract, it will not be maximally stable
- Dependency will decrease stability

- Cannot enforce a rule that all components sit at either (0, 1) or (1, 0) we must assume that there is a locus of points on the A/I graph that defines reasonable positions for components
- Can infer that the locus is by finding the areas where components `should not be` - by determining zones of `exclusion` (Figure 11.13)

![alt text](Images/Figure-14.13.png)

- Consider component in area (0, 0)
- Highly stable and concrete component
- Such a component is not desirable b/c it is rigid
- Cannot be extended b/c it is not abstract, and it is very difficult to change because of its stability
- Do not normally expect to see well designed components sitting near (0, 0)
- Area around (0, 0) is a zone of exclusion called the `Zone of Pain`

- Some software entities do fall within this zone
- Example is database schema
- Database schemas are notoriously volatile, extremely contrete, and highly depended on
- One reason why the interface between OO applications and databases is so difficult to maange and why schema updates are generally painful

- Another example of software that sits near (0, 0) is a concrete utility library
- Although such a library has an `I` metric of 1, it may actually be nonvolatile
- Consider `string` component
- Even though all classes within it are concrete, it is so commonly used that changing it would create chaos
- Therefore `string is nonvolatile`

- Nonvolatile exponents are harmless in (0, 0) since they are not likely to be changed
- Only volatile software components that are problematic in the `Zone of pain`
- The more volatile a component in the zone, the more `painful` it is
- Might consider volatility to be a 3rd axis on the graph
- Figure 14.13 shows only the most painful plane where volatility = 1

######## Zone of Uselessness

- Consider a component near (1, 1)
- Location is undesirable b/c it is maximally abstract yet has no dependents
- Such components are useless

- Software entities that inhabit this region are a kind of detritus
`Detritus: waste or debris of any kind.`
- They are often leftover abstract classes that no one ever implemented
- Find them in systems from time to time, sitting in a code based, unused

- A component that has a deep position within this zone must contain a significant fraction of such entities

####### Avoiding the Zones of Exclusion

- Seems clear that our most volatile components should be kept as far from both zones of exclusion as possible
- Locus of points that are maximally distant from each zone is the line that connects (0, 1) and (1, 0).
- The `Main Sequence`

- Component that sits on the `Main Sequence` is not `too abstract` for its stability, nor is it `too unstable` for its abstractness
- It is neither useless or particularly painful
- It is depended on to the extent that it is abstract
- Depends on others to the extent that is is concrete

- Most desirable position for a component is at one of the two endpoints of the `Main Sequence`
- Good architects strive to position the majority of their components at those endpoints
- Some small fraction of components in a large system are neither perfectly abstract nor perfectly stable
- Those components have the best characteristics if they are on, or close, to the Main sequence

######## Distance from the Main Sequence

- Our last metric
- Measures how far away a component is from this ideal

- D<sup>3</sup>: Distance. D = | A + I - 1 |. Range of metric is [0, 1]. Value of 0 indicates that component is directly on Main Sequence. 1 indicates as far away as possible from Main Sequence

- Design can be analyzed for its overall conformance to the Main Sequence
- The `D` metric for each component can be calculated
- Any component that has a `D` value that is not near zero can be reeexamined and restructured

- Statistical analysis of a design is also possible
- Can calculate mean and variance of all the D metrics for the components within a design
- Expect a conforming design to have a mean and variance close to zero
- Variance can be used to establish `control limits` so as to identify components that are `exceptional` in comparison to others

- In Figure 14.14, we see bulk of components lie along the Main Sequence, but some are more than one standard devization (Z = 1) away from the mean
- Thes aberrant component are worth examining more closely
`Aberrant: departing from an accepted standard.`
- They are either very abstract with few dependents or very concrete with many dependents

![alt text](Images/Figure-14.14.png)

- Another way to use metrics is to plot the D metric of a component over time
- Graph in 14.15 is a mock-up of such a plot
- You can see that some strange dependencies have been creeping into the `Payroll` component over the last few releases
- Plot shows a control threshold at `D = 0.1`
- R2.1 point has exceeded control limit, so it would be worth while to find out why this component is so far from main sequence

![alt text](Images/Figure-14.15.png)

####### Conclusion

- `Dependency management metrics` measure the conformance of a design to a pattern of dependency and abstraction that author thinks is a `good` pattern
- Experience has shown that certain dependencies are good and others are bad
- Pattern reflects this experience
- Metrics are not perfect

#### What is Architecture

- Architecture of a software system is the shape given to that system by those who build it
- Form of that shape is in the division of that system into components, the arrangement of those components, and the ways in which those components communicate with each other
- Purpose of that shape is to facilitate the development, deployment, operation, and maintenance of a software system contained within it

```
The strategy behind that facilitation is to leave as many options open as possible, for as long as possible
```

- Troubles in architecture usually do not lie in their operation
- They occur in their deployment, maintenance, and ongoing development

- Architecture plays `passive and cosmetic` role `not active or essential`
- Few, if any, `behavioral` options that the archtiecture of a system can leave open

- Primary purpose of architecture is to support the life cycle of the system
- Good architecture makes the system easy to understand, easy to develop, easy to maintain, and easy to deploy
- Ultimate goal is to minimize the lifetime cost of the system and to maximize programmer productivity

######## Development

- Software system that is hard to develop is not likely to have a long or healthy lifetime
- Achitecture of a system should make that system easy to develop, for the team(s) who develop it

- Different team structures imply different architectural decisions
- On one hand, small team of 5 devs can work effectively together to develop a monolithic system w/o well-defined components or interfacts
- Such a team would likely find strictures of an architecture something of an impediement during early days of development
- This is likely the reason why so many systems lack good architecture
- They were begin with none, b/c team was small and did not want the impediement of a superstructure

- On other hand, system being developed by 5 different teams, each with 7 devs, cannot make progress unless sysytem is well defined into components with reliably stable interfaces
- If no other factors are considered, the architecture of that system will likely evolve into 5 components - one for each system

- Component-per-team architecture is not likely to be best architecture fro development, operation, and maintenance ofsystem
- It is the architecture, though, that a group of teams with gravitate toward if they are driven solely by development schedule

####### Deployment

- Higher the cost of deployment, the less useful the system is
- Goal of a software architecture, should be to make a system that can be easily deployed `with a single action`
- Deployment strategy is seldom considered during initial development
- Leads to architectures that may make sytem easy to develop, but leave it difficult to deploy

- In early development of a system, developers may decide to use the `micro-service architecture`
- May find that this approach makes the system very easy to develop since component bundaries are very firm and the interfaces are relatively stable
- When it comes time to deploy, they may discover that the numberof microservices has become daunting
- Configuring connections between them, and the timing of their initation, may also turn out to be huge sources of errors

- Had architects considered deployment issues early on, they might have decided on fewer servies, a hybrid of services and in-process components, and a more integrated means of managing the interconnections

######## Operation

- Impact of architecture on system operations tend to be less dramatic than the impact of architecture on development, deployment, and maintenance
- Most any operational difficult can be resolved by throwing more hardware at the system w/o drastically impacting the software architecture

- Software systems that have inefficient architectures can often be made to work effectively simply by adding more storage and more servers
- Hardware is cheap and people are expensive means that architectures that impede operation are not as costly as architectures that impede development, deployment, and maintenance

- Cost equation leans more toward development, deployment, and maintenance

- Good software architecture communicates operational needs of the system

- Architecture of a system makes the operation of the system readily apparent to the developers
- Architecture should reveal operation
- Architecture should elevate the use cases, the features, and the required behaviors of the system to first-class entities that are visible landmarks for the devs
- Simplifies understanding of the system and, therefore, greatly aids in development and maintenance

######## Maintenance

- Maintenance is the most costly
- Never ending parade of new features and the inevitable trail of defects and corrections consume vast amounts of human resources

- Primary cost of maintenance is `spelunking` and risk
- Spelunking is the cost of digging through existing software, trying to determine the best place, and the best strategy to add a new feature or repair a defect
- While making changes, the likelihodo of creating inadvertent defects is always there, adding to the cost of risk

- Carefully thought-through architecture vastly mitigates these costs
- By separating system into components, and isolating those components through stable interaces, it is possible to illuminate the pathways for future features and greatly reduce the risk of inadvertent breakage

######## Keeping Options Open

- Software has two types of value
- Value of its behavior
- Value of its structure
- Second of these is the greater of the two b/c it is this value that `makes software soft`

- The way you keep software soft is to leave as many options open as possible, for as long as possible
- What are the options that we need to leave open?
- `They are the details that don't matter`

- All software systems can be decomposed into two major elements: `policy and details`
- Policy element embodies all the business rules and procedures
- The policy is where the true value of the system lives

- The details are other things that are necessary to enable humans, other systems, and programmers to communicate with policy
- They do not impact behavior of policy at all
- Tis inclues IO devices, databases, web systems, servers, frameworks, communication protocols, and so forth

- Goal of architect is to create a shape for the system that recognizes policy as the most essential eement of the system while making the details `irrelevant` to that policy
- This allows decisions about those details to be `delayed` and `deferred`

For example:
- Not necessary to choose database system in the early days of development b/c the high level policy should not care which kind of databse will be used. If architect is careful, the high-level policy will not care if the databse is relational, distributed, hierarchical, or just plain flat files
- Not necessary to choose web server in early development b/c high-level policy should not know that it is being delivered over the web
    - If high-level policy is unaware of HTML, AJAX, JSP, JSP, or anything else in web development, then you don't need to decide which web system to use until much later in the project
    - You don't even have to decide if the system will be delivered over the web
- Not necessary to adop `REST` early in development b/c high level policy should be agnostic about the interface to the house world
    - Nor is it necessary to adopt a micro-services framework, or a SOA framework
- Not necessary to adopt a dependency injection framework early in development b/c high level policy should not care how dependencies are resolved

- If you can develop high-level policy w/o committing to the details that surround it, you can delay and defer decisions about those details for a long time
- Longer you wait, `the more information you have with which to make them properly`
- Can experiment with different options by doing this
- If you have a portion of the high-level policy working, and it is agnostic about the database, you could try connecting to several different databases to check applicabilitu and performance
- Same is true with web systems, web frameworks, or even the web itself

- What if these decisions have already been made by someone else?
- `Good architect pretends that the decisions have not been made` and shapes the system such that those decisions can still be deferred or changed for as long as possible

`A good architect maximizes the number of decisions not made`

######## Device Independence
- Example of why you should defer decisions about things that don't matter to the high-level policy (page 231)

###### Independence

- Good architecture must support:
    - The use cases and operation of the system
    - Maintenance of the system
    - Development of the system
    - Deployment of the system

######## Use cases

- Architecture must support the intent of the system
- First concern of the architect
- First priority of the architecture

- However, architecture does not wield much influence over the behavior of the system
- Most important thing a good architecture can do to support behavior is to clarify and expose that behavior so that the intent of the system is visible at the architectural level

- A shopping cart app with a good architecture will look like a shopping cart app
- Use cases of system will be plainly visible within structure of that system
- Developers will not have to hunt for its behaviors/ b/c those behaviors will be 1st-class elements visible at the top level of the system
- Those elements will be classes/functions/modules that have prominient positions within architecture
- They will have names that clearly describe their function

######## Operation

- Architecture plays a more substantial, and less cosmetic, role in supporting operation of the system
- If system must handle 100k customers per second, architecture must support that kind of throughput and response time for each use case that demands it
- If system must query big data cubes in milliseconds, then architecture must be structured to allow this kind of operation

- For some systems, this will mean arranging the processing elements of the system into an array of little services that can be run in parallel on many different servers
- For other systems, it will mean a plethora of little lightweight threads sharing the address space of a single process within a a single processor
- This decision is one that a good architect leaves open
- A system that is written as a monolith, and that depends on that monolithic structure, canno teasily be upgrade to multiple processes, multiple threads, or micro-services should the need arise
- An architecture that maintains proper isolation of its components, and does not assume the means of communication between those components, will be much easier to transition through the spectrum of threads, processes, and services as the operational need of the system changes over time

######## Development

- Architecture plays a significant role in supporting the development environment
- This is where `Conway's Law` comes into play

```
Any organization that designs a system will produce a design whose structure is a copy of the organizations communication structure
``` 

- A system that must be developed by an org with many teams and concerns must have an architecture that facilitates independent actins by those teams, so that the teams do not interfere with each other during development
- Accomplished by properly partitioning the system into well-isolated, indepedently developable components
- Those components can be then allocated to teams that can work independently of each other

######## Deployment

- Architecture plays huge role in determining ease with which the system is deployed
- Goal is `imemdiate deployment`
- Good architecture does not rely on dozens of little configuration scripts or property file tweaks
- Does not require manual creation of directories or files that must be arranged just so
- Good architecture helps system to be immediately deployable after build

- This is achieved through proper partitioning and isolation of the components of the system including `master components` that tie the whole system together and ensure that each component is properly started, integrated, and supervises

######## Leaving Options Open

- Good architecture balances all of these concerns w/ a component structure that mutually satisfies them all

- This balance is hard to achieve
- Problem is most of the time we don't know what all the use cases are or the operational constrains or the team structure or the deployment requirements
- Even if we did know them, they will change as the system moves through its life cycle
- Goals we must meet are indistinct and inconstant

- Some principles of architecture are relatively inexpensive to implement and can help balance those concerns, even when you don't have a clear pitcure of the targets you have to hit
- These principles help us partition our system into well-isolated components that allow us to leave as many options open as possible, for as long as possible

- A good architecture makes the system easy to change, in all the ways that it must change, by leaving the options open

######## Decoupling layers

- Architect wants the structure of the system to support all necessary uses cases, but does not know all use cases
- Architect does know basic intent of the system
- It's a shopping cart system, or it's a bill of materials system, or it's an order processing system
- So architect can employ `Single Responsibility Principle` and the `Common Closure Principle` to separate thsoe things that change for different reasons, and to collect those things that change for the same reasons - given the context of the intent of the system

- What changes for different reasons?
- There are some obvious things
- UI changes for reasons that have nothing to do with business rules
- Use cases have elements of both of those
- Good architect will want to separate the UI portions of a use case from the business rule portions in such a way that they can be changed independently of each other, while keeping those use cases visible and clear

- Business rules themselves may be closely tied to the application, or they may be more general
- For example, the validation of input fields is a business rule that is closely tied to the application itself
- In contrast, the calculatio of interest on an account and the counting of inventory are business rules that are more closely associated with the domain
- These two different kinds of rules will change at different rates, and for different reasons
- They should be separated so that they can be independetly changed

- The database, query language, and even the schema are technical details that have nothing to do w/ the business rules or the UI
- They will change at rates, and for reasons, that are independent of other aspects of the system
- Architecture should separate them from the rest of the system so that they can be independently changed

######## Decoupling Use Cases

- What else changes for differnet reasons?
- Use cases themselves do
- Use case for adding an order to an order entry ssystem almost certainly will change at a different rate, and for different reaosns, than the use case that deletes an order from the system
- Use cases are a very natural way to divide the system

- At the same time, use cases are narrow vertical slices that cut through the horizontal layers of the system
- Eah use cases uses some UI, some application-specific business rules, some application-dependent business rules, and some database functionality
- As we are dividing the system in horizontal layers, we are also dividing the system into thin vertical use cases that cut through those layers

- To achieve this decoupling, we separate the UI of the add-order use case from the UI of the delete-order use case
- We do the same with the business rules, and with the database
- We keep the use cases separate down the vertical height of the system

- There is a pattern here
- If you decouple the elements of the system that change for different reasons, then you can continue to add new use cases w/o interfering with old ones
- If you also group the UI and database in support of those use cases, so that each use case uses a different aspect of the UI and database, then adding new will be unlikely to affect older ones

######## Decoupling Mode

- If the different aspects of the use cases are separated, then those that must run at a high throughput are likely already separated from those that must run at low throughput
- If the UI and database have been separated from business rules, then they can run on different servers
- Those that require higher bandwidth can be replicated in many servers

- The decoupling we did for the sake of the use cases also helps w/ the operations
- To take advantage of the operational benefit, the decoupling must have the appropriate mode
- To run in separate servers, the separated components cannot depend on being together in the same address space of the processor
- They must be independent services, which communicate over a network of some kind

- Many architects call such components `services` or `microservices` depending upon the vague notion of line count
- An architecture based on services is often called a service-oriented architecture

- Sometimes we have to separate our components all the way to the service level

- Remember, a good architecture leaves options open
- `The decoupling mode is one of those options`

######## Independent Developability

- When components are strongly decoupled, the interference between teams is mitigated
- If the business rules don't know about the UI, then the team that focuses on the UI cannot much affect a team that focuses on the business rules
- If the use cases themselves are decoupled from one another, then a team that focuses on `addOrder` use case is not likely to interfere with a team that focuses on the `deleteOrder` use case

- So long as layers and use cases are decoupled, the architecture of the system will support the organization of the teams, irrespective of whether they are organized as feature teams, component teams, layer teams, or some other variation

######## Independent Deployability

- Decoupling of the use cases and layers affords a high degree of flexibility in deployment
- If decoupling is done well, then it should be possible to hot-swap layers and use cases in running systems
- Adding a new use case could be as simple as adding a few new jars or services to the system while leaving the rest alone

######## Duplication

- Architects often fall into a trap - a trange that hinges on their fear of duplication

- Duplication is generally bad
- But there are different kinds of duplication
- There is true duplication in which every change to one instance necessitates the same change to every duplicate of that instance
- There is false/accidental duplication
- If two apparently duplicated sections of code evolve along different paths - if they change at different rates, and for different reasons - `then they are not true duplicates`
- If you returned to these in a few years, they would be very different from each other

- Image two use cases that have very similar screen structures
- Architects will likely be stongly tempted to share code
- Should they? Is it true duplication? Or is it accidental?

- Most likely it's accidental
- As time goes by, odds are that those two screens will diverge and eventually look different
- For this reason, care must be taken to avoid unifying them
- Otherwise, separating them later will be a challenge

- When you are vertically separating use cases from one antoher, you will run into this issue, and you temptation will be to couple the use cases b/c they have similar screen structures, similar algorithms, similar database queries and/or schemas
- Resist temptation to commit knee-jerk elimination of duplication
- Make sure duplication is real

- By same token, when separating layers horizontally, you might notice that the data structure of a particular database record is very similar to the data structure of a particular screen view
- May be tempted to simply pass the database record to the UI, rather than create a view model that looks the same and copy elements across
- Be careful: this duplication is almost certainly accidental
- Creating a separate view model is not a lot of effor, and it will help you keep the layers properly decoupled

######## Decoupling Modes (Again)

- There are many ways to decouple layers and use cases
- They can be decoupled at source level, at binary code (deployment) level, and at the execution unit (service) level

- Source level: We can control the dependenices btwn source code modules so that changes to one module do not force changes or recompliation of others (e.g., Ruby Gems)
    - In this decoupling mode, the components all execute in the same address space, and communicate with each other using simple function calls
    - Single executable loaded into computer memory
    - People often call this a `monolithic structure`
- Deployment level: Can control dependencies btwn deployable units such as jar files, DLLs, or shared library, so that changes to the source code in one module to not force others to be rebuilt and redeployed
    - Many components may still live in the same address space and communicate through function calls
    - Other components may live in other processes in the same processor, and communicate through interprocess communications, sockets, or shared memory
    - Important thing here is that the decoupled components are partitioned into independely deployable units such as jar files, Gem files, or DLLs
- Service level: We can reduce the dependencies down to the level of data structures, and communicate solely through network pckets such that every execution unit is entirely independent of source and binary changes to others (e.g., services or microservices)

- What is the best mode to use?

- Hard to know which mode is best during early phases of a project
- As the project matures, the optimal mode may change

- It's not difficult to imagine that a system that runs comfortable on one server right now might grow to to the point where some of its components ought to run on separate servers
- While the server runs on a single server, the source-level decoupling might be sufficient
- Later, it might require decoupling into deployable units or even services

- One solution (which seems popular at the moment) is to decouple at the service level by default
- A problem with this approach is that it is expensive and encourages coarse-grained decoupling
- No matter how `micro` the microservices get, the decoupling is not likely to be fine-grained enough

- Another problem w/ service-level decoupling is that it is expensive, in both dev time and in system resources
- Dealing w/ service boundaies where none are needed is waste of effort, memory, and cycles

- Push decoupling to the point where services could be formed should be necessary
- Then leave components in same address space as long as possible
- This leaves option for a service option

- W/ this approach, initially components are separated at source code level
- This may be good enough for duration of project
- If deployment or development issues arise, driving some of decoupling to a deployment level may be sufficient (at least for awhile)

- As development, deployment, and operation issues increase, author carefully chooses which deployable units to turn into services, and gradually shift the system in that direction

- Over time, operational needs of the system may decline
- What once required decoupling at the service level may not require only deployment-level decoupling or even source-level decoupling

- A good architecture allows a system to be born as a monolith, deployed into a single file, but then grow into a set of independently deployable units, and then all the way to independently deployable units, and then all the way to independent services and/or microservices
- Later as things change, it should allow for reversing that progression and sliding all the way back into a monolith

- Good architecture projects maorith of the source code from those changes
- It leaves decoupling mode open as an option so that large deployments can use one mode, where small deployments use another

######## Conclusion

- This is tricky
- Change of decoupling modes should not be a trivial configuration option (though sometimes that is appropriate)
- Decoupling mode of a system is one of those things that is likely to change w/ time
- A good architect foresees and appropriately facilitates those changes


#### Boundaries: Drawing Lines

- Software architecture is the art of drawing lines called `boundaries`
- Boundaries separate software elements from one another
- Restricts those on one side from knowing about those on the other
- Some of these lines are drawn early (maybe even before code is written)
- Others are drawn latter
- Those that are drawn latter are drawn for the purposes of deferrign decisions for as long as possible, and of keeping those decisions from polluting the core business logic

- Gaol of an architect is to minimize the human resources required to build and maintain the required system
- What is it that saps this kind of people power?
- `Coupling` - and especially coupling to premature decisions

- What kinds of decisions are premature
- Decisions that have nothing to do w/ business requirement (the use cases) of the system
- These include decisions about frameworks, databases, web servers, utility libraries, dependency injection, and the like
- A good system architecture is one in which decisions like these are rendered ancillary and deferrable
- Good system architecture does not depend on these decisions
- Good system architecture allows those decisions to be made at the latest possible moment, w/o significant impact

######## Which Lines Do You Draw, And When Do You Draw Them?

- You draw lines btwn things that matter and things that don't
- The GUI doesn't matter to the business rules, so there should be a line btwn them
- The database doesn't matter to the business rules, so there should be a line btwn them

- You can see this clearly in Figure 17.1 (with respect to the database)
- The `BusinessRules` use the `DatabaseInterface` to load and save data
- The `DatabaseAccess` implements the interface and directs the operation of the actual database

![alt text](Images/Figure-17.1.png)

- Classes and interfaces in this diagram are symbolic
- In a real application there would be many business rule classes, many database interface classes, and may database access implementations
- All of them would roughly follow this same pattern though

- The boundar is drawn across the inheritance relationship, just below `DatabaseInterface` (Figure 17.2)

![alt text](Images/Figure-17.2.png)

- Note the two arrows leaving the `DatabaseAccess` class
- Those two arrows point away from the `DatabaseAccess` class
- Tha tmeans that none of these classes know that the `DatabaseAccess` class exists

- Next is a component that contains many business rules, and the component that contains the database and all its access classes (Figure 17.3)

![alt text](Images/Figure-17.3.png)

- Note the direction of the arrow
- The `Database` knows about the `BusinessRules`
- The `BusinessRules` do not know about the `Database`
- This implies that the `DatabaseInterface` classes in the `BusinessRules` component, while the `DatabaseAccess` classes live in the `Database` component

- The direction of this line is important
- Shows that the `Database` does not matter to the `BusinessRules`, but the `Database` cannot exist w/o the `BusinessRules`

- `Database` component contains the code that translates the calls made by the `BusinessRules` into the query language of the database
- It is that translation code that knows about the `BusinessRules`

- Having drawn this boundary btwn the to components, and having set the direction of the arrow toward the `BusinessRules`, we can now see that the `BusinessRules` could use any kind of database
- The `database` component could be replaced with many different implementations - the `BusinessRules` don't care

- Database decision can be deferred and you can focus on getting the business rules written and tested before you have to make a database decision

######## What About Input and Output?

- Devs and customers often get confused about what the system is
- They see the GUI, and think that the GUI is the system
- They define system in terms of GUI
- Believe that they should see the GUI start working immediately
- They fail to realize a critically important principle: `the IO is irrelevant`

- The interface (the GUI) does not matter to the model (the business rules)
- As a result, `GUI` and `BusinessRules` components are separated by a bounday line (Figure 17)
- We see that less relevant component depends on the more relevant component
- Arrow shows which component knows about the other, and therefore, which components cares about the other
- The `GUI` cares about the `BusinessRules`

![alt text](Images/Figure-17.4.png)

######## Plugin Architecture

- Taken together, these 2 decisions about database and GUI create a kind of pattern for the addition of other components
- This pattern is the same pattern that is used by systems to allow 3rd party plugins

- History of software development technology is the story of how to conveniently create plugins to establish a scalable and maintainable system architecture
- Core business rules are kept separate from, and independent of, those components that are either optional or that can be implemented in different forms

![alt text](Images/Figure-17.5.png)

- B/c the user interface in this design is considered to be a plugin, we have made it possible to plug into many different kinds of UIs
- Could be web based, client/server based, SOA based, console based, or based on any other kind of user interface technology

- Same is true of the database
- Since we have chosen to treat it as a plugin, we can replace it with any of the various SQL databases, or a NOSQL database, or a file system-based database, or any other kind of database technology we might deem necessary in the future

- These replacemeents might not be trivial though
- If initial deployment of our system was web-based, then writing the plugin for a client-server UI could be challenging
- Likely that some of the communications btwn the business rules and the new UI would have to be reworked
- However, by starting with presumption of a plugin structure, we have at least made such a practical change

######## The Plugin Argument

- Consider relationship between ReSharper and Visual Studio
- Components made by completely different teams in completely differnet companies

- Code of ReSharper depends on the source code of Visual Studio
- Nothing that the ReSharper team could do to disturb the Visual Studio team
- But Visual Studio team could completely disable the ReSharper team if they so desired

![alt text](Images/Figure-17.6.png)

- Deeply asymmetric relationship
- One that we desire to have in our own systems
- Want certain modules to be immune to others
- Don't want business rules to break when someone changes the format of a web page, or changes the schema of the database
- Don't want changes in one part of the system to cause other unrelated parts of the system to break
- Don't want our system to exhibit that kind of fragility

- Arranging system in plugin architecture creates firewalls across which changes cannot propagate
- If GUI plugs in to the business rules, then the changes in the GUI cannot affect those business rules

- Boundares are drawn where there is an `axis of change`
- Components on one side of the boundary change at different rates, and for different reasons, than the components on the other side of the boundary

- GUIs change at different times and at different rates than the business rules, so there should be a boundary between them
- Business rules change at different times and for different resosns than dependency injection framework, so there should be a boundary btwn them

- This is the Single Responsibility Principle again
- SRP tells us where to draw our boundaries

######## Conclusion

- To draw boundary lines in a software architecture, you first partition the system into components
- Some of those components are core business rules
- Others are plguins that contain necessary functions that are not directly related to the core business
- Arrange the code in those components such that the arrays between them point in one direction - `towards the core business`

- This is an application of the `Dependency Inversion Principle` and the `Stable Abstractions Principle`
- Dependency arrows are arranged to point from lower-level details to higher-level abstractions

#### Boundary Anatomy

- Architecture of a system is defined by a set of software components and the boundaries that separate
- Boundaries come in many different forms

######## Boundary Crossing

- At runtime, boundary crossing is nothing more than function on one side of boundary calling a function on the other side and passing along some data
- Trick to creating appropriate boundary is to manage the source code dependencies

- Why source code?
- B/c when one source code module changes, other source code modules may have to be changed, or recompiled, and then redeployed
- Managing and building firewalls against this change is what boundaries are all about

######## The Dreaded Monolith

- Simplest and most common of the architectural boundaries has no strict physical representation
- Simply a disciplined segregation of functions and data within a single processor and single address space
- This is called `source-level decoupling mode`

- From deployment point of view, this amounts to a single executable file - the so-called monolith
- This file might be statically linked to C to C++ project, a set of Java classes bound together into an executable jar file
- a set of .NET binaries bound into single .EXE file, and so on

- Even though boundaries are not visible during the deployment of a monolith does not mean that they are not present and meaningful
- Even when statically linked into a single executable, the ability to independently develop and marshal the various components for final assembly is immensely valuable

- Simplest possible boundary crossing is a function call from a low-level client to a higher-level service
- Both the run time dependency and the compile-time dependency point in the same direction, toward the higher-level component

- In Figure 18.1, the flow of control crosses the boundary from left to right
- `Client` calls function f() on the `Service`
- Passes along an instance of `Data`
- `Data` may be passed as a function argument or by some other more elaborate means
- Note that the definition of `Data` is on the `called` side of the boundary

![alt text](Images/Figure-17.7.png)

- When a high-level client needs to invoke a lower-level service, dynamic polymorphism is used to invert dependency against flow of control

- Runtime dependency opposes the compile-time dependency

- In Figure 18.2, the flow of control crosses the boundary from left to right as before
- High-level `Client` calls the `f()` function of the lower-level `ServiceImpl` through the `Service` interface
- Note, however, that all dependencies cross the boundary from right to left `toward the higher-level component`
- Note, also, that the definition of the data structure is on the calling side of the boundary

![alt text](Images/Figure-17.8.png)

 - Even in monolithic, statically linked executable, this kind of disciplined partitioning can greatly aid the job of developing, testing, and deploying the project
 - Teams can work independetly of each other on their own components w/o treading on each other's toes
 - High-level components remain independent of lower-level details

 - Communications btwn componnets in a monolith are very fast and inexpensive
 - Typically are just function calls
 - Consequently, communicaitons across source-level decoupled boundaries are very chatty

 - Since deployments of monoliths usually requires compliation and static linking, components in these systems are typically delievered as source code

 ######## Deployment Components

 - Simplest physical representation of an architectural boundary is a dynamically linked library like a .NET DLL, a Java jar file, a Ruby gem, or a UNIX shared library
 - Deployments do not involve compilation
 - Instead, components are delivered in binary, or some equivalent deployable form
 - This is the deployment-level decoupling mode
 - Act of deployment is simply the gathering of these deployable units together in some convenient form, such as a WAR file, or even just a directory

 - W/ that one exception, deployment-level components are the same as monoliths
- Functions generally all exist in the same processor and address space
- Strategies for segragating componenents are managing their dependencies are the same

- As w/ monoliths, communications across deployment component boundaries are just function calls, and therefore, are very inexpensive
- May be one-time hit for dynamic linking or runtime loading, but communications across these boundaries can still be very chatty

######## Threads

- Both monoliths and deployment components can make use of threads
- Threads are not architectural boundaries or units of deployment, but rather a way to organize and schedule order of execution
- May be contained within a component, or spread across many components

######## Local Processes

- Much stornger physical architectural boundary is a local process
- Local process is typically created from cmd line or equivalent system call
- Local processes run within same processor, or in the same set of processors within a multicore, but run in separate address spaces
- Memory protection generally prevents such processes from sharing memory, althrough shared memory partitions are often used

- Most often, local processes communicate with each other using sockets, or some other kind of operating system communications facility such as mailboxes or message queues

- Each local process may be a statically linked monolith, or it may be composed of a dynamically linked deployment components
- In former case, several  monolithic processes may have the same components compiled and linked into them
- In the latter, they may share the same dynamically linked deployment components

- Think of a local process as a kind of uber-component
- THe process consists of lower-level components that manage their dependencies through dynamic polymorphism

- Segregation strategy btwn local processes is the same as for monoliths and binary components
- Source code dependenices point in the same direction across the boundary, and always toward the higher-level component

- For local processes, this means that the source code of the higher-level processes must not contain names, physical addresses, or registry lookup keys of lower-level processes
- REMEMBER that the architectural goal is for lower-level processes to be plugins to higher-level processes

- Communication across local process boundaries involve operation system calls, data marshaling and decoding, and interprocess context switches which are moderately expensive
- Chattiness should be limited

```
Data Marshaling:

The process of gathering data and transforming it into a standard format before it is transmitted over a network so that the data can transcend network boundaries. In order for an object to be moved around a network, it must be converted into a data stream that corresponds with the packet structure of the network transfer protocol. This conversion is known as data marshalling. Data pieces are collected in a message buffer before they are marshaled. When the data is transmitted, the receiving computer converts the marshaled data back into an object.

Data marshalling is required when passing the output parameters of a program written in one language as input to a program written in another language.

https://www.webopedia.com/TERM/D/data_marshalling.html
```

######## Services

- Strongest boundary is a service
- A service is a process that is generally started from cmd line or through system call
- Services do not depend on physical location
- Two communicating services may, or may not, operate in same physical processor or multicore
- The services assume that all communication take place over network

- Communications across service are very slow compared to function calls
- Turnaround times can range from tens of milliseconds to seconds
- Care must be taken to avoid chatting where possible
- Communications at this level must deal with high levels of latency

- Otherwise, the same rules apply to services as apply to local processes
- Low-level services should `plug in` to higher-level services
- Source code of `higher-level` services must not contain any specific physical knowledge (e.g., a URI) of a lower-level service

######## Conclusion

- Most systems, other than monoliths, use more than one boundary strategy
- A system that makes use of service boundaries may also have some local process boundaries
- A service is often just a facade for a set of interacting local processes
- A service, or a local process, will almost cretainly be either a monolith composed of source code components or a set of dynamically linked deployment components

- This means that boundaries in a system will often be a mixture of local chatty boundaries and boundaries that are more concerned with latency

#### Policy and Level

- Software systems are statements of policy
- A computer program is a detailed description of a policy by which inputs are transformed into outputs

- In most nontrivial systems, that policy can be broken down into many different smaller statements of policy
- Some of those statements will describe how particular business rules are to be calculated
- Others will describe how certain reports are to be formatted
- Still some others will describe how input data are to be validated

- Part of art of developing a software architecture is carefully separating those policies from onee other, and regrouping them based on the ways that they change
- Policies that change for the same reason, and at the same times, are at the same level and belong together in the same component
- Policies that change for different reasons, or at different times, are at different levels and should be separated into different components

- Art of architecture often involves forming the regrouped components into a directed acyclic graph
- Nodes of the graph are the components that contain policies between those components
- They connect components that are at different levels

- Those dependencies are source code, compile-time dependencies
- In Java, they are `import` statements
- In C#, they are using statements
- In Ruby, they are require statements
- They are dependencies that are necessary for the compiler to function

- In a good architecture, the direction of those dependencies is based on the level of the components that they connect
- In every case, low-level components are designed so that they depend on high-level components

######## Level

- A strict defintion of `level` is `the distance from the inputs and outputs`
- The farther a policy is from both inputs and outputs of the system, the higher the level
- The policies that manage the input/output are the lowest-lvel policies in the system

- The data flow diagram in `Figure 19.1` depicts simple encryption program that reads chars form an input device, translates them using a table, and then writes the translated characters to an output device
- Data flows are shown as curved solid arrows
- Properly designed source code dependencies are shown as straight dashed lines

![alt text](Images/Figure-17.9.png)

- The `Translate` component is the highest-level component in this system b/c it is the component that is farthest from the inputs and outputs

- Note that the data flows and the source code do not always point in the same direction
- This is part of the art of software architecture
- We want source code dependencies decoupled from data flow and coupled ot level

- It would be easy to create incorrect architecture by writing the encryption program like this:
```
function encrypt() {
  while(true)
    writeChar(translate(readChar()));
}

Excerpt From: Robert C. Martin. “Clean Architecture: A Craftsman's Guide to Software Structure and Design (Robert C. Martin Series).” iBooks. 
```

- Incorrect because the high-level `encrypt` function depends on the `lower-level` readChar and the `writeChar` functions

- A better architecture for this system is shown in the class diagram Figure 19.2
- Note the dashed border surrounding the `Encrypt` class, and the `CharWriter` and the `CharReader` interfaces
- All the dependencies crossing that border point inward
- This unit is the highest-level element in the system

![alt text](Images/Figure-19.2.png)

- `ConsoleReader` and `ConsoleReader` are shown here as classes
- They are low level b/c they are close to the inputs and outputs

- This structure decouples the high-level encryption policy from the lower-level input/output policies
- This makes encryption policy usable in a wide range of contexts
- When changes are made to the input and output policies, they are not likely to affect the encryption policy

- Recall that policies are grouped into components based on the way that they change
- Policies that change for the same reasons and at the same times are grouped together by the SRP and the CCP
- Higher-level policies - those that that are farthest from the inputs and outputs - tend to change less frequently, and for more important reasons, than lower-level policies
- Lower-level policies - those that are closest to inputs and outputs - tend to change frequently, and with more urgency, but for less important reasons

- For example, even with trivial example of encryption program, it is far more likely that the IO devices will change than that the encryption algorithm will change
- If encryption algorithm does change, it will be for a more substantive reason thana change to one of the IO decies

- Keeping these policies separate, with all source code dependencies, pointing in the direction of the higher-level policies, reduces level of impact
- Triival but urgent changes at the lowest levels of the system have little or no impact on the higher, more important, levels
- Another way to look at this issue is to note that lower-level components should be plugins to the higher-level components
- Component diagram in Figure 19.3 shows this arrangement
- `Encryption` component knows nothing of the IODevices component; the `IODevices` component depends on the `Encryption` component

![alt text](Images/Figure-19.3.png)

######## Conclusion

- At this point, discussion of policies has involved mixture of SRP (single responsibility principle) and OCP (open closed principle), Dependency Inversion Principle, Stable Dependencies Princple, and the Stable Abstractions Principle


#### Business Rules

- If we are going to divide our app into business rules and plugins, good grasp of business rules required
- Several different kinds

- Strictly speaking, business rules are rules or procedures that make or save the business money
- Very strictly speaking, these rules would make or save the business money, irrespective of whether they were implemented on a computer
- Would make/save money even if they were executed manually

- The fact that a bank charges `N%` interest for a loan is a business rule that makes the business money
- Doesn't matter if a computer program calculates the interest, or if a clerk with an abacus does

- We call these rules `Critical Business Rules`
- Critical to business itself
- Would exist even if there were no system to automate them

- Critical Business Rules usually require some data to work with
- For example, our loan requires a loan balance, an interest rate, and a payment schedule

- Critical rules and critical data are inextricably bound, so they are a good candidate for an object
- We'll call this kind of object an `Entity`

######## Entities

- An entity is an object within our computer system that embodies a small set of critical business rules operating on Critical Business Data
- Entity object either contains the Critical Business Rules or has very easy access to that data
- The interface of the Entity contsists of the functions that implement the Critical Business Rules that operate on data

- In Figure 20.1, it shows what our `Loan Entity` might look like as a class in UML
- 3 pieces of Critical Business Data, and presents 3 related Critical Business Rules at its interface

![alt text](Images/Figure-20.1.png)

- Wheb we create this kind of class, we are gathering together the software that implements a concept that is critical to the business, and separating it from every other concern in the automated system we are building
- Class stands alone as a representative of business
- Unsullied w/ conerns about databases, user interfaces, or 3rd party frameworks
- Could serve business in any system, irrespective of how system is presented, or how data was presented, or hwo data was stored, or how computers in that system were arranged
- The `Entity` is pure business and nothing else

- Don't need to use an object-oriented language to create an Entity
- All that is required is that you bind the Critical Business Data and the Critical Business Rules together in a single and separate software module

######## Use Cases

- Not all business rules are as pure as Entities
- Some business rules make/save money for business by defining and constraining the way that an automated system operates
- These rules would not be used in a manual environment, b/c they only make sense as part of automated system

- For example, imagine app that is used by bank officers to create a new loan
- Bank may decide it does not want loan officers to offer loan payment estimates until they have first gathered, and validated, contact infomration and ensured that a candidate's credit score is 500 or higher
- For this reason, bank may specify that the system will not proceed to the payment estimation screen until the contact info screen has been filled out and verified, and credit score has been confirmed to be greater than the cutoff

- This is a `use case`
- A use case is a description of the way that an automated system is used
- Specifies input provided by the user, the output to be returned to the user, and the processing steps involved in producing that output
- A use case describes `application-specific` business rules as opposed to the Critical Business Rules within the Entitiess

- Figure 20.2 shows example of use case
- Notice in last line it metiones Customer
- Reference to Customer entity, which contains the Critical Business Rules that govern the relationship between the bank and its customers

![alt text](Images/Figure-20.2.png)

- Use cases contain the rules that specify how and when the Critical Business Rules within the Entities are invoked
- Use cases control the dance of Entities

- Notice that the use case does not describe the UI other than to informally specify data coming in from that interface, and data going back through that interface
- From use case, it is impossible to tell whether the app is delivered on the web, or on a thick client, or on a console, or is a pure service

- This is very important
- Use cases do NOT describe how the application appears to the user
- They describe application-specific rules that govern interaction btwn users and the Entities
- How the data gets in and out of the system is irrelevant to the use cases

- A use case is an object
- It has one or more functions that implement the appliation-specific business rules
- Also has data elements that include input data, output data, and the references to the appropriate Entities with which it interacts

- Entities have no knowledge of the use cases that control them
- Another example of the direction of dependencies following the DIP
- Higher level concepts, such as Entities, know nothing of lower-level concepts, such as use cases
- Instead, lower-level use cases know about the higher-level Entities

- Why are Entities high level and use cases lower level?
- B/c use cases are specific to a single app and, therefore, are closer to inputs and outputs of that system
- Entities are generalizations taht can be used in many different apps, so they are further from inputs and outputs of the system
- Use cases depend on Entities; Entities do not depend on use cases

######## Request and Response Model

- Use cases expect input data, and they produce output data
- However, a well-formed use case object should have no inkling about the way that data is communicated to the user, or to any other component
- Certainly don't want the code within the use case to know about HTML or SQL

- The use cases accepts simple request data structures for its input, and returns simple response data structures as its output
- These data structures do not depend on anything
- Do not derice from standard framework interfaces such as `HttpRequest` and `HttpResponse`
- Know nothing of the web, nor do they share any of the trappings of whatever UI might be in place

- This lack of dependencies is critical
- If request and response models are not independent, then the use cases that depend on them will indirectly bound to whatever dependencies the models carry with them

- May be tempted to have data structures contain references to the Entities objects
- May think this makes sese b/c the Entities and the request/response models share so mcuh data
- Avoid this
- PUrpose of these two objects is very different
- Over time they will change for different reasons, so tying them together violates the Common Closure and the Single Responsibilities Principles
- The result would be lots of tramp dta, and lots of conditionals in your code

######## Conclusion

- Business rules are the reason a software system exists
- They should remain pristine, unsullied by baser concerns such as UI and database used
- Ideally the code represents the business rules that should be the heart of system w/ lesser concerns being plugged into them
- Business rules should be most independent and reusable code in the system


#### Screaming Architecture

- What does you architecture of your app scream?
- When you look at the top-level directory structure, and the source files in the highest-level package, do they screm `Health Care System` or `Accounting System` or `Inventory Management System`
- Or do they scream `Rails` or `Spring/Hibernate` or `ASP`

####### The Theme of an Architecture

- Software architectures are structures that support the use cases of the system
- Just as the plans for a house or a library scream about the use cases of those buildings, so should the architecture of a sofware application scream about the use cases of the application

- Architectures are not about frameworks
- Architectures should not be supplied by frameworks
- Frameworks are tools to be used, not architectures to be conformed to
- If architecture based on frameworks, then it cannot be based on your use cases

######## Purpose of an Architecture

- Good architectures are centered on use cases so that architects can safely describe the structures that support those use cases w/o committing to frameworks, tools, and environments
- Consider the plans for the house
- First concern of the architect is to make sure that the house is usable - not to ensrue that the house is made of bricks
- Architect takes pains to ensure that the homeowner can make decisions about the exterior material later, aft erht eplans ensure that the use cases are met

- Good architecture allows decisions about frameworks, databases, web servers, and other environmental issues and tools to be deferred and delayed
- `Frameworks are options to be left open`
- Good architecture makes it unnecessary to decide on Rails, or Spring, or Hibernate, or Tomcat, or MYSQL, until much later in the project
- Good architecture makes it easy to change mind about those decisions, too
- Good architecture emphasizes the use cases and decouples them from peripheral concerns

######## But What About The Web?

- Is web an architecture?
- Does fact that system is delievered on the web dictate the architecture of your system?
- No
- The web is a delivery mechanism - an IO device - and your application architecture should treat it as such
- The fact that app is delivered over web is a detail and should not dominate your system structure
- Decision that app will be delivered over the web is one that you should defer
- System architecture should be as ignorant as possible about how it will be delievered
- Should be able to deliver as console app, or web app, or thick client, or even a web service app, w/o undue complication or change to the fundamental architecture

######## Frameworks are Tools, Not Ways of Life

- Look at each framework with skepticism
- It might help but at what cost?
- Ask yourself how to use it, and how you should protect yourself from it
- Think about how you can preserve the use-case emphasis of your architecture
- Develop a strategy that prevents the framework from taking over architecture

######## Testable Architectures

- If system architecture is all about use cases, and if you have kept your frameworks at arm's legnth, then you should be able to unit-test all those use cases w/o any of the frameworks in place
- Shouldn't need web server running to run tests
- Shouldn't need database connected to run tests
- Entity objects should be plain objects that have no dependencies on frameworks
or database or other complications
- Use case objects should coordinate your Entity objects
- All of them together should be testable in situ, w/o any of the complications of frameworks

######## Conclusion

- Architecture should tell readers about the system, NOT about the frameworks you used in your system
- If building a health care system, then when programmers look at source repo, their first impression should be `oh this is a healthcare system`
- New programmers should be able to learn all the use cases of the system, yet still not know how the system is delivered
- They may come and say
```
We see some things that look like model - but where are the views and controllers
```

And you should respond
```
Oh, those are details that needn't concern as the the moment. We'll decide about them later
```

#### The Clean Architecture

- Over the last several decades, we've seen whole range of ideas regarding the architecture of systems

- Hexagonal Architecture (also known as Ports and Adapters)
- DCI
- BCE

- Although these architectures all vary somewhat in details, they are very similar
- All have the same objective, which is the separation of concerns
- All achieve this separation by dividing the software into layers
- Each has at least one layer for business rules, and another layer for user and system interaces

Each of these architectures have the following characteristics
- `Independent of Frameworks` - the architecture does not depend on the existence of some library of feature-laden software.
    * This allows you to use such frameworks as tools, rather than forcing you to cram your system into their limited constrains
- `Testable` - The business rules can be tested w/o the UI, database, webserver, or any other external element
- `Independent of the UI` - The UI can change easily, w/o changing the rest of the system.
    * A web UI could be replaced w/ a console app for example w/o changing business rules
- `Independent of Database` - Business rules are not bound to database
- `Independent of any external agency` - business rules don't know anyhting at all about the interfaces to the outside world

- Diagram in Figure 22.1, is attempt at integrating all these architectures into single actionable idea

![alt text](Images/Figure-22.1.png)

######## The Dependency Rule

- Concentric circles in Figure 22.1, represents different areas of software
- In general, the further you go in, the higher level the software becomes
- The outer circles are mechanisms
- Inner circles are policies

- Overriding rule that makes this archtitecture work is the `Dependency Rule`

`Source code dependencies must point only inward, toward higher-level policies`

- Nothing in the inner circle can know anything at all about something in an outer circle
- The name of something declared in an outer circle must not be mentioned by the code in an inner circle
- That includes functions, classes, variables, or any other named software entity

- By same token, data formats declared in outer circle should not be used by an inner circle, especially if those formats are generated by a framework in an outer circle
- `We don't want anything in an outer circle to impact the inner circle`

######## Entitites

- Entities encapsulate enterprise-wide Critical Business Rules

- An entity can be an object with methods, or it can be a set of data structures and functions
- It doesn't matter so long as the entitites can be used by many different apps within the enterprise

- If you don't have an enterprise and are writing just a single application, then these entities are the business objects of the application
- They encapsulate the most general and high-level rules
- They are the least likely to change when something external changes
- For example, would not expect these objects to be affected by a change to page navigation or security
- No operational change to any particular application should affect the entity layer

######## Use Cases

- Software in the use cases layer contain `application-specific` business rules
- Encapsulates and implements all of the use cases of the system
- These use cases orchestrate the flow of data to and from the entitites, and direct those entities to use their Critical Business Rules to achieve the goals of the use case

- Do not expect changes in this layer to affect entities
- Also do not expect this layer to be affected by changes to externalities such as the database, the UI, or any of the common frameworks
- Use cases layer is isolated from such concerns

- Do, however, expect that changes to the operation of the app will affect the use cases and, therefore, the software in this layer
- If details use use case change, then some code in this layer will certainly be affected

######## Interface Adapters

- Software in the interface adapters layer is a set of adapters that convert data from the most convenient for the use cases and entities, to the format most convenient for some external agency such as the databse or web
- It is this layer, fro example, that will wholly contain the MVC architecture of a GUI
- Presenters, views, and controllers all belong in the interface adapters layer
- Models are likely just data structures that are passed from the controllers to the use cases, and then back from the use cases to the presenters and views

- Similarly, data is converted, in this layer, from the form most convenient for entities and use cases, to the form most convenient for whatever persistence framework is being used (i.e., the databse)
- No code inward of this circle should know anything at all about the database
- If the database is a SQL database, then SQL should be restricted to this layer - and in particular to the parts of this layer that have to do with the database
- Also in this layer is any other adapter necessary to convert from some external form, such as an external service, to the internal form used by the use cases and entities

######## Frameworks and Drivers

- Outermost layer of the model in Figure 22.1 is generally composed of frameworks and tools such as the database and the web framework
- Generally don't write much code in this layer, other than glue code that communicates to the next circle inward

- Frameworks and drivers layer is where all the details go
- Web is a detail
- Database is a detail
- Keep these things on the outside where they can do little harm

######## Only Four Circles?

- Circles in Figure 22.1, are intended to be schematic
- You may find that you need more than just these four
- There's no rule that says you must have just these four
- However, Dependency Rule always applies
- Source code dependencies always point inward
- As you move inward, the level of abstraction and policy increases
- Outermost circle consists of low-level concrete detais
- As you move inward, the software grows more abstract and encapsulates higher-level policies
- Innermost circle is the most general and highest level

######## Crossing Boundaries

- At lower right of Figure 22.1 is an example of how we cross the circle boundaries
- Shows the controllers and presenters communicating with the use cases in the next layer
- Note the flow of control: it begins in the controller, moves through the sue case, and then winds up executing in the presenter
- Note source code dependencies: Each points inward toward the use cases

- We usually resolve this apparent contradiction by using the Dependency Inversion Principle
- In a language like Java, we would arrange interfaces and inheritance relationships such that the source code dependencies oppose the flow of control at just the right points across the boundary

- For example, suppose use case needs to call the presenters
- Call must not be direct b/c it would violate the `Dependency Rule`: `No name in the outer circle can be mentioned by the inner circle` (shown in Figure 22.1 as "use case output port") in the inner circle, and have the presenter in the outer circle implement it

- Same technique is used to cross all boundaries in the architecture
- Take advantage of dynamic polymorphism to create source code dependencies that oppose the flow of control so that we can confrom to the Dependency Rule, no matter which direction the flow of control travels

######## Which Data Crosses the Boundaries

- Typically the data that crosses the boundaries consists of simple data structures
- Can be basic structs or simple data transfer objects
- Data can simply be arguments in function calls
- Important thing is that isolated, simple data structures are passed across boundaries
- DO NOT WANT TO PASS ENTITY OBJECTS OR DATABASE ROWS
- DOn't want the data structures to have any kind of dependency that violates the Dependency Rule

- For example, database frameworks return a convenient data format in response to a query
- We might call this a `row structure`
- Don't want to pass that strucuter inward across a boundary
- Doing so would viollate the Dependency Rule b/c it would force an inner circle to know something about an outer circle

- This when we pass data across a boundary, it is always in the form that is most convenient for the inner circle

######## A Typical Scenario

- Diagram in Figure 22.2 shows typical scenario for web based Java system using a database
- Web server gathers input data from user and hands it to `Controller`
- `Controller` packages that data into plain old java object and passes this object through `InputBoundary` to the `UseCaseInteractor`
- `UseCaseInteractor` interprets data and uses it to control the dance of the `Entities`
- Also uses `DataAcessInterface` to bring the data used by those `Entities` into memory from the `Database`
- Upon completion, the `UseCaseInteractor` gathers data form the `Entities` and constructs the `OutputData` as another plain old Java object
- `OutputData` is then passed through the `OutputBoundary` interface to the `Presenter`

![alt text](Images/Figure-22.2.png)

- Job of the `Presenter` is to repackage the `OutputData` into viewable from as the `ViewModel` which is yet another plain Java object
- `ViewModel` contains mostly `Strings` and flags that the `View` uses to display the data
- Whereas the `OutputData` may contain `Date` objects, the `Presenter` will load the `ViewModel` with corresponding `Strings` already formatted properly for the user
- Same is true of `Currency` objects or any other business-related data
- `Button` and `MenuItem` names are placed in the `ViewModel`, as are the flags that tell the `View` whether those `Buttons` and `MenuItems` should be gray

- This leaves the `View` with almost nothing to do other than to move the data from the `ViewModel` into the `HTML` page
- Note the direction of the dependencies
- All dependencies cross the boundary lines pointing inward, following the `Dependency Rule`

######## Conclusion

- Conforming to these simple rules is not difficult
- It will save lot of headaches moving forward
- By separating software into layers and conforming to Dependency Rule, you will create a system that is intrinsically testable with all the benefits that implies
- When any of the external parts of the system become obselete, such as database, or web framework, you can replace those obsolete elements w/ a minimum of fuss


#### Presenters and Humble Objects

- Presenters are a form of the `Humble Object` pattern, which helps us identify and protect architectural boundaries

######## Humble Object Pattern

- A design pattern that was originally identified to help unit testers to separate the behaviors that are hard to test from the behaviors that are easy to test
- Split the behaviors into two modules or classes
    - One of those modules is humble; it contains all the hard-to-test behaviors stripped down to their barest essence
    - Other module contains all the testable behaviors that were stripped out of the humble object

- For example, GUIs are hard to unit test b/c it is very difficult to write tests that can see the screen and check that appropriate elements are displayed there
- However, most of behavior of GUI is, in fact, easy to test
- Using `Humble Object` pattern, we can separate these two kidns of behaviors into two different classes called the `Presenter` and the `View`

######## Presenters and Views

- The `View` is the humble object that is hard to test
- The code in this object is kept as simple as possible
- Moves data into the GUI but does not process that data

- The `Presenter` is the testable object
- Its job is to accept data from the application and format it for presentation so that the View can simply move it to the screen
- For example, if the application wants a date displayed in a field, it will hand the Presenter a `Date` object
- Presenter will then format that data into an appropriate string and place it in a simple data structure called the View Model, where the View can find it

- If the application wants to display the money on the screen, it might pass a `Currency` object to the `Presenter`
- The Presenter will then format that object with the appropriate decimal places and currency markers, creating a string that it can place in the View Model
- If that currency value should be turned red if it is negative, then a simple boolean flag in the View model will be set appropriately

- Every button on the screen will have a name
- That name will be a string in the View Model, placed there by the presenter
- If those buttons should be grayed out, the Presenter will set an appropriate boolean flag in the View model
- Every menu item name is a string in the View model, loaded by the Presenter
- The names for every radio button, check box, and text field are loaded, by the Presenter, into appropriate strings and booleans in the View model

- Anything and everything that appears on the screen and that application has some kind of control over, is represented in the View Model as a string, or a boolean, or an enum
- Nothing is left for the View to do other than to load the data from the View Model into the screen
- Thus the View is humble

######## Testing and Architecture

- Testability is an attribute of good architectures
- Humble Object pattern is a good example
- B/c the separation of the behaviors is testable and non-testable parts often defines an architectural boundary
- Presenter/View boundary is one of these boundaries, but there are many others

######## Database Gateways

- Btwn the use case interactos and the database are the database gateways
- These gateways are polymorphic interfaces that contain methhods for every create, read, update, or delete operation that can be performed by the application on the database
- For ex, if the app needs to know the last names of all the users who logged in yesterday, then the `UserGateway` interface will have a method named `getLastNamesOfUsersWhoLoggedInAfter` that takes a `Date` argument as its argument and returns a list of last names

- Recall that we do not allow SQL in the use cases layer; instead, we use gateway interfaces that have appropriate methods
- Those gateways are implemented by classes in the database layer
- That implementation is the humble object
- It simply uses SQL, or whatever the interface of the database is, to access the data required by each of the methods
- The interactors, in contrast, are not numble b/c they encapsulate application-specific business rules
- Althought they are not humble, those interactors are testable, b/c the gateways can be repalced w/ appropriate stubs or test-doubles

######## Data Mappers

- In which layers do you think ORMs like Hibernate belong?

- Author says there is no such thing as an object relational mapper (ORM)
- Reason is simple: objects are not data structures
- At least, they are not data structures from the users' point of view
- The users of the objet cannot see the data, since it is all private
- So from user's point of view, an object is simply a set of operations

- A data struture, in contrast, is a set of public variables that have no implied behavior
- ORMs would be better named `data mappers` b/c they load data into data structures from relational database talbes

- ORMs should reside in the database layer
- ORMs are another kind of `Humble Object` boundary btwn gateway interfaces and the database

######## Service Listeners

- What about services?
- If app must communicate w/ other services, or if your app provides a set of services, will we find `Humble Object` pattern creating a service boundary?

- Yes, the app will load data into simple data structures and then pass those structures across the boundary to moduels that properly format the data and send it to external services
- On the input side, the service listeners will receive data from the service inteface and format it into a simple data structure that can be used by the application

######## Conclusion

- At each architectural boundary, we are likely to find `Humble Object` pattern lurking somewhere nearby
- Communcation across that boundary will almost always invovle some kind of simple data structure, and the boundary will frequently divide something that is hard to test from something that is easy to test
- Use of this pattern at architectural boundaries vastly increases the testabiliy of the entire system


#### Partial Boundaries
 - Full-fledged architectural boundaries are expensive
 - They require reciprocal polymorphic `Boundary` interfaces, `Input` and `Output` data structures, and all of the dependency management necessary to isolate the two sides into independently compilable and deployable units
 - Lot of work

 - Good architect might judge that the expense of such a boundary is too high - but might want to hold a place for such a boundary in case it is needed later

 - This kind of anticipatory design of often frowned upon by many in Agile community as a violation of `YAGNI` or `You Aren't Going to Need It`
 - Architects may sometimes look at a problem and think `Yeah but I might`
 - In this case, they implemement a partial boundary

 ######## Skip The Last Step

 - One way to construct a partial boundary is to do all the work necessary to create independently compilable and deployable components, and then simply keep them together in the same component
 - The reciporcal interfaces are there, the input/output data structures are there, and everything is set up - but we compile and deploy all of them as a single component

 - This kind of partial boundary requires same amount of code and preparatory design work as a full boundary
 - Does not require administration of multiple components
 - No version number tracking or release management burden
 
 ######## One-Dimensional Boundaries

 - Full-fledged architectural boundaries uses reciprocal boundary interfaces to maintain isolation in both directions
 - Maintaining this separation is expensive both in initial setup and ongoing maintenance

 - Simpler structure that serves to hold the place for later extension to a full-fledged boundary is shown in Figure 24.1
 - Exemplifies the `Strategy` pattern
 - A `ServiceBoundary` interface is used by clients and implemented by `ServiceImpl` classes

 ![alt text](Images/Figure-24.1.png)

 - Should be clear that this sets the stage for a future architectural boundary
 - This necessary dependency inversion is in place in an attempt to isolate the `Client` from the `ServiceImpl`
 - Should be clear that the separation can degrade pretty rapidly as shown by the nasty dotted arrow in the diagram
 - W/o repirocal interfaces, nothing prevents this kind of backchannel other than the diligence and discipline of the developers and architects

 ######## Facades

 - Even simpler boundary is the `Facade` battern (Figure 24.2)
 - In this case, even dependency inversion is sacrificed
 - Boundary is simply defined by the `Facade` class which lists all the services as methods, and deploys the service calls that classes that the client is not supposed to access

 ![alt text](Images/Figure-24.2.png)

- Note, that the `Client` has the transitive dependency on all those service classes
- In static languages, a change to the source code in one of the `Service` classes will force the `Client` to recompile

######## Conclusion

- Seen 3 simple ways to partially implement an architectural boundary
- Many other approaches

- Each approach has its own set of costs and benefits
- Each is appropriate, in certain contexts, as a placeholder for an eventual full-fledged boundary
- Each can also be degraded if that boundary never materializes

- One of the functions of an architect to decide where an architectural boundary might one day exist, and whether to fully or partially implement that boundary


#### Layers and Boundaries

- Easy to think of systems as being composed of 3 components: UI, business rules, and database
- For simple systems, this is sufficient
- For most systems, though, the number of components is larger than that

- Consider simple computer game
- Easy to imagine 3 components
- UI handles all messages from the players to the game rules
- Game rules store the state of the game in some kind of persistent data strucuture
- Is that all there is?

######## Hunt the Wumpus

- Making Hunt the Wumpus game
- Text-based game that uses very simple commands like `GO EAST` and `SHOOT WEST`
- Player enters a command, and the computer responds to what the player sees, hears, smells, and experiences

- Keep text-based UI
- Decouple it from the game rules so version can use different languages in diferent markets
- Game rules will communicate with the UI component using a language-independent API, and the UI will translate the API into the appropriate human language
- If source code dependencies are properly managed (like in Figure 25.1), then any number of UI components can reuse the same game rules
- Game rules do not know, nor do they care, which human language is used 

 ![alt text](Images/Figure-25.1.png)

 - Assume that state of game is manipulated on some persistent storage (flash, cloud, or just RAM)
 - In any of those cases, we don't want game rules to know details
 - Create API that the game rules can use to communicate w/ the data storage component

 - Don't want game rules to know anything about different kinds of data storage, so the dependencies have to be properly directed following the dependency rule as show in Figure 25.2

  ![alt text](Images/Figure-25.2.png)

 ######## Clean Architecture?

 - Could easily apply clean architecture approach in this text, with all the use cases, boundaries, entities, and corresponding data structures
 - But have we really found all significant architectural boundaries?

 - For ex, language is not the only axis of change for the UI
 - Might also want to vary the mechanism by which we communicate the text
 - For example, we might want touse the normal shell window, or text messages, or a chat application (many different possibilities)

 - That means that there is a potential architectural boundary defined by this axis
 - Perhaps we should construct an API that crosses that boundary and isolates the language from the communications mechanism; that idea is illustrated in Figure 25.3

  ![alt text](Images/Figure-25.3.png)

 - Dashed outlines indicate abstract components that define an API that is implemented by the components above or below them
- For example, the `Language` API is implemented in both `English` and `Spanish`

- `GameRules` communicates with `Language` through an API that `GameRules` defines and `Language` implements
- `Language` communicates with `TextDelivery` using an API that `Language` defines but `TextDelivery` implements
- The API is defined and owned by the the user, rather than by the implementer

- If we were to look inside `GameRules1 we would find the polymorphic `Boundary` interfaces used by the code inside `GameRules` and implemented by the code inside the `Language` component
- Would also find polymorphic `Boundary` interfaces used by `Language` and implemented by code inside `GameRules`

- If we were to look inside of `Language`, we would find the same thing
- Polymorphic `Boundary` interfaces implemented by the code inside `TextDelivery`, and polymorphic `Boundary` interfaces used by the `TextDelivery` and implemented by `Language`

- In each case, the API defined by those `Boundary` interfaces are owned by the upstream component

- The variations, such as `English`, `SMS`, and `CloudData`, are provided by polymorphic interfaces defined in the abstract API component, and implemented by the concrete components that serve them
- For example, we would expct polymorphic interfaces defined in `Languages` to be implemented by `English` and `Spanish`

- Can simply this diagram by eliminating all the variations and focusing on just the API components (Figure 25.4)

![alt text](Images/Figure-25.4.png)

- Notice that all diagrams are oriented so that all arrows point up
- This puts `GameRules` at the top
- This orientation makes sense b/c `GameRules` is the component that contains the highest-level policies

- Consider direction of information flow
- All input comes from the user throught the `TextDelivery` component at the bottom left
- That information rises through the `Language` component, getting translated into commands to `GameRules`
- `GameRules` processes the user input and sends appropriate data down to `DataStorage` at the lower right

- `GameRules` then sends output back down to `Language`, which translates the API back to the appropriate language then delivers the language to the user through `TextDelivery`

- This organization effectively divides the flow of data into two streams
- The stream on the left is concerned with communicating with the user
- Stream on the right is concerned with data persistence
- Both streams meet at the top at `GameRules`, which is the ultimate processor of data that goes through both streams

######## Crossing The Streams

- Are there always two data streams as in this example
- No
- Imagine that we would like to play Hunt The Wumpus on the net with multiple players
- In this case, we would need a network component (like in 25.5)
- This organization divides the data flow into 3 streams, all controlled by the `GameRules`

![alt text](Images/Figure-25.5.png)

- A sthe system becomes more complex, the component may split into such streams

######## Splitting The Streams

- At this point you may be thinking that all streams eventually meet at the top of the component
- Can be much more complex than this

- Consider the `GameRules` component for Hunt the Wumpus
- Part of the games rules deal with the mechanics of the map
- They know how the caverns are connected, and which objects are located in each cavern
- They know how to move player from cavern to cavern, and how to determine the events that the player must deal with

- But there is another set of policies at an even higher level - policies that know the health of the player, and the cost or benefit of a particular event
- These policies could cause the player to gradually lose halth, or to gain health by discovering food
- The lower-level mechanics policy would declare events to this higher-level policy, such as `FoundFood` or `FellInPit`
- Higher-level policy would then manage the state of the player (Figure 25.6)
- Eventually the policy would decide whether the player wins or loses

![alt text](Images/Figure-25.6.png)

- Is this an architectural boundary?
- Do we need an API that separates `MoveManagement` from `PlayerManagement`
- Let's add microservices

- Assume we've got a massive multiplayer version of Hunt the Wumpus
- `MoveManagement` is handled locally within the player's computer
- `PlayerManagement` is handled by the server
- `PlayerManagement` offers a microservice API to all the connected `MoveManagement` components

- Diagram in Figure 25.7 depicts this scenario
- `Network` elements are a bit more complex than depicted
- A full-fledged architectural boundary exists btwn `MoveManagement` and `PlayerManagement` in this case

######## Conclusion

- What does this mean?
- Why take absurdbly simple program, which could be implemented in 200 lines of Kornshell, and extrapolated it out with all these crazy architectural boundaries

- Example is intedned to show that architectural boundaries everywhere
- Architects must be careful to recognize when they are needed
- Also have to be aware that such boundaries, when fully implemented, are expensive
- At same time, we have to recognize that when such boundaries are ignored, they are expensive to add later - even in presence of comprehensive test-suites and refactoring discipline

- So what to do?
- You must `see` the future
- Must guess - intelligently
- Weigh costs and determine where the architectural boundaries lie, and which should be fully implemented, and which should be partially implemented, and which should be ignored

- Not a 1 time decision
- Don't decide at start of a project which boundaries to implement and which to ignore
- You `watch`
- Pay attention as the system evolves
- You note where boundaries may be required, but then carefully watch for the first inkling of friction b/c those boundaries don't exist

- At that point, you weigh the cost of implementing those boundaries versus the cost of ignornign them - and you review that decision frequently
- Goal is to implement the boundaries right at the inflection point where the cost of implementing becomes less than the cost of ignoring


#### The Main Component

- In every ssytem, there is at least 1 component that creates, coordinates, and oversees the other
- Author calls this `main`

######## The Ultimate Detail

- `Main` component is the ultimate detail - the lowest-level policy
- Initial entry point of the system
- Nothing, other than the OS, depends on it
- Its job is to create all the Factories, Strategies, and other global facilities and then hand control over to the high-level abstract portions of the system

- It is in the `Main` component that dependencies should be injected
- Once they are injected into `Main`, `Main` should distrubte those dependencies normally w/o using the framework

- Think of `Main` as the dirtiest of all the dirty components

- Consider the following `Main` component from a recent version of Hunt the Wumpus
- Notice how it loads up all the strings that we don't want all the main body of the code to know about

```
public class Main implements HtwMessageReceiver {
  private static HuntTheWumpus game;
  private static int hitPoints = 10;
  private static final List<String> caverns = new   ArrayList<>();
  private static final String[] environments = new String[]{
    “bright",
    "humid",
    "dry",
    "creepy",
    "ugly",
    "foggy",
    "hot",
    "cold",
    "drafty",
    "dreadful"
  };
 
  private static final String[] shapes = new String[] {
    "round",
    "square",
    "oval",
    "irregular",
    "long",
    "craggy",
    "rough",
    "tall",
    "narrow"
  };
 
  private static final String[] cavernTypes = new String[] {
    "cavern",
    "room",
    "chamber",
    "catacomb",
    "crevasse",
    "cell",
    "tunnel",
    "passageway",
    "hall",
    "expanse"
  };

private static final String[] adornments = new String[] {
   "smelling of sulfur",
    "with engravings on the walls",
    "with a bumpy floor",
    "",
    "littered with garbage",
    "spattered with guano",
    "with piles of Wumpus droppings",
    "with bones scattered around",
    "with a corpse on the floor",
    "that seems to vibrate",
    "that feels stuffy",
    "that fills you with dread"
  };

```

- Now here's the `main` function
- Notice how it uses the `HtwFactory` to create the game
- It passes in the name of the class, `htw.game.HuntTheWumpusFacade`, b/c that class is even dirtier than `Main`
- This prevents changes in that class from cause `Main` to recompile/redeploy

Page 356 is expert for code being talked about
- Notice also that `main` creates the input stream and contains the main loop of the game, interpreting the simple input commands, but then defers all processing to other, higher-level component

Page 357 code being talked about
- Notice that `main` creates the map

- The point is that `Main` is a dirty low-level module in the outermost circle of the clean architecture
- Loads everything up for the high level system, and then hands control over it

######## Conclusion

- Think of `Main` as a plugin to the app - a plugin that sets up the initial conditions and configurations, gathers all the outside resources, and then hands control over to the high-level policy of the app
- Sinc eit is a plugin, it is possible have many `Main` components, one for each configuration of your application

- For example, could have a `Main` plugin for `Dev`, another for `Test`, and yet another for `Production`
- Could also have a `Main` plugin for each country you deploy to, or each jurisdiction, or each customer

- When you think about `Main` as a plugin component, sitting behind an architectural boundary, the problem of configuration becomes a lot easier to solve


#### Services: Great and Small

- Service-oriented architectures and microservice architectures have become very popular
- Reasons for popularity
    - Services seem to be strongly decoupled from each other. This is only actually partially true
    - Services appear to support independence of development and deployment. Again, this is only partially true

######## Service Architecture?

- Consider notion that using services, by their nature, is an architecture
- Author says this is untrue
- Architecture of a system is defined by boundaries that separate high-level policy from low-level deatil and follow the Dependency Rule
- Servies that simply separate application behavior are little more than expensive function calls, and are not necessarily architecturally significant

- This is not to say that all services `should` be architecturally significant
- There are often substantial benefits to creating services that separate fucntionality across processes and platforms - whether they obey the Dependency Rule or not
- It's just that services, in and of themselves, do not define architecture

- A helpful analogy is the oranization of functions
- The architecture of a monolithic or component-based system is defined by certain function calls that cross architectural boundaries and follow the Dependency Rule
- Many functions in those systems, however, simply separate one behavior from another and are not architecturally significant

- Same w/ services
- Services are just function calls across process and/or platform boundaries
- Some are architecturally significant, and some aren't

######## Service Benefits?

########## The Decoupling Fallacy

- One of big supposed benefits of breaking a system up into services is that services are strongly decoupled from each other
- After all, each service runs in a different process, or even a different processor; therefore, those services do not have access to each other's variables
- The interface of each service must be well defined

- Some truth to this - but not very much
- Yes, services are decoupled at the level of individual variables
- However, they can still be coupled by shared resources within a processor, or on the network
- They are strongly coupled by the data they share

- For example, if a new field is added to a data record that is passed btwn services, then every service that operates on a new field must be changed
- Services must also strongly agree about the interpretation of the data in the field
- Thus those services are strongly coupled to the data record and, therefore, indirectly coupled ot each other

- As for interfaces being well defined, that's certainly true - but it is no less true for functions
- Service interfaces are no more formal, no more rigorous, and no better defined than function interfaces
- Benefit is something of an illusion

########## The Fallacy of Independent Development and Deployment

- Another supposed benefit of services is that they can be owned and operated by a dedicated team
- Team can be responsible for writing, maintaining, and operating the service as part of a dev-ops strategy
- This independence of development and deployment is presumed to be scalable
- It is believed that large enterprise systems can be created from dozens, hundreds, or even thousands of independently developable and deployable services
- Development, maintenance, and operation of the system can be partitioned btwn a similar number of independent teams

- Some truth to this
- History has shown that large enterprise systems can be built from monoliths and component-based systems as well as service-based systems
- Thus services are not the only option for building scalable systems

- Second, the decoupling fallacy means that services cannot always be independently developed, deployed, and operated
- To the extent that they are coupled by data or behavior, the development, deployment, and operation must be coordinated

######## The Kitty Problem

- As an example of these 2 fallacies, let's look at our taxi system aggregator system again
- System knows about many taxi providers in a given city, and allows customers to order rides
- Assume that the customers select taxis based on criteria such as pickup time, cost, luxury, and driver experience

- Wanted our sytem to be scalable, so we chose to build it out of lots of little microservices
- Subdivided our development staff into small teams, each of which is responsible for developing, maintaining, and operating a correspondingly small number of services

- Diagram in Figure 27.1 shows how our ficticious architecture arranged services to implement this app
- The `TaxiUI` service deals w/ the customers, who use mobile devices to order taxis
- The `TaxFinder` service examines inventories of the various `TaxiSuppliers` and determines which taxis are possible candidates for the user
- It deposits these into a short-term data record attached to that user
- The `TaxiSelector` service takes the user's criteria of cost, time, luxury, and so forth, and chooses an appropriate taxi from among the candidates
- Hands that taxi off to the `TaxiDispatcher` service, which orders the appropriate taxi

![alt text](Images/Figure-27.1.png)

- Suppose that this system has been in operation for more than a year
- Staff of devs have been developing new features while maintaining and operating new systems

- Company announce plans to offer a kitten delivery service to the city
- Users can order kittens to be delivered to their homes or to place of business

- Company will set up several kitten collection points across city
- When kitten order is placed, a nearby taxi will be selected to a collection from one of the collection points, and then deliver it to the appropriate address

- One of taxi suppliers has agreed to participate, others are likely to follow, others may decline

- Some drivers may be allergic to cats, so those drivers should never be selected for this service
- Some customers will have similar allergies, so a vehicle that has been used to deliver kittens within the last 3 days should not be selected for customers who declare such allergies

- Look at diagram of services
- How many will have to change to implement this feature? `All of them`
- Clearly, the development and deployment of the kitty feature will have to be carefully coordinated

- In other words, services are coupled, and cannot be independently developed, deployed, or maintained

- This is the problem with cross-cutting concerns
- Every software system must face this problem, whether service oriented or not
- Functional decompositions, of the kind featured in Figure 27.1, are very vulnerableå to new features that cut across all those functional behaviors

######## Objects To The Rescue

- How would we have solved this problem in a component-based architecture
- Careful consideration of the SOLID design principles would have prompted us to create a set of classes that could be polymorphically extended to handle new features
- Diagram in 27.2 shows the strategy
- Classes in this diagram roughly correspond to the services shown in Figure 27.1
- Note the boundaries
- Note also that the dependencies follow the Dependency Rule

- Much of the logic of the original services is perserved within the base classes of the object model
- However, that portion of the logic that was specific to rides has been extracted into the `Rides` component
- These two components override the abstract base classes in the original components using a pattern such as `Template Method` or `Strategy`

- Note again that the two new components, `Rides` and `Kittens`, follow the Dependency Rule
- Note also that the classes that implement those features are created by factories under the control of the UI

- In this Scheme, when the Kitty feature is implemented, the `TaxiUI` must change
- But nothing else needs ot be changed
- Rather a new jar, or Gem, or DLL is added to the system and dynamically loaded at run time

- Thus, the Kitty feature is decoupled, and independently developable and deployable

![alt text](Images/Figure-27.2.png)

######## Component-Based Services

- Obvious question is: Can we do that for services?
- Yes
- Services do not need to be little monoliths
- Services, can be designed using SOLID principles, and given a component structure so that new components can be added to them w/o changing existing components within the service

- Adding new features conforms to the Open-Closed Princple

- Service diagnram in Figure 27.3 shows the structure
- The services still exist as before, but each has its own internal component design, allowing new features to be added as new derivative classes
- Those derivative classes live within their own components

![alt text](Images/Figure-27.3.png)

######## Cross-Cutting Concerns

- What we learned is that architectural boundaries do not fall between sevices
- Those boundaries run through serivces, dividng them into components

- To deal w/ cross-cutting concerns that all significant systems face, services must be designed w/ internal component architectures that folloe the Dependency Rule, as show in Figure 27.4
- Those services do not define the architectural boundaries of the system; instead, the components within the services do

![alt text](Images/Figure-27.4.png)

######## Conclusion

- As useful as services are to the scalability and the developability of a system, they are not, in and of themselves, architecturally significant elements
- The architecture of a system is defined by boundaries drawn within that system, and by the dependencies that cross those boundaries
- That architecture is not defined by the physical mechanisms by which elements communicate and execute

- A service might be a single component, completely surrounded by an architectural boundary
- Alternatively, a service might be composed of several components separated by architectural boudnaries
- In rare cases, clients and services may be so coupled as to have no architectural significance at all


#### The Test Boundary

- `The Tests are part of the system` and they participate in the architecture just like every other part of the system does
- In some ways, that participation is pretty normal
- In other ways it can be unique

######## Tests As System Components

- Great deal of confusion around tests
- Are they part of the system?
- Are they separate from the system?
- Which kind of tests are there?
- Are unit tests and integration tests different things?

- From architectural standpoint, all tests are the same

- Tests, by nature, follow Dependency Rule
- Very detailed and concrete
- Always depend inward toward the code being tested
- You can think of tests as the outermost circle in the architecture
- Nothing within the system depends on the tests, and the test always depend inward on the components of the system

- Test are also independenty deployable
- Most of the time they are deployed in test systems, rather than in production systems
- So even in systems where independently deployment is not otherwise necessary, the tests will still be independently deployed

- Tests are most isolated system component
- They are not necessary for system operation
- No user depends on them
- Their role is to support development, not operation
- And yet, they are no less a system component than any other
- In fact, in many ways they represent the model that all other system components should follow

######## Design For Testability

- Extreme isolation of tests, combined w/ fact that they are not usually deployed, often causes devs to think that tests fall outside of the design of the system
- Test that are not will integrated into the design of the system tend to be fragile, and they make the system rigid and difficult to change

- The issue is coupling
- Tests that are strongly coupled to the system must change along w/ the system
- Even most trivial change to a system component can cause many coupled tests to break or require change

- Changes to common system components can cause hundreds, or even thousands, of tests to break
- This is known as the `Fragile Tests Problem`

- Not hard to see how this can happen
- Imagine a suite of tests that can use the GUI to verify business rules
- Such tests may start on the login screen and then navigate through the page structure until they can check particular business rules
- Any change to the login page, or the navigation structure, can cause an enormous number of tests to break

- Fragile tests often have the perverse effect of making the system rigid
- When devs realize that simple changes to the system can cause massive test failures, they may resist making those changes

- Solution is to design for testability
- First rule of software design - whether for testability or for any other or for any other reason - is always the same: `Don't depend on volatile things`
- GUIs are volatile
- Test suites that operate the system through the GUI `must be fragile`
- Design the systems, and the tests, so that business rules can be testted w/o using the GUI

######## The Testing API

- The way to accomplish the goal is create a specific API that the tests can use to verify all the business rules
- This API should have super powers that allow the tests to avoid security constraints, bypass expensive resources (such as databases), and force the system into particular testable states
- This API will be a superset of the suite of `interactors` and `interface adapters` that are used by the user interface

- Purpose of the testing API is to decouple the tests from the app
- This decouplign encompasses more than just detaching the tests from the UI: goal is decouple the structure of the tetst form the structure of the app

########## Structural coupling

- Structural coupling is one of the strongest, and most insidious, forms of test coupling
- Imagine a test suite that has a test class for every prod class, and a set of test methods for every prod method
- Such a test sutie is deeply coupled to the structure of the app

- When one of those prod methods or classes changes, a large number of tests much change as well
- Consequently, the tests are fragile, and they make the prod code rigid

- The role of the testing API is to hide the structure of the app from the tests
- This allows prod code to be refactored and evolved in ways that don't affect the tests
- Also allows tests to be refactored and evolved in ways that don't affect the production code

- Separation of evolution is necessary b/c as time passes, the tests end to become increasingly more concrete and specific

- In contrast, the prod code tends to become increasingly more abstract and general
- Strong structural coupling prevents - or at least impedes - this necessary evolution, and prevents the prod code from being as general, and flexible, as it could be

########## Security

- Superpowers of testing API could be dangerous if they were deployed in the prod systems
- If this is a concern, then the testing API, and the dangerous parts of its implementation, should be kept separate, independently deployable component

######## Conclusion

- Tests are not outside the system; rather, they are parts of the system that must be well designed if they are to provide the desired benefits of stability and regression
- Tests that are not designed as part of the sysytem tend to be fragile and difficult to maintain
- Such tests often wind up on the maintenance room floor - discarded b/c they are too difficult to maintain


#### Clean Embedded Architecture 


#### The Database Is A Detail

- From architectural point of view, the database is a nonentity - it is a detail that does not rise to the level of architectural element
- Its relationship to the architecture of a software system is rather like the relationship of a doorknob to the architecture of your home

- Not talking about the data model
- The structure you goive to your data within your app is highly significant to the architecture of your system
- But database is not the data model
- Database is a piece of software
- Database is a utility that provides access to the data
- From architecture's point of view, that utility is irrelevant b/c it's a low-level detail -  mechanism
- A good architect does not allow low-level mechanisms to pollute the system architecture

######## Relational Databases

- Just a technology and that means it's a detail
- While relational databases may be convenient for certain forms of data access, there is nothing architecturally significant about arranging data into rows within tables
- The use cases of your app should neither now nor care about such matters
- Knowledge of the tabular structure of the data should be restricted to the lowest-level utility functions in the outer circles of the architecture

- Many data access frameworks allow database rows and tables to be passed around the system as objects
- Allows this is an architetural error
- It couples the use cases, business rules, and some cases even the UI to the relational structure of the data

######## Why Are Database Systems So Prevalent 

- Why are software systems and software enterprises dominated by database systems?
- Disks

- Rotating magnetic disk was mainstay of data storage for 5 decades
- Several generations of programmers have known no other form of data storage
- One fatal trait of disk technology: `Disks are slow`

- Reading data can take milliseconds of time
- May not seem slike a lot, but a millisecond is a million times longer than the cycle time of most processors
- If that data was not on a disk, it could be accessed in nanoseconds

- To mitigate the time deplay imposed by disks, you need indexes, caches, and optimized query schemas; and you need some kidn of regular means of representing data so that these indexes, caches, and query schemes know what they are working with
- You need a data access and management system
- Over the years these systems have split into two distinct kinds: file systems and RDBMS

- File systems are document based
- Provide a natural and convenient way to store whole documents
- Work well when you need to save and retrieve a set of documents by name, but they don't offer a lot of help when you're searching the content of those documents

- Database systems are content based
- Provide a natural and convenient way to find records based on their content
- Very good at associating multiple records based on some bit of content that they all share
- Unfortunately, they are rather poor at storing and retrieving opaque documents

- Both of these systems organize the data on disk so that it can be stored and retrieved in as efficient a way as possible, given their particular access needs 
- Each has their own scheem for indexing and arranging the data
- Each eventually brings the relevant data in to RAM, where it can be quickly manipulated

######## What If There Were No Disk?

- As prevalent as disks once were, they are now a dying breed
- They are being replaced by RAM

- When disks are gone, and all data is stored in RAM, how will you organize data?
- You won't organize it into tables and access it with SQL
- You won't organize it into files and access it through a directory

- You'll organize it into linked lists, trees, hash tables, stacks, queues, or any other myriad data structures, and you'll access it using pointers or references - b/c that's what programmers do

- If you think about it, you'll realize that you do this anyway
- Even though data is kept in database or file system, you read it into RAM and then reorganize it into lists, sets, stacks, queues, trees, or whatever data structure you want
- Very unlikely that you leave the data in the form of files or tables

######## Details

- Reality is why I say that the database is a detail
- Just a mechanism we use to move the data back and forth btwn the surface of the disk and the RAM
- Database is nothing more than big bucket of bits where we store our data on a long-term basis
- We seldom use the data in that form

- We should not care about the form that the data takes while it is on the surface of a rotating magnetic disk
- Should not acknowledge that the disk exists at all

######## But What About Performance

- Isn't performance an architectural concern?
- Yes, but when it comes to data storage, it's a concern that can be entirely encapsulated and separated from the business rules
- We need to get data in and out of data store quickly, but that's a low-level concern
- Can address that concern w/ low-level data access mechanisms
- Has nothing to do whatsoever w/ overall architecture of our systems

######## Anecdote

Page 415

######## Conclusion

- Organizational structure of data, the data model, is architecturally significant
- Technologies and systems that mvoe data on and off a rotating magnetic surface are not
- Relational database systems that force the datat to be organized into tables and access w/ SQL have much more to do w/ the latter than w/ the former
- Data is significant
- Database is a detail


#### The Web Is A Detail

- Web is just the latest in a series oscillations that our industry has gone through since the 1960s
- These oscillations move back and forth btw putting all the computing power in central servers and putting all compter power out at the terminals

- We've seen several of these oscillations just in the last decade or so since the web became prominent
- At first we thought all computter power would be in server farmers, and the browsers would be stupid
- Then we started putting applets in the browser
- But we didn't like that, so we moved dynamic content back to the servers
- But then we didn't like that, so we invented Web 2.0 and moved lots of procesing back into the browser w/ Ajax and Javascript
- Created whole huge apps written in the browers

######## The Endless Pendulum

- This didn't start just with the web
- Before the web, there was client-server architecture
- Before that, there were central minicomputers w/ arrays of dumb terminals
- Before that, there were mainframes w/ smart green-screen terminals

- Can't seem to figure out where we want computer power
- When you look at it in the overall scope of IT history, the web didn't change anything
- Just another oscillation

- As architects though, we have to look at long term
- Oscillations are short-term issues that we want to push away from central core of our business rules

- Decouple business rules from UI

######## The Upshot

- Upshot is simply this: The GUI is a detail
- Web is a GUI
- So Web is a detail
- An architects wants to put details like that behind boundaries tha tkeep them separate from core business logic

- `The Web is an IO device`