# Custom file system

The `FileSystem` class allows to create custom file system by passing instance of class implemented `IFileSystemReadOperations` or `IFileSystemOperations` operations.

Example implementation:

```csharp
var fileSystem = new FileSystem(new CustomFileSystemOperations());


internal class CustomFileSystemOperations : IFileSystemOperations, IFileSystemHistoryOperations
{
    // Implementation
}
```

## File system operations interfaces

| Interface    | Description           | 
| ------------- |:------------- 
| `IFileSystemReadOperations`     | Provides methods for reading files / directories, e.g. `ReadFile`, `GetLinkInfo`, `GetLinks`. | 
| `IFileSystemOperations`    | Provides methods for writing, moving or deleting files; create, move or delete directories and update metadata files / directories. |   
| `IFileSystemPermissionsOperations`    | Provides methods `HasReadAccess` and `HasWriteAccess` for checking files permissions.  | 
| `IFileSystemWatchOperations`    | Provides methods `CreateFileWatcher` and `CreateDirectoryWatcher` to watch files/directories changes.   | 
| `IFileSystemHistoryOperations`    | Provides files/directories history methods e.g. `ReadFileVersion`, `CreateFileVersion`, `DeleteFileVersion`, `ReadFileVersion`, `GetFileVersions`.| 
| `IFileSystemAsyncReadOperations`    | Provides asynchronous equivalents of `IFileSystemReadOperations` methods.  | 
| `IFileSystemAsyncOperations`    | Provides asynchronous equivalents of `IFileSystemOperations` methods.  | 
| `IFileSystemHistoryAsyncOperations`    | Provides asynchronous equivalents of `IFileSystemHistoryOperations` methods. | 