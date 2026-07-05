# Change notifications and watching

TagBites.IO exposes two different mechanisms for reacting to changes - pick the one that matches what you actually need to observe.

## `FileSystem` events

Every `FileSystem` instance raises `Creating`/`Created`, `Changing`/`Changed`, `Deleting`/`Deleted` and `Moving`/`Moved` events whenever an operation is performed **through that instance**, regardless of the underlying provider:
```csharp
var fileSystem = FileSystem.Local;
fileSystem.Created += (sender, e) => Console.WriteLine($"Created: {e.Link.FullName}");
fileSystem.Changed += (sender, e) => Console.WriteLine($"Changed: {e.Link.FullName}");
fileSystem.Deleted += (sender, e) => Console.WriteLine($"Deleted: {e.Link.FullName}");
fileSystem.Moved += (sender, e) => Console.WriteLine($"Moved: {e.Link.FullName} -> {e.Destination.FullName}");

fileSystem.GetFile("file.txt").WriteAllText("content"); // raises Creating/Created
```
These events only fire for operations your own code performs via this `FileSystem` object - they are not a substitute for watching for changes made by other processes or users.

## Watchers

`DirectoryLink.CreateWatcher(recursive)` and `FileSystemStructureLink.CreateWatcher()` return an `IFileSystemLinkWatcher` with its own `Created`/`Changed`/`Deleted`/`Moved` events:
```csharp
var directory = FileSystem.Local.GetDirectory("C:/Files");
using var watcher = directory.CreateWatcher(recursive: true);
watcher.Changed += (sender, e) => Console.WriteLine($"Changed: {e.Link.FullName}");
```

> **Important:** only [`FileSystem.Local`](local-file-system.md) and the [virtual file system](virtual-file-system.md) implement real, OS-level watching that detects changes made by other processes. Every other built-in provider (FTP, SFTP, HTTP, ZIP, Dropbox, Google Cloud Storage, S3) falls back to a watcher that only reacts to changes made through the *same* `FileSystem` instance in the current process - it will not notice a file someone else uploads directly to the server. If you need to detect changes made outside your process against a remote storage, poll it instead (e.g. periodically re-list the directory, or call `FileSystemLinkSynchronizer.ForceSyncAsync()` - see [synchronization](synchronization.md)).
