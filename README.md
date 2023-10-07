

# Rings-SDK

[![CI Status](https://img.shields.io/travis/weicb/Rings-SDK.svg?style=flat)](https://travis-ci.org/weicb/Rings-SDK)
[![Version](https://img.shields.io/cocoapods/v/Rings-SDK.svg?style=flat)](https://cocoapods.org/pods/Rings-SDK)
[![License](https://img.shields.io/cocoapods/l/Rings-SDK.svg?style=flat)](https://cocoapods.org/pods/Rings-SDK)
[![Platform](https://img.shields.io/cocoapods/p/Rings-SDK.svg?style=flat)](https://cocoapods.org/pods/Rings-SDK)

## SDK说明

​	这是智能戒指的蓝牙通信SDK，主要提供与戒指通信相关的API管理类RingManager，以及相关的数据存储管理类RingDBManager，同时也提供部分复杂算法的实现，例如睡眠相关算法。

​	注：由于sdk需要用到蓝牙，仅支持真机调试，且注意在你项目的info中添加蓝牙权限说明

## 环境要求

​	支持 iOS 版本：iOS 13.0+

​	支持的语言版本：Swift 5.0+

## 安装使用

​	该SDK由于使用数据库进行管理数据，外部依赖于WCDB框架，所以使用CocoaPods进行管理，目前仅支持使用CocoaPods的方式进行导入使用。首先在你的项目的Podfile文件中新增一个Spec源，即将下面的语句复制粘贴到项目中的Podfile最上方：

```ruby
source 'https://github.com/wcb133/RingSpec.git'
```

然后pod中导入该SDK：

```ruby
pod 'Rings-SDK'
```

最后安装：

```ruby
pod install
```

在使用地方中引入SDK即可：

```swift
import RingsSDK
```

## API说明
### 设备连接相关
##### 搜索附近的蓝牙设备，开始搜索后可通过回调获取搜索到的设备列表，也可通过 RingManager.shared.devices属性获取当前搜索到的设备

```Swift
RingManager.shared.startScan { devices in
        print("搜索到的设备列表 =========>\(String(describing: devices))")
}
```

##### 停止搜索附近的蓝牙设备，停止搜索不会清空已搜索到的设备列表，即不会清空RingManager.shared.devices

```Swift
RingManager.shared.stopScan()
```

##### 通过设备的uuid来连接指定设备，uuid由搜索到的设备模型DeviceInfo中获取

```Swift
RingManager.shared.startConnect(deviceUUID: "uuidString", resultBlock: { res in
            switch res {
            case .success(let deviceInfo):
                print("已连接设备 =========\(String(describing: deviceInfo.peripheralName))")
            case .failure(let error):
                print("连接失败 ========> \(error)")
            }
        })
```

##### 断开当前设备的连接

```Swift
RingManager.shared.disconnect()
```

### 设备通讯相关

##### 同步时间

```Swift
RingManager.shared.syncTime(date: Date()) { res in
                  switch res {
                  case .success(let isSuccess):
                      print("同步时间结果======\(isSuccess)")
                  case .failure(let error):
                      print("同步失败======\(error)")
             }
         }
```

##### 读取时间

```Swift
RingManager.shared.readTime { res in
              switch res {
              case .success(let value):
                  print("成功=====>\(value)毫秒")
              case .failure(let error):
                  print("失败=====>\(error)")
              }
          }
```

##### 读取软件版本号

```Swift
RingManager.shared.readAppVersion { res in
                switch res {
                case .success(let version):
                    print("软件版本号=====>\(version)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 读取硬件版本号

```Swift
RingManager.shared.readHardWareVersion { res in
                switch res {
                case .success(let version):
                    print("硬件版本号=====>\(version)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 读取电池电量，若返回的电池电量为101，则表示正在充电中

```Swift
RingManager.shared.readBattery { res in
                switch res {
                case .success(let value):
                    print("电量=====>\(value)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 读取电池充电状态，返回结果为ChargeStatus枚举类型，值有:

full：充满

charging：充电中

normal：正常未充电状态

```Swift
RingManager.shared.readChargeStatus { res in
                switch res {
                case .success(let state):
                    print("状态=====>\(state)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 输出实时测量心率值，单位BPM

```Swift
RingManager.shared.readHeartRate(progressBlock: { progress in
                print(" 测量进度 =====>\(progress)")
            }) { res in
                switch res {
                case .success(let value):
                    print("心率=====>\(value)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 输出心率变异性，单位毫秒(ms)

```Swift
RingManager.shared.readHRV(progressBlock: { progress in
                print(" 测量进度 =====>\(progress)")
            }) { res in
                switch res {
                case .success(let value):
                    print("心率变异性=====>\(value)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 输出实时测量血氧值

```Swift
RingManager.shared.readO2(progressBlock: { progress in
                print(" 测量进度 =====>\(progress)")
            }) { res in
                switch res {
                case .success(let value):
                    print("血氧值=====>\(value)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 读取当天实时步数

```Swift
RingManager.shared.readSteps { res in
                switch res {
                case .success(let value):
                    print("步数=====>\(value)步")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 清除实时步数

```Swift
RingManager.shared.clearSteps { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 读取本地历史数据，并且内部会将每一条数据存入到数据库中。关于如何从数据库中获取数据，见数据库管理类RingDBManager部分说明

```Swift
RingManager.shared.readDatas { progress, dataModel in
                print(" 进度 =====>\(progress)==\(dataModel)")
            } resultBlock: { res in
                switch res {
                case .success(let state):
                    print("结果=====>\(state)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

该API获取设备历史数据，每获取到一条历史信息，都会通过progressBlock闭包回调，并在获取全部结果之后，通过resultBlock回调最终结果。其中dataModel为设备数据模型，相关属性如下：

```swift
public final class RingDataModel: NSObject,TableCodable {
    // 总个数
    public var total:UInt32 = 0
    // 序号，1开始
    public var serialNum:UInt32 = 0
    // 时间戳,秒
    public var timestamp:UInt32 = 0
    // 当天截止当前累计步数
    public var stepsOfTheDay:UInt16 = 0
    // 心率,0无效
    public var rate = 0
    // 血氧,0无效
    public var O2 = 0
    // 心率变异性,0无效
    public var hrv = 0
    // 精神压力指数,0无效
    public var mentalStress = 0
    // 温度，有符号的
    public var temp:Float = 0
    // 运动激烈程度 0: 静止  0x65:运动
    public var isRunning = false
    // 睡眠类型 0：无效 1：清醒 2：浅睡 3：深睡 4.眼动期
    public var sleepType = 0
    // RR 期间个数
    public var RRNums = 0
    // RR数组
    public var rrs:[Int] = []
}
```

返回的结果state为ReadDataResult枚举类型，值有: 

```swift
public enum ReadDataResult {
    case empty // 无数据
    case complete // 完成
}
```



##### 删除全部本地数据历史记录

```Swift
RingManager.shared.clearRingData { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 采集周期设置，采集周期设置单位为秒(s)

```Swift
RingManager.shared.setFrequency(time: time) { res in
                    switch res {
                    case .success(let isSuccess):
                        print("结果=====>\(isSuccess)")
                    case .failure(let failure):
                        print("失败=====>\(failure)")
                    }
                }
```

##### 采集周期读取，读取到的采集周期为秒(s)

```Swift
RingManager.shared.readFrequency { res in
                switch res {
                case .success(let value):
                    print("成功=====>\(value)秒")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

##### 恢复出厂设置

```Swift
RingManager.shared.reset { res in
                switch res {
                case .success(let isSuccess):
                    print("成功=====>\(isSuccess)")
                case .failure(let error):
                    print("失败=====>\(error)")
                }
            }
```

### OTA相关
##### 固件升级，参数fileUrl为固件文件所在的本地路径，升级进度以及结果通过handle进行回调，

```Swift
let distance = RingManager.shared.calculateDistance(steps:100,stepSize:80)
```

### 逻辑算法相关
##### 步行距离计算，steps参数为步数，stepSize参数为步长，单位为厘米(cm)，返回结果为距离，单位为米(m)

```Swift
let distance = RingManager.shared.calculateDistance(steps:100,stepSize:80)
```

##### 睡眠时间计算

```Swift
RingManager.shared.clearRingData { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 删除全部本地数据历史记录

```Swift
RingManager.shared.clearRingData { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 删除全部本地数据历史记录

```Swift
RingManager.shared.clearRingData { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```

##### 删除全部本地数据历史记录

```Swift
RingManager.shared.clearRingData { res in
                switch res {
                case .success(let isSuccess):
                    print("结果=====>\(isSuccess)")
                case .failure(let failure):
                    print("失败=====>\(failure)")
                }
            }
```


## Author

weicb

## License

Rings-SDK is available under the MIT license. See the LICENSE file for more info.
