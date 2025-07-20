📘 Combine Framework - Basic Usage in Swift
This note demonstrates how to use different Combine operators and publishers in Swift including Just, Future, PassthroughSubject, CurrentValueSubject, debouncing user input, and throttling user actions.

1. `Just` Publisher

```swift
import Combine
import Foundation

let publisher = Just("Deepam")

publisher.sink { completion in
    print("Done \(completion)")
} receiveValue: { value in
    print("Value : \(value)")
}
```

2. `Future` Publisher

```swift
func fetchData() -> Future<String, Error> {
    return Future { promise in
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
            let success: Bool = true
            if success {
                promise(.success("Data Loaded"))
            } else {
                promise(.failure(NSError(domain: "Error", code: -1)))
            }
        }
    }
}

let cancellable = fetchData().sink { completion in
    print("Completion: \(completion)")
} receiveValue: { value in
    print("Value: \(value)")
}
```

3. Network Call using `dataTaskPublisher`

```swift
let url: URL = URL(string: "https://www.example.com/v1/endPoint")!
var urlRequest: URLRequest = URLRequest(url: url)
urlRequest.httpMethod = "POST"
urlRequest.setValue("application/json", forHTTPHeaderField: "Content-Type")
urlRequest.httpBody = try! JSONEncoder().encode(["key": "value"])

let newCancellable = URLSession.shared.dataTaskPublisher(for: urlRequest)
    .sink { completion in
        switch completion {
        case .finished:
            debugPrint("Completed")
        case .failure(let error):
            debugPrint(error)
        }
    } receiveValue: { value in
        debugPrint("Received Value: \(value)")
    }
```

4. Using `PassthroughSubject`

```swift
let passThrough = PassthroughSubject<String, Never>()
passThrough.send("Hello")
passThrough.send("World")

passThrough.sink { value in
    print("Received Value using PassThrough: \(value)")
}

passThrough.send("Swift")
passThrough.send("Backend")
```

5. Using `CurrentValueSubject`

```swift
let currentValue = CurrentValueSubject<String, Never>("Hello")

currentValue.sink { value in
    print("Received Value from Current Value is: \(value)")
}

currentValue.send("World")
currentValue.send("Swift")
currentValue.send("Google")
```

6. Debouncing Search Input

```swift
class SearchViewModel {
    @Published var searchText: String = ""
    private var cancellables: Set<AnyCancellable> = Set<AnyCancellable>()

    func debounceSearch() {
        $searchText
            .debounce(for: .milliseconds(500), scheduler: RunLoop.main)
            .removeDuplicates()
            .sink { value in
                debugPrint("Value is: \(value)")
            }
            .store(in: &cancellables)
    }
}

let viewModel = SearchViewModel()
viewModel.searchText = "H"
viewModel.searchText = "He"
viewModel.searchText = "Hel"
viewModel.searchText = "Hell"
viewModel.searchText = "Hello"
viewModel.debounceSearch()
```

7. Throttling Button Taps

```swift
class ThrottleViewModel {
    let buttonTapSubject = PassthroughSubject<Void, Never>()
    private var cancellables: Set<AnyCancellable> = Set<AnyCancellable>()

    func throttleButton() {
        buttonTapSubject
            .throttle(for: .seconds(1), scheduler: RunLoop.main, latest: false)
            .sink {
                print("Button Pressed")
            }
            .store(in: &cancellables)
    }

    func simulateTap() {
        buttonTapSubject.send()
    }
}

// Usage:
// let throttleObject = ThrottleViewModel()
// throttleObject.throttleButton()
// throttleObject.simulateTap()
// throttleObject.simulateTap()
// throttleObject.simulateTap()
```

8. Bonus: Empty `Just` Publisher Example

```swift
let a = Just<String>("Deepam")
a.sink { completion in
    // Do nothing
} receiveValue: { value in
    // Do nothing
}
```
