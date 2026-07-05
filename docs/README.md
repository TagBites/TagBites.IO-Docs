# TagBites.IO

[![NuGet](https://img.shields.io/nuget/v/TagBites.IO.svg)](https://www.nuget.org/packages/TagBites.IO/)
[![NuGet downloads](https://img.shields.io/nuget/dt/TagBites.IO.svg)](https://www.nuget.org/packages/TagBites.IO/)

TagBites.IO turns "the local disk", "an FTP server", "a Dropbox account" and "an S3 bucket" into the same thing: a `FileSystem` you browse with `FileLink`/`DirectoryLink`. Write the operation once - copy, move, sync, watch, restore a version - and it works no matter where the files actually live, without ever touching a provider-specific SDK.

## One API, any storage

Restore yesterday's S3 backup to disk:
```csharp
TagBites.IO.S3.S3FileSystem.Create(serviceUrl, accessKey, secretKey, "backups")
    .GetDirectory("2026-07-04")
    .CopyTo(FileSystem.Local.GetDirectory("C:/Restore"));
```

Turn Dropbox, an S3 bucket and a local folder into one Windows drive:
```csharp
var merged = new VirtualDirectory
{
    Entries =
    {
        new VirtualDirectoryEntry(TagBites.IO.Dropbox.DropboxFileSystem.Create(dropboxToken).GetDirectory("Photos"), "Dropbox"),
        new VirtualDirectoryEntry(TagBites.IO.S3.S3FileSystem.Create(serviceUrl, accessKey, secretKey, "bucket").GetDirectory("backups"), "S3"),
        new VirtualDirectoryEntry(FileSystem.Local.GetDirectory("C:/Files"), "Local")
    }
};

new WinDrive(merged.ToDirectory()) { Name = "Merged Drive" }.Mount(); // "V:\Dropbox", "V:\S3", "V:\Local"
```

Keep a local folder and an FTP folder live-mirrored, in both directions, for as long as the process runs:
```csharp
var synchronizer = new FileSystemLinkSynchronizer();
synchronizer.Add(FileSystem.Local.GetDirectory("C:/Photos"), ftpFileSystem.GetDirectory("photos"), FileSystemSynchronizeMode.BothWaysUsingLastWriteTime);
synchronizer.Enabled = true;
```

See [virtual file system](virtual-file-system.md) and [WinDrive](windrive.md) for the details behind the second example, and [synchronization](synchronization.md) for an important caveat about what the third example detects on the FTP side.

## Built-in file systems

| File system | Package | Storage |
| --- | --- | --- |
| [`FileSystem.Local`](local-file-system.md) | [`TagBites.IO`](https://www.nuget.org/packages/TagBites.IO/) | Local disk (`System.IO`) |
| [`VirtualFileSystem`](virtual-file-system.md) | [`TagBites.IO`](https://www.nuget.org/packages/TagBites.IO/) | In-memory composition of other file/directory links into one tree |
| [`FtpFileSystem`](ftp-file-system.md) | [`TagBites.IO.Ftp`](https://www.nuget.org/packages/TagBites.IO.Ftp/) | FTP / FTPS |
| [`SftpFileSystem`](sftp-file-system.md) | [`TagBites.IO.Sftp`](https://www.nuget.org/packages/TagBites.IO.Sftp/) | SFTP |
| [`HttpFileSystem`](http-file-system.md) | [`TagBites.IO.Http`](https://www.nuget.org/packages/TagBites.IO.Http/) | HTTP (read-only, directory listing files) |
| [`ZipFileSystem`](zip-file-system.md) | [`TagBites.IO.Zip`](https://www.nuget.org/packages/TagBites.IO.Zip/) | ZIP archive |
| [`DropboxFileSystem`](dropbox-file-system.md) | [`TagBites.IO.Dropbox`](https://www.nuget.org/packages/TagBites.IO.Dropbox/) | Dropbox |
| [`GoogleFileSystem`](google-file-system.md) | [`TagBites.IO.Google`](https://www.nuget.org/packages/TagBites.IO.Google/) | Google Cloud Storage |
| [`S3FileSystem`](s3-file-system.md) | [`TagBites.IO.S3`](https://www.nuget.org/packages/TagBites.IO.S3/) | Amazon S3 and S3 compatible storage (e.g. Cloudflare R2) |

and also allows you to create your [custom file system](custom-file-system.md).

Additionally, [`TagBites.IO.WinDrive`](windrive.md) lets you mount any `DirectoryLink` - regardless of the underlying file system - as a real Windows drive letter.

## Installation

Install the core package, plus one package per storage you need to talk to:
```
dotnet add package TagBites.IO
dotnet add package TagBites.IO.Ftp
dotnet add package TagBites.IO.Sftp
dotnet add package TagBites.IO.Http
dotnet add package TagBites.IO.Zip
dotnet add package TagBites.IO.Dropbox
dotnet add package TagBites.IO.Google
dotnet add package TagBites.IO.S3
dotnet add package TagBites.IO.WinDrive
```

## More examples

### Checking if a file exists.
```csharp
var exists = FileSystem.Local.GetFile("file.txt").Exists;
```

### Read all text from a file.
```csharp
var content = FileSystem.Local.GetFile("file.txt").ReadAllText();
```

### Asynchronously write the text to a file.
```csharp
await FileSystem.Local.GetFile("file.txt").WriteAllTextAsync("test content");
```

### Rename the file.
```csharp
FileSystem.Local.GetFile("file.txt").Rename("newTest");
```

### Move and overwrite the file.
```csharp
var source = FileSystem.Local.GetFile("a/test2.txt");
var destination = FileSystem.Local.GetFile("b/test2.txt");
source.Move(destination, true);
```

### Move the directory to a new location.
```csharp
var source = FileSystem.Local.GetDirectory("a/c");
var destination = FileSystem.Local.GetDirectory("b"); 
source.MoveTo(destination); // b/c/*
```

### Restore a previous version of the file.
```csharp
// fileSystem is a custom file system whose operations implement IFileSystemHistoryOperations, see file history and versioning.
var file = fileSystem.GetFile("file.txt");
var fileVersion = file.GetHistoryVersions().FirstOrDefault(v => v.ModifyTime >= DateTime.Now.Date.AddDays(-7));
fileVersion?.Restore();
```
See [file history and versioning](file-history.md) for details.

### One-time synchronization of directory contents between file systems.
```csharp
var ftpFileSystem = TagBites.IO.Ftp.FtpFileSystem.Create(/*parameters*/);
var localFileSystem = FileSystem.Local;
var directory1 = ftpFileSystem.GetDirectory("directory1");
var directory2 = localFileSystem.GetDirectory("directory2");
directory1.SyncWith(directory2, FileSystemSynchronizeMode.OneWay);
```
See [synchronization](synchronization.md) for a continuously running alternative, and [change notifications and watching](change-notifications.md) for reacting to changes as they happen.