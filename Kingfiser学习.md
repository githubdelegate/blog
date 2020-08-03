
# Kingfisher 学习总结

KF 是一个图片展示的库，可以很方便的展示图片。展示图片流程是图片的获取，图片的修改，图片的展示



## 0. **图片的定义**
图片，如何定义图片数据， 在iOS里面可以使用UIImage，在macOS里面使用NSImage，在下载文件中使用的是Data,还可以使用Base64字符串类型表示图片,在其他平台有其他的定义，所有如果要适配不同平台，不同类型的图片，需要一个统一的定义，方便下面的处理。
很巧妙的使用泛型的方式，`Base`可以为`UIImage NSImage 或者Data`

```swift
public struct KingfisherWrapper<Base> {
    public let base: Base
    public init(_ base: Base) {
        self.base = base
    }
}


// 对应的是值类型
public protocol KingfisherCompatibleValue {}
extension String: KingfisherCompatibleValue { }
extension CGSize: KingfisherCompatibleValue {}
extension KingfisherCompatibleValue {
    /// Gets a namespace holder for Kingfisher compatible types.
    public var kf: KingfisherWrapper<Self> {
        get { return KingfisherWrapper(self) }
        set { }
    }
}


// 对应的是引用类型，
public protocol KingfisherCompatible: AnyObject { }
extension KFCrossPlatformImage: KingfisherCompatible { }
extension KingfisherCompatible {
    /// Gets a namespace holder for Kingfisher compatible types.
    public var kf: KingfisherWrapper<Self> {
        get { return KingfisherWrapper(self) }
        set { }
    }
}
```
> 但是为什么要区分 值类型和 引用类型呢？ 






## 1. 图片的获取

包括网络下载图片，本地文件图片，自定义方式获取的图片，还有缓存图片

* `Source` 表示图片数据的来源，有两种方式 看下面代码，一个是网络请求，一个是自定义一个图片数据提供方式(这种方式可以是本地图片文件，base64图片，或者其他的方式)，总之就是要么提供图片下载地址，要么提供图片数据。 `ImageDataProvider`


```swift
    case network(Resource)
    
    /// The target image should be provided in a data format. Normally, it can be an image
    /// from local storage or in any other encoding format (like Base64).
    case provider(ImageDataProvider)

```



## 2. 图片的修改



## 3. 图片的展示

包括 图片解码


* `ImageFormat` 定义图片格式， 并且计算Data类型的图片数据的格式
### 1.  `ImageProcessor` 定义图片处理协议类型，定义了一些常用的图片处理方法 
```swift
public enum ImageProcessItem {
    case image(KFCrossPlatformImage)
    case data(Data)
}


public protocol ImageProcessor {
    var identifier: String { get }
    func process(item: ImageProcessItem, options: KingfisherParsedOptionsInfo) -> KFCrossPlatformImage?
}

extension ImageProcessor {
    public func process(item: ImageProcessItem, options: KingfisherOptionsInfo) -> KFCrossPlatformImage? {
        return process(item: item, options: KingfisherParsedOptionsInfo(options))
    }
}

```

* processor 可以拼接使用，通过 `|>` 来实现处理器的拼接。


```swift


typealias ProcessorImp = ((ImageProcessItem, KingfisherParsedOptionsInfo) -> KFCrossPlatformImage?)
struct GeneralProcessor: ImageProcessor {
    let identifier: String
    let p: ProcessorImp
    func process(item: ImageProcessItem, options: KingfisherParsedOptionsInfo) -> KFCrossPlatformImage? {
        return p(item, options)
    }
}

 public func append(another: ImageProcessor) -> ImageProcessor {
        let newIdentifier = identifier.appending("|>\(another.identifier)")
        return GeneralProcessor(identifier: newIdentifier) {
            item, options in
            if let image = self.process(item: item, options: options) {
                return another.process(item: .image(image), options: options)
            } else {
                return nil
            }
        }
    }


infix operator |>: AdditionPrecedence
public func |>(left: ImageProcessor, right: ImageProcessor) -> ImageProcessor {
    return left.append(another: right)
}


```

### 2. **ImageProgressive**



### 3. **ImageTransition** 
定义了图片的交互动画


### 4. **ImageDrawing**
这个主要包括 图片的解码 && 图片处理 Blend Crop,Resize,Blur图片
