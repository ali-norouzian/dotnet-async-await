# dotnet-async-await
The `.ConfigureAwait` method is used to configure how an awaited task behaves regarding context capturing. It has significant implications on performance and context switching, especially in UI and server applications.

### `ConfigureAwait(false)` vs `ConfigureAwait(true)`

- **`ConfigureAwait(false)`**: This instructs the awaiter not to capture the current synchronization context, meaning the continuation after the await will not run on the captured context. This can improve performance and avoid deadlocks in certain scenarios, especially in non-UI contexts like ASP.NET Core.

- **`ConfigureAwait(true)`**: This instructs the awaiter to capture the current synchronization context and run the continuation on that context. This is the default behavior when using `await` without `ConfigureAwait`.

### Comparison with `await`

- **`await`**: The `await` keyword pauses the execution of an async method until the awaited task completes. By default, `await` captures the current synchronization context to resume execution on the same context.

### Nearness to `await`

When considering which behavior is nearer or more similar to the default behavior of `await`:

- **`ConfigureAwait(true)`**: This is the default behavior of `await`. It captures the current synchronization context and resumes execution on that context. Therefore, `ConfigureAwait(true)` is identical to `await` without any `ConfigureAwait`.

- **`ConfigureAwait(false)`**: This differs from the default `await` behavior by explicitly not capturing the synchronization context.

### Example Usage

Here’s an example to illustrate the difference:

#### Default `await` (equivalent to `await.ConfigureAwait(true)`)

```csharp
public async Task MyMethodAsync()
{
    // Some code that runs on the original context
    await Task.Delay(1000);
    // Continuation runs on the original context
    Console.WriteLine("Resumed on original context");
}
```

#### Using `ConfigureAwait(false)`

```csharp
public async Task MyMethodAsync()
{
    // Some code that runs on the original context
    await Task.Delay(1000).ConfigureAwait(false);
    // Continuation runs on a different context (not the original one)
    Console.WriteLine("Resumed on different context");
}
```

### When to Use `ConfigureAwait(false)`

- **Library Code**: It’s often recommended to use `ConfigureAwait(false)` in library code to avoid deadlocks and improve performance, as library code typically does not need to run on the original context.
- **ASP.NET Core Applications**: In ASP.NET Core, it is common to use `ConfigureAwait(false)` because there is no synchronization context, and you don’t need to return to the original context.

### When to Use `ConfigureAwait(true)`

- **UI Applications**: In UI applications (like WPF or WinForms), you often need to update the UI after an async operation. Using `ConfigureAwait(true)` ensures that the continuation runs on the UI thread.

### Conclusion

- **`await` without `ConfigureAwait`**: By default, `await` captures the synchronization context (`ConfigureAwait(true)`).
- **`ConfigureAwait(false)`**: Does not capture the synchronization context, improving performance and avoiding deadlocks in non-UI contexts.

In summary, `ConfigureAwait(true)` is the behavior that is nearer to the default behavior of `await`.
