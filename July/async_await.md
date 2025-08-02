**All About Async & Await**


`aync` marks a function asynchronous, it might pause its execution while waiting for the result
`await` is used to call an `aync` function & wait for its result.

You can think of it as Wait here untill i get the result back then continue


**Benifits:**
* Makes asynchronous call readable and linear like synchronous code.
* Avoids callback hell.
* Works well with structured concurrent(Task, TaskGroup, etc)

**Example:**
```swift
func fetchUserName() aync -> String {
  try? await Task.sleep(nanoseconds: 1_000_000_000)// 1 Second
  return "Deepam"
}

func greetUser() async {
  let name = await fetchUserName()
  print("Hello: \(name)")
}

Task {// To run this from a Synchronous Context (like viewDidLoad()) use:
  await greetUser()
}
```
**Real World Example:**

```swift
func fetchData(from url: URL?) async -> Data {
  let (data, _) = try await URLSession.shared.data(from : url)
  return data
}

func loadJSON() {
  Task {
    do {
        let url = URL(string: "https://api.example.com/data/json")
        let data = try await fetchData(from: url)
        print("Got; \(data)")
    } catch {
      print("Error while retriving the Data")
    }
  }
}
```

**TaskGroup:** is used to run multiple asynchronous task at the same time

```swift
func fetchAllData() async {
  //
  let urls = [
               URL(string: "https://example.api.com/v1/json"),
               URL(string: "https://example.api.com/v1/json"),
               URL(string: "https://example.api.com/v1/json"),
               URL(string: "https://example.api.com/v1/json")
             ]

  //
  await withTaskGroup(of: Data?.self) { group in
      for url in urls {
        group.addTask {
          try? await fetchData(from: url)
        }
      }
    //
    for await result in group {
        print("Received Data")
      }
  }
}
```

**aync-let** also used for Multiple Concurrent calls but for fixed number, W can use Task for Multiple Calls where the number is large as aync-let is more cleaner

```swift
func loadUserData() async {
  //
  async let userName = fetchUserNameApi()
  async let userProfile = fetchUSerProfile() // Instead of using await at here we are using async let as we need to call multiple api calls and then after that we will use the await
  //
  let (finalName, profilePicture) = await(userName, userProfile)
  print("Name: \(finalName) user Profile: \(profilePicture)")
}
```

**Step-by-step: How to Cancel a Task:**

```swift
var currentTask: Task<Void, Never>? = nil

func startTask() {
    currentTask = Task {
        for i in 1...5 {
            try Task.checkCancellation()  // <- Check if cancelled
            print("Working on step \(i)")
            try await Task.sleep(nanoseconds: 1_000_000_000)
        }
        print("Task completed!")
    }
}

// Cancel later

func cancelTask() {
    currentTask?.cancel()
}
```










