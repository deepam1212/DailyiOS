**Actors are concurrent safe reference types in Swift:**
* Introduced with `async-await` in `Swift5.5`

Think of them like **Thread-Safe Classes.**
**When to use Actors in Swift:**

When there could be a Race Condition
```swift
var counter: Int = 0
//DispatchQueue.concurrentPerform spawns multiple threads to perform the task concurrently — not a single thread.
DispatchQueue.concurrentPerform(iterations: 1000) {
  counter += 1
}
```

This might not give you 1000 at the end,UN pridictive Behavior


**Solution:**

`Actors`

```swift
actor SafeCounter {
  var value: Int = 0

func increment() {
  value += 1
}

func getValue() -> Int {
  return value
}
}
```
**How to Use Actors in Swift:**



```swift
actor BankAccount {
    private var balance: Double = 0.0

    func deposit(_ amount: Double) {
        balance += amount
    }

    func withdraw(_ amount: Double) -> Bool {
        if balance >= amount {
            balance -= amount
            return true
        } else {
            return false
        }
    }

    func getBalance() -> Double {
        return balance
    }
}
```
**Usage:**

```swift
let account = BankAccount()

Task {
    await account.deposit(1000)
    let success = await account.withdraw(200)
    print("Withdraw successful? \(success)")
    print("Balance is \(await account.getBalance())")
}
```
**actors provide await default with every function inside it:**
**You must use await when accessing mutable state:**

```swift
actor ScoreTracker {
    var score = 0
}

let tracker = ScoreTracker()

Task {
    await tracker.score += 10  // ✅ Safe
}
```
`❌ You cannot access actor’s properties from outside without await.`

**Non-isolated Methods**
Some actor methods can be marked nonisolated to run outside actor isolation (like read-only operations that don’t need protection).
```swift
actor Logger {
    nonisolated func logStaticMessage() {
        print("Hello from Logger")
    }
}
```
So, For nonisolated functions we dont need to add await with them while calling
**MainActor:**

Ensures code runs on the main thread (UI-safe zone in iOS).

```swift
@MainActor
class ViewModel {
    var name: String = ""

    func updateUI() {
        name = "Updated"
    }
}
```

```swift
actor DownloadManager {
    private var downloadedFiles: [String] = []

    func download(file: String) {
        // simulate download
        print("Downloading \(file)...")
        downloadedFiles.append(file)
    }

    func getAllDownloads() -> [String] {
        return downloadedFiles
    }
}
//Usage:
let manager = DownloadManager()

Task {
    await manager.download(file: "video.mp4")
    await manager.download(file: "image.jpg")

    let files = await manager.getAllDownloads()
    print("Downloaded files: \(files)")
}
```




















