# Local Storage in iOS


## User Defaults
#### What is user defaults?
An interface to the user’s defaults database.
This is the most common and the most comfortable way to store and retrieve information across app launches.
UserDefaults work as a key-value storage, with Strings as keys. 

UserDefaults are stored in a plist file, and that's why data must be saved in a key-value pair dictionary.

There's only one copy of userDefaults shared among all classes as it's a **singletone**.

The value parameter can be only property list objects: **NSData, NSString, NSNumber, NSDate, NSArray, or NSDictionary**. For NSArray and NSDictionary objects, their contents must be property list objects.

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

However, with the **set(_ value: Any, for key: String)**, we cannot store everything.

Despite that, it is fairly easy to create a small extension to support the store and retrieve operations for any Codable type.

#### When to use it
As the name implies, we should use UserDefaults to store the user’s preferences. We should use it to store small tidbits of information (e.g. whether the user prefers light or dark mode, at what time they want to receive a daily reminder, whether they actually want to receive notifications, etc.).
As a rule of thumb, if you have a Settings screen in your app, UserDefaults are a good fit. As it needs to load up the entire file to retive data which can affect the time consumed and the app effecincy, and that's why it doesn't accept *custom datatypes*, only *standard datatypes*.

To print the filePath which is saved with the userDefaults plist ```print(NSSearchPathForDirectoriesInDomains(.userDirectory, .userDomainMask, true).last! as String)```

The following part is based on the *Beginning Core Data Course* By *raywenderlich.com*. All used photos are provided by the course.
Course link: 
# How to store data on an iOS device using the core data framework.
#### What is core data?

Core Data is the Object-Relational Mapping (ORM) framework that comes with iOS.
With this framework, we can leverage Xcode to create a cohesive data model for our application from an intuitive GUI.
Under the hood, Core Data uses an SQLite database that is stored in the Library/Application Support folder.

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

#### Manged Object Model
With core data we don't use swift types to model the data instead we use a **Managed Object**
- A managed object looks and feels like a swift class, but it's more of a representation of the data in the persistence store.
- Instead of being a specific type a managed object is a subclass of **NSManagedObject** and it's more of a generic container to store data in.
- Instead of creating objects directly in code, core data provides a visual interface to define the types that you'll use to represent your data. These types are called **Entities**.

![Screenshot](https://github.com/AyaEMahmoud/LocalStorageiOS/blob/main/Screen%20Shot%202021-03-22%20at%2010.54.50%20AM.png)

Each of the operations needed is generally delegated to very specific objects. All of these objects are referred to as the **Core Data Stack**. 

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
Core data retrives records from the data store by means of fetch requests.
Fetch requests are represented by **NSFetchRequest** and it contains an entity description and also optionally sorting and filtring for retrieved data.


## Keychain Services API 
**This part is base on a medium tutorial at
https://medium.com/ios-os-x-development/securing-user-data-with-keychain-for-ios-e720e0f9a8e2**
#### What is Keychain Services API?

A keychain is defined in Apple’s documentation as:

“…an encrypted container that securely stores small chunks of data on behalf of apps and secure services.”

Keychain offers a secure alternative to saving sensitive data, such as user names and passwords, with NSUserDefaults, plist or similar methods.

As you might already know, NSUserDefaults is simple and effective for saving small, simple bits of data, like NSNumbers or NSStrings, to your device’s file system. But this data is in no way stored securely as hackers can access it pretty easily from the device.

Apple has provided the Keychain Services API to deal with this problem and help developers build apps that safely handle passwords and other sensitive information.

Keychain is great because data encryption automatically is taken care of before it is stored in the file system so there is no need to waste time building encryption algorithms.

A keychain in both OS and iOS can be configured to lock. When locked it is impossible to access and decrypt stored keychain items. For iOS the keychain is locked when the device is locked and unlocked when the device is unlocked. Even when it is unlocked, only apps that have created an item can access it, unless configured otherwise.

#### Implementing the Keychain API
Storing and retrieving data directly with Keychain is not an easy task. Unfortunately, the Keychain API is a bit torturous to use. It is written in C and requires a lot of time-consuming configuration. Luckily, Apple and many other contributors have created higher level wrappers to hide the convoluted C code and organization powering things from beneath.

#### Implementation with Wrappers
Apple’s own Keychain wrapper is called GenericKeychain and is available within the sample code in both Objective C and Swift.
Other wrappers exist as Cocoapods or extension libraries on Github and other dependency management sites.

Here are a few Keychain wrappers I recommend:

SwiftKeychainWrapper by **Jason Rendel(jrendel) for Swift. https://cocoapods.org/pods/SwiftKeychainWrapper** 
or **https://github.com/jrendel/SwiftKeychainWrapper**
SAMKeychain by Sam Soffes for Objective C. **https://cocoapods.org/pods/SAMKeychain**
Locksmith by Matthew Palmer for Swift. (Check out the video tutorial) **https://github.com/matthewpalmer/Locksmith**

Most wrappers include three methods: one to **add** a keychain item, one to **edit** a keychain item, and one to **delete** it, and make accessing and editing keychain items possible with one line of code.
