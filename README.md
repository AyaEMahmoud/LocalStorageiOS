# Local Storage in iOS

## User Defaults
#### What is user defaults?
User defaults is a built-in ***Data Dictionary*** that stores data for as long as the app is installed.

User defaults can store integers, booleans, strings, arrays, dictionaries, dates, and more.

#### How is it used?
```swift
let defaults = UserDefaults.standard
defaults.set(32, forKey: "Age")
defaults.set(true, forKey: "CanSentNotification")
defaults.set(Date(), forKey: "Birthday")
defaults.set("User", forKey: "userName")
```

```swift
let array = ["local", "storage"]
defaults.set(array, forKey: "Array")

let dict = ["name": "Simon", "country": "UK"]
defaults.set(dict, forKey: "Dict")
```
#### How to read stored data?
```swift
let age = defaults.integer(forKey: "Age")
let canNotify = defaults.bool(forKey: "CanSentNotification")
let date = defaults.string(forKey: "userName")
```
When retrieving objects the result is **optional**, so we need to ***type-cast*** to a non-optional type and use a ***nil coalescing*** operator to handle missing values.

```swift
let savedArray = defaults.object(forKey: "Array") as? [String] ?? [String]()
```








# How to store data on an iOS device using the core data framework.
#### What is core data?
Core Data is an extremely powerful ***framework*** that abstracts much of the details of how and where the data is actually stored, allowing the developer to focus on what you need to save.

You can use the framework to: 
- Store Data permanently for offline use.
- Cache temporary data.
- Functionality like undos and redos.

While the how of the framework works the same under the hood, it uses a different format to achieve that in **SwiftUI** Vs in **UIKit**.

## A High-level Overview of core data's parts
Core data mainly consists of an **Object Graph Mapper (OGM)** and a **Persistent Store**.

- The object graph represents the different objects in the model layer and their relationship to one another and the rules of these relationships.

![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.20.23%20AM.png)


Once you have instances of these models, you'll need to save, update, or delete them. 

- The persistence framework provided by core data handles all of these operations.

![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.29.15%20AM.png)


Each of the operations needed is generally delegated to very specific objects. All of these objects are referred to as the **Core Data Stack**. 

#### Manged Object Model
With core data we don't use swift types to model the data instead we use a **Managed Object**
- A managed object looks and feels like a swift class, but it's more of a representation of the data in the persistence store.
- Instead of being a specific type a managed object is a subclass of **NSManagedObject** and it's more of a generic container to store data in.
- Instead of creating objects directly in code, core data provides a visual interface to define the types that you'll use to represent your data. These types are called **Entities**.

![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.46.33%20AM.png)
![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.54.50%20AM.png)

#### Managed Object Context
As you work with your app you'll need a way to keep track of all changes, this is where the next part of the stack comes in.
- The managed object context is an intelligent scratchpad, keeps track of all changes occurring in the object graph. 
- The tracked changes are not persisted until saved.
- Managed object must be registered with a managed object context.
- The context also tracks changes in relationships between all managed objects.
- By tracking these changes the context is able to provide undos and redos automatically.
- The managed object context is an instance of the **NSMAnagedObjectContext**

***Context -> Recored of Changes -> Presistence Layer***

- Also there can be more than one context in any app.


![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.59.10%20AM.png)


#### The Persistent Store Coordinator
Sets in the middle of the stack between the object graph and the persistence store and facilitate communications between the two.
It's an instance of the **NSPresistenceCoordinator**.


![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2011.27.09%20AM.png)


While the context keeps track of the changes that occur on the models, it's the persistence coordinator that creates the actual instances of the models when need to be saved.


![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%201.46.39%20PM.png)


Similarly, if the user retrieves the stored data, the persistence coordinator is responsible for retrieving existing instances from a persistence store.


![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%201.46.39%20PM.png)


#### Persistent Conttainer
Is not a part of the core data stack, but it creates the stack with minimal code. In the app delegate file writing this bit of code creates the core data stack.
Since it's a **lazy** var it's only initialized when it's accessed for the first time.

```Swift
  lazy var persistentContainer: NSPersistentContainer = {
    let container = NSPersistentContainer(name: "Name of your data model file")
    container.loadPersistentStores(completionHandler: { (storeDescription, error) in
      if let error = error as NSError? {
        fatalError("Unresolved error \(error), \(error.userInfo)")
      }
    })
    return container
  }()
```


## Core Data Fetch Request
Fetch requests are represented by **NSFetchRequest** and it contains an entity description and also optionally sorting and filtring for retrieved data.






# CoreData and UIKIT

While all the fundamentals are the same as core data in SwiftUI, the syntax, helper classes, and how they are used are different.
- There's no Environment object in UIKIT, so we need to pass the mange object context manually whenever needed.
- There're no property observers to handle fetch requests.
- Unlike SwiftUI, the views won’t be automatically updated when data in the president store changes.


