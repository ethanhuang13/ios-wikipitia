---
description: 本文撰寫年代：Swift 3
---

# init\(contentsOf: URL\)會卡住執行緒

這應該是初心者容易犯的錯誤。

一般來說，產生一個 Data 或 String 是直接拿現成的資料，例如：

```swift
let string = String(data: someData)
```

但是假如有一天你的資料要從網路上讀取怎麼辦？這時你發現了有這個功能：

```swift
let string = String(contentsOf: url)
```

只要傳入有效的網址即可。

於是你就把它跟 UI 寫在一起。

```swift
func viewDidLoad() {
    ...
    let titleString = String(contentsOf: url)
    self.titleLabel.text = titleString
    ...
}
```

結果你就會發現，開啟這個畫面的時候會卡一下。為什麼呢？

因為 `init(contentsOf: URL)` 這個方法，是會下載指定 url 的資料沒錯，但是卻要等到下載完才會繼續執行下一行程式😱。換言之，如果你把它寫在 main thread，那麼 UI 的所有更新就要等你從網路上讀取完資料才能繼續。

視網路速度或與檔案大小的差異，可能會卡住幾百毫秒甚至更多。這對於本來應該是微秒級的工作來說是超大的浪費。

如果要從網路上抓資料下來，不要偷懶，還是乖乖用 URLSession 等工具吧。這樣萬一網路出錯，你才有機會做處理。

如果非要用不可的話，至少可以這樣寫：

```swift
DispatchQueue.global(qos: .userInteractive).async {
    let titleString = String(contentsOf: url)

    DispatchQueue.main.async {
        self.titleLabel.text = titleString
    }
}
```

第一行 qos 的參數請查閱 DispatchQoS.QoSClass 的文件。

