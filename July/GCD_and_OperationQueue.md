🧵 Multithreading in Swift: GCD & OperationQueue
This example demonstrates the core concepts of GCD (Grand Central Dispatch) and OperationQueue in Swift with code snippets and notes.

📌 1. Serial vs Concurrent Queues (GCD)
🔁 Serial Queue (Executes tasks in order, one after another)

```swift
let serialQueue = DispatchQueue(label: "com.example.task1")

DispatchQueue.main.async {
    print("A") // Executes first on main thread

    serialQueue.sync {
        sleep(2)
        print("B") // Blocks main thread due to sync
    }

    print("C") // Executes after B, still on main thread

    serialQueue.async {
        sleep(4)
        print("D") // Executes after B and C
        serialQueue.async {
            print("D1") // Nested async on same queue
        }
    }

    serialQueue.async {
        print("D2")
        serialQueue.async {
            print("D3")
        }
    }

    print("E") // Executes immediately after C
}
```

✅ Key Takeaways:
`.sync` blocks the current thread.
`.async` does not block.
Execution in a serial queue is sequential (one task at a time).
You should never call `.sync` on the same serial queue from within itself – it causes deadlock.

⚡️ Concurrent Queue (Executes multiple tasks simultaneously)

```swift
let concurrentQueue = DispatchQueue(label: "concurrentQueue", attributes: .concurrent)

print("Sync Concurrent")
concurrentQueue.sync {
    print("X")
}
concurrentQueue.sync {
    sleep(2)
    print("Y")
}

concurrentQueue.async {
    sleep(1)
    print("1")
}
concurrentQueue.async {
    print("2")
}
concurrentQueue.async {
    print("3")
}
concurrentQueue.async {
    print("4")
}
```

✅ Key Takeaways:
With `.concurrent`, multiple `.async` tasks can run in parallel.
`.sync` is still blocking but tasks run concurrently.
Useful when performance is key and order is not critical.

🧱 2. OperationQueue with Dependencies

```swift
let operationQueue = OperationQueue()
print("A")

let block1 = BlockOperation {
    print("1")
}
let block2 = BlockOperation {
    print("2")
}
let block3 = BlockOperation {
    print("3")
}
let block4 = BlockOperation {
    print("4")
}

// Add dependency: block2 will wait for block4
block2.addDependency(block4)

// Optional: Make it a serial queue by setting concurrency to 1
operationQueue.maxConcurrentOperationCount = 1

// Add all operations
operationQueue.addOperations([block1, block2, block3, block4], waitUntilFinished: false)

// Uncomment to ensure print after queue finishes
// print("Z")
```

 Key Takeaways:
1. **OperationQueue** gives more control than GCD:
    You can set max concurrency.
    You can add dependencies.
2. Execution order is not guaranteed unless controlled via dependencies.
3. `waitUntilFinished` is `false` here, so the main thread continues immediately.

