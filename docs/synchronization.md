# Synchronization

TagBites.IO offers two ways to keep two directories in sync: a one-time transfer, and a continuously running synchronizer.

## One-time synchronization

`SyncWith` compares and transfers the difference between a source and a destination directory once:
```csharp
var directory1 = FileSystem.Local.GetDirectory("directory1");
var directory2 = FileSystem.Local.GetDirectory("directory2");
directory1.SyncWith(directory2, FileSystemSynchronizeMode.OneWay);
```

Available modes (`FileSystemSynchronizeMode`):

| Mode | Description |
| --- | --- |
| `OneWay` | Copies new/changed files from source to destination. Files removed from source are left untouched in destination. |
| `OneWayWithRemoveNotExisting` | Same as `OneWay`, but also removes files/directories from destination that no longer exist in source. |
| `BothWaysUsingLastWriteTime` | Propagates changes in both directions, using the last write time to resolve which side is newer. |

An overload also accepts `ignoredPaths` to exclude specific paths from the comparison.

## Continuous synchronization

`FileSystemLinkSynchronizer` keeps one or more source/destination pairs in sync for as long as it is enabled, re-syncing whenever a change is detected:
```csharp
var synchronizer = new FileSystemLinkSynchronizer();
synchronizer.Add(FileSystem.Local.GetDirectory("C:/Photos"), ftpFileSystem.GetDirectory("photos"), FileSystemSynchronizeMode.BothWaysUsingLastWriteTime);
synchronizer.Enabled = true;

// ... later
synchronizer.Remove(FileSystem.Local.GetDirectory("C:/Photos"), ftpFileSystem.GetDirectory("photos"));
synchronizer.Dispose();
```

- `Add`/`Remove` register or unregister a source/destination pair with a synchronization mode. Pairs can be added before or after `Enabled` is set.
- `Enabled` starts or stops watching and syncing all registered pairs. Enabling performs an initial synchronization pass for every pair.
- `Synchronized` is `true`/`false` once enabled, or `null` while disabled.
- `WaitForSync()` (with optional timeout) blocks until the current synchronization pass completes.
- `ForceSyncAsync()` triggers an extra synchronization pass immediately, without waiting for a change notification.

> **Caveat:** the synchronizer detects changes using the same [watchers](change-notifications.md) described above. For [`FileSystem.Local`](local-file-system.md) and the [virtual file system](virtual-file-system.md) this means real, OS-level change detection. For every other provider (FTP, SFTP, HTTP, ZIP, Dropbox, Google Cloud Storage, S3) it only reacts to changes made through the same `FileSystem` instances registered with the synchronizer - it will **not** notice a file someone else uploads directly to the remote server. In that case, call `ForceSyncAsync()` periodically (e.g. on a timer) instead of relying on change detection alone.
