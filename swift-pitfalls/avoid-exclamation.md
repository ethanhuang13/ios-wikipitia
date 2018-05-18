---
description: '#2 這種閃退，Swift 編譯器要附上一些責任'
---

# 避免使用驚嘆號

## Swift 語法中的 ! 是個大坑

基本上用到`!`的地方都有可能會閃退。就像這樣：

`fatal error: unexpectedly found nil while unwrapping an Optional value`

好啦，我這樣寫是有點誇張了。況且如果你是 Swift 初學者的話，我必須說這實在不是你的錯。畢竟 Xcode 的錯誤訊息老是問你是不是漏了`!`沒加。然後自動更正的話它就給你加上一個`!`了事。

其實這是不對的。

針對`Optional`（也就是名稱後面有問號的型別），真正的原則是：**萬非必要，不要用驚嘆號**`!`。在搞清楚什麼情況是萬非必要之前，不要用就對了。

尤其很多時候 func 並不是你寫的，它們的回傳型別是`Optional`，這時候就要特別小心。

比如說建立一個`URL`物件，你可能用`URL(string: String)`這個方法。它回傳的是`URL?`。因為合法的網址有特定格式，你送入的`String`可能沒辦法組成真正的`URL`物件。

你很有自信地認為網址格式無誤，於是這樣寫：

```swift
let url = URL(string: "http://www.catchplay.com")! // 這邊用了!
```

但是萬一你手殘多打了個空格，

```swift
let url = URL(string: "http://www.catchplay .com")! // 這邊用了!
// 使用 url 物件時就會 crash
```

在使用 url 物件之前，應該檢查它到底是 nil 還是有東西：

```swift
let string = "http://www.catchplay.com"
let url = URL(string: string)
if url != nil {
    // 繼續使用 url!
}
```

## 改用 if let / guard let

但是而且這邊還是用了 url!。不是說好了不要用`!`嗎？你可以用`if let`語法：

```swift
let string = "http://www.catchplay.com"
if let url = URL(string: string) {
    // url 不是 nil，繼續使用 url
}
// 這邊沒有 url 可用
```

如果你覺得每次要檢查都要包了一層 if 不好，可以改用`guard let`語法：

```swift
let string = "http://www.catchplay.com"
guard let url = URL(string: string) else {
    // url 是 nil，沒辦法用
    // 這邊可以做一些例外處理
    return // 下面的行數不會被執行
}
// url 不是 nil，繼續使用 url
```

`if let`與`guard let`語法還可以一次處理多個條件，例如我丟進來的物件裡面同時要確認多個`Optional`：

```swift
func useMovie(movie: Movie) {
    guard let movieId = movie.id,
        let movieTitle = movie.title else {
            // 沒得用
            // 這邊可以做一些例外處理
            return
        }
    // 使用 movieId 與 movieTitle
}
```

Swfit 的 Optional 有很大的好處，只是初學者常常會誤用`!`。這篇的重點就是用`if let`或`guard let`取代`!`的使用。先熟用了這樣的寫法之後，再去搞清楚原理也不遲。

