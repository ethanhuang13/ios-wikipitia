# 避免使用 Swift 的驚嘆號

Swift 語法中的驚嘆號是個大坑。基本上用到 `!` 的地方都有可能會閃退。就像這樣：

```text
fatal error: unexpectedly found nil while unwrapping an Optional value
```



