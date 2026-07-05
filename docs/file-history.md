# File history and versioning

TagBites.IO can expose previous versions of a file as `FileVersionLink` objects, if the underlying file system supports it. None of the built-in providers (Local, Virtual, FTP, SFTP, HTTP, ZIP, Dropbox, Google Cloud Storage, S3) implement this - it is an opt-in capability for a [custom file system](custom-file-system.md) whose operations implement `IFileSystemHistoryOperations` (and, optionally, `IFileSystemAsyncHistoryOperations` for the asynchronous equivalents).

## Listing and restoring versions

```csharp
// fileSystem is a custom file system whose operations implement IFileSystemHistoryOperations.
var file = fileSystem.GetFile("file.txt");

IList<FileVersionLink> versions = file.GetHistoryVersions();
var lastWeek = versions.FirstOrDefault(v => v.ModifyTime >= DateTime.Now.Date.AddDays(-7));

lastWeek?.Restore(); // overwrites file.txt with this version's content
```

Asynchronous equivalents are available as `GetHistoryVersionsAsync()` and `RestoreAsync()`.

## `FileVersionLink` members

| Member | Description |
| --- | --- |
| `Source` | The `FileLink` this version belongs to. |
| `ModifyTime` | The modification time identifying this version. |
| `Length`, `Hash` | Size and hash of this version's content. |
| `Read()` / `ReadAsync()` | Opens a read-only stream on this version's content. |
| `Restore()` / `RestoreAsync()` | Overwrites the source file with this version's content. |
| `Delete()` / `DeleteAsync()` | Deletes this version from the history. |
| `CanRead`, `CanWrite` | Whether this version can be read/written, per `IFileSystemHistoryOperations.HasReadAccess`/`HasWriteAccess`. |

A version can also be copied to an arbitrary destination like any other file link, e.g. `lastWeek.Copy(otherFile, overwrite: true)`, instead of restoring it in place.
