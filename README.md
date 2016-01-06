![Spots logo](https://raw.githubusercontent.com/hyperoslo/Spots/master/Images/logo_v3.png)

[![CI Status](http://img.shields.io/travis/hyperoslo/Spots.svg?style=flat)](https://travis-ci.org/hyperoslo/Spots)
[![Version](https://img.shields.io/cocoapods/v/Spots.svg?style=flat)](http://cocoadocs.org/docsets/Spots)
[![License](https://img.shields.io/cocoapods/l/Spots.svg?style=flat)](http://cocoadocs.org/docsets/Spots)
[![Platform](https://img.shields.io/cocoapods/p/Spots.svg?style=flat)](http://cocoadocs.org/docsets/Spots)

## Table of Contents

* [Description](#description)
* [Key features](#key-features)
* [Usage](#usage)
  * [View models in the Cloud](#view-models-in-the-cloud) 
  * [Programmatic approach](#programmatic-approach) 
* [SpotsController](#spotscontroller)
  * [Delegates](#delegates) 
    *  [SpotsDelegate](#spotsdelegate) 
    *  [SpotsRefreshDelegate](#spotsrefreshdelegate) 
    *  [SpotsScrollDelegate](#spotsscrolldelegate)
    *  [SpotsCarouselScrollDelegate](#spotscarouselscrolldelegate)
* [JSON structure](#json-structure)
* [Models](#models)
  * [Component](#component)
  * [ListItem](#listitem)
* [Installation](#installation)
* [Dependencies](#dependencies)
* [Author](#author)
* [Credits](#credits)
* [Contributing](#contributing)
* [License](#license)

## Description

**Spots** is a view controller framework that makes your setup and future
development blazingly fast. Because of its internal architecture,
generic view models, you can easily move your view models into
the cloud. This is super easy to do because **Spots** can translate
JSON data into view model data right out-of-the-box.
It is packed with convenience methods that are all at your disposal through
the public API.

## Key features

- JSON based views that could be served up by your backend.
- Supports displaying multiple collection and table views in the same container.
- Features both infinity scrolling and pull to refresh, all you have to do is to 
setup delegates that conform to the public protocols on `SpotsController`.
- No need to implement your own data source, every `Spotable` object has their 
own set of `ListItem`’s.
which is maintained internally and is there at your disposable if you decide to 
make changes to them.
- Easy configuration of `UICollectionView`’s, `UITableView`'s and any custom spot 
implementation that you add. 
This improves code reuse and helps to theme your app and ultimately keep your application consistent.
- Support custom Spots, all you need to do is to conform to `Spotable`
- A rich public API for appending, prepending, inserting, updating or 
deleting `ListItems`.
- Features three different spots out-of-the-box; `CarouselSpot`, `GridSpot`, `ListSpot`
- Static custom cell registrations for all `Spotable` objects. 
Write one view cell and use it across your application, when and where you 
want to use it.
- Cell height caching, this improves performance as each cell has its height stored as a calculated value.
on the view model.
- Supports multiple cell types inside the same data source, no more ugly if-statements in your implementation;
**Spots** handles this for you by using a cell registry.

## Usage

### View models in the Cloud
```swift
let spots = Parser.parse(json)
let controller = SpotsController(spots: spots)
navigationController?.pushViewController(controller, animated: true)
```

The JSON data will be parsed into view model data and your view controller is ready to be presented, it is just that easy.

### Programmatic approach
```swift
let spots = [Spotable]()
let myContacts = Component(title: "My contacts", items: [
  ListItem(title: "John Hyperseed"),
  ListItem(title: "Vadym Markov"),
  ListItem(title: "Ramon Gilabert Llop"),
  ListItem(title: "Khoa Pham"),
  ListItem(title: "Christoffer Winterkvist")
])
let listSpot = ListSpot(component: myContacts)
let controller = SpotsController(spots: [listSpot])

navigationController?.pushViewController(controller, animated: true)
```

## SpotsController
The `SpotsController` inherits from `UIViewController` but it sports some core features that makes your everyday mundane tasks a thing of the past. `SpotsController` has four different delegates

## Delegates

### SpotsDelegate

```swift
public protocol SpotsDelegate: class {
  func spotDidSelectItem(spot: Spotable, item: ListItem)
}
```

`spotDidSelectItem` is triggered when a user taps on an item inside of a `Spotable` object. It returns both the `spot` and the `item` to add context to what UI element was touched.

### SpotsRefreshDelegate

```swift
public protocol SpotsRefreshDelegate: class {
  func spotsDidReload(refreshControl: UIRefreshControl, completion: (() -> Void)?)
}
```

`spotsDidReload` is triggered when a user pulls the `SpotsScrollView` offset above its initial bounds.

### SpotsScrollDelegate

```swift
public protocol SpotsScrollDelegate: class {
  func spotDidReachEnd(completion: (() -> Void)?)
}
```

`spotDidReachEnd` is triggered when the user scrolls to the end of the `SpotsScrollView`, this can be used to implement infinite scrolling.

### SpotsCarouselScrollDelegate

```swift
public protocol SpotsCarouselScrollDelegate: class {
  func spotDidEndScrolling(spot: Spotable, item: ListItem)
}
```

`spotDidEndScrolling` is triggered when a user ends scrolling in a carousel, it returns item that is being displayed and the spot to give you the context that you need.

## JSON structure

```json
{
 "components" : [
    {
      "title"    : "Hyper iOS",
      "type"     : "list",
      "span"     : "1",
      "items" : [
        {
          "title"    : "John Hyperseed",
          "subtitle" : "Build server",
          "image"    : "{image url}",
          "type"     : "profile",
          "action"   : "profile:1",
          "meta"     : {"nationality" : "Apple"}
        },
        {
          "title"    : "Vadym Markov",
          "subtitle" : "iOS Developer",
          "image"    : "{image url}",
          "type"     : "profile",
          "action"   : "profile:2",
          "meta"     : {"nationality" : "Ukrainian"}
        },
        {
          "title"    : "Ramon Gilabert Llop",
          "subtitle" : "iOS Developer",
          "image"    : "{image url}",
          "type"     : "profile",
          "action"   : "profile:3",
          "meta"     : {"nationality" : "Catalan"}
        },
        {
          "title"    : "Khoa Pham",
          "subtitle" : "iOS Developer",
          "image"    : "{image url}",
          "type"     : "profile",
          "action"   : "profile:4",
          "meta"     : {"nationality" : "Vietnamese"}
        },
        {
          "title"    : "Christoffer Winterkvist",
          "subtitle" : "iOS Developer",
          "image"    : "{image url}",
          "type"     : "profile",
          "action"   : "profile:5",
          "meta"     : {"nationality" : "Swedish"}
        }
      ],
      "meta" : []
    }
  ]
}
```

## Models

### Component

```swift
public struct Component: Mappable {
  public var index = 0
  public var title = ""
  public var kind = ""
  public var span: CGFloat = 0
  public var items = [ListItem]()
  public var size: CGSize?
  public var meta = [String : String]()
}
```

- **.index**
Calculated value to determine the index it has inside of the spot.
- **.title**
This is used as a title in `UITableView` view.
- **.kind**
Determines which spot should be used. `carousel`, `list`, `grid` are there by default but you can register your own.
- **.span**
Determines the amount of views that should fit on one row, by default it is set to zero and uses the default `UICollectionViewFlowLayout` to render `UICollectionView` based views.
- **.size**
Calculated value based on the amount of items and their combined heights.
- **.meta**
Custom data that you are free to use as you like in your implementation.

### ListItem

```swift
public struct ListItem: Mappable {
  public var index = 0
  public var title = ""
  public var subtitle = ""
  public var image = ""
  public var kind = ""
  public var action: String?
  public var size = CGSize(width: 0, height: 0)
  public var meta = [String : AnyObject]()
}
```

- **.index**
Calculated value to determine the index it has inside of the component.
- **.title**
The headline for your data, in a `UITableViewCell` it is normally used for `textLabel.text` but you are free to use it as you like.
- **.subtitle**
Same as for the title, in a `UITableViewCell` it is normally used for `detailTextLabel.text`.
- **.image**
Can be either a URL string or a local string, you can easily determine if it should use a local or remote asset in your view.
- **.kind**
Is used for the `reuseIdentifier` of your `UITableViewCell` or `UICollectionViewCell`.
- **.action**
Action identifier for you to parse and process when a user taps on a list item. We recommend [Compass](https://github.com/hyperoslo/Compass) as centralized navigation system.
- **.size**
Can either inherit from the `UITableViewCell`/`UICollectionViewCell`, or be manually set by the height calculations inside of your view.
- **.meta**
This is used for extra data that you might need access to inside of your view, it can be a hex color, a unique identifer or additional images for your view.

## Installation

**Spots** is available through [CocoaPods](http://cocoapods.org). To install
it, simply add the following line to your Podfile:

```ruby
pod 'Spots'
```

## Dependencies

- **[Sugar](https://github.com/hyperoslo/Sugar)**
To sweeten the implementation.
- **[Tailor](https://github.com/zenangst/Tailor)**
To seamlessly map JSON to both `Component` and `ListItem`.
- **[Imaginary](https://github.com/hyperoslo/Imaginary)**
To offer a solid and easy-to-use solution for loading remote images in both core and custom views.

## Author

[Hyper](http://hyper.no) made this with ❤️. If you’re using this library we probably want to [hire you](https://github.com/hyperoslo/iOS-playbook/blob/master/HYPER_RECIPES.md)! Send us an email at ios@hyper.no.

## Contribute

We would love you to contribute to **Spots**, check the [CONTRIBUTING](https://github.com/hyperoslo/Spots/blob/master/CONTRIBUTING.md) file for more info.

## Credits

- The idea behind Spot came from [John Sundell](https://github.com/johnsundell)'s tech talk "Components & View Models in the Cloud - how Spotify builds native, dynamic UIs".
- [Ole Begemanns](https://github.com/ole/) implementation of [OLEContainerScrollView](https://github.com/ole/OLEContainerScrollView) is the basis for `SpotsScrollView`, we salute you.
Reference: http://oleb.net/blog/2014/05/scrollviews-inside-scrollviews/

## License

**Spots** is available under the MIT license. See the LICENSE file for more info.
