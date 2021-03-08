# Local Storage in iOS

## User Defaults
#### What is user defaults?
User defaults is a built in ***Data Dictionary*** that stores data for as long as the app is installed.

User defaults ca store integers, booleans, strings, arrays, dictionaries, dates and more.

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
When retreving objects the result is **optional**, so we need to ***type-cast*** to a non-optional type and use a ***nil coalising*** operator to handle missing values.

```swift
let savedArray = defaults.object(forKey: "Array") as? [String] ?? [String]()
```








# How to store data on an iOS device using the core data framework.
#### What is core data?
Core data is an extremly powerful ***framework*** that abstracts much of the details of how and where the data is aculty stored, allowing the developer to focus on what you need to save.

You can use the framework to: 
- Store Data permently for offline use.
- Cache temporary data.
- Functionality like undos and redos.

While the how of the framework works the same under the hood, it uses different format to achive that in **SwiftUI** Vs in **UIKit**.
