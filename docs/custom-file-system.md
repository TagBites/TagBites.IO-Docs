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

All operations interfaces are optional except `IFileSystemOperations` - implement only the ones relevant to what your storage supports; `FileSystem` checks at runtime which interfaces an operations object implements and reports `NotSupportedException` for missing capabilities.

| Interface    | Description           | 
| ------------- |:------------- 
| `IFileSystemOperations`    | The base interface every operations class must implement. Exposes `Kind` and `Name` of the file system. |   
| `IFileSystemReadOperations`     | Provides methods for reading files / directories, e.g. `ReadFile`, `GetLinkInfo`, `GetLinks`. | 
| `IFileSystemWriteOperations`    | Provides methods for writing, moving or deleting files; create, move or delete directories and update metadata files / directories. |
| `IFileSystemDirectReadWriteOperations`    | Provides `OpenFileStream` for opening a file directly as a read/write/read-write `Stream`, instead of going through `ReadFile`/`WriteFile`. |
| `IFileSystemCustomPathOperations`    | Lets a file system provide its own `DirectorySeparator` and normalize/correct paths via `CorrectPath`. |
| `IFileSystemMetadataSupport`    | Reports which metadata (`IsHidden`, `IsReadOnly`, last write time) the file system actually supports. |
| `IFileSystemPermissionsOperations`    | Provides methods `HasReadAccess` and `HasWriteAccess` for checking files permissions.  | 
| `IFileSystemWatchOperations`    | Provides methods `CreateFileWatcher` and `CreateDirectoryWatcher` to watch files/directories changes, see [change notifications and watching](change-notifications.md).   | 
| `IFileSystemHistoryOperations`    | Provides files/directories history methods e.g. `ReadFileVersion`, `DeleteFileVersion`, `GetFileVersionInfo`, `GetFileVersions`, see [file history and versioning](file-history.md).| 
| `IFileSystemAsyncReadOperations`    | Provides asynchronous equivalents of `IFileSystemReadOperations` methods.  | 
| `IFileSystemAsyncWriteOperations`    | Provides asynchronous equivalents of `IFileSystemWriteOperations` methods.  | 
| `IFileSystemAsyncDirectReadWriteOperations`    | Provides an asynchronous equivalent of `IFileSystemDirectReadWriteOperations` (`OpenFileStreamAsync`). |
| `IFileSystemAsyncHistoryOperations`    | Provides asynchronous equivalents of `IFileSystemHistoryOperations` methods. |

> Built-in async-to-sync and sync-to-async wrappers (in `TagBites.IO.Operations.Wrappers`) let a file system that only implements the synchronous (or only the asynchronous) interfaces be used through the other API as well.