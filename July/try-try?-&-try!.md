```swift
func divide(_ a: Int, by b: Int) throws -> Int {
    if b == 0 {
        throw NSError(domain: "DivisionByZero", code: 1, userInfo: nil)
    }
    return a / b
}

do {
    let result = try divide(10, by: 0)
    print(result)
} catch {
    print("Error occurred: \(error)")
}
```
**try required a do-try-catch block, Use when you want to handle the error Manually, Disadvantages is it is verbose and it does not check the nil values**

**try?** Converts error to Optional

Returns nil if the error is thrown

```swift
let result = try? divide(10, by: 0)
print(result) // Prints: nil
```

**try!** force tries, crashes if the error is thrown, Use when you are absolutely sure that call wont fail

```swift
let result = try! divide(10, by: 2) // OK
let crash = try! divide(10, by: 0)  // ❌ Crashes at runtime
```
