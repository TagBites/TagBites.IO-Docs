# TagBites.IO

Lightweight and simple .NET library provides facilities for performing operations on file systems.  
TagBites.IO provides implementation for many built-ins file systems:
* [`LocalFileSystem`](local-file-system.md), 
* [`FtpFilesystem`](ftp-file-system.md),
* [`HttpFileSystem`](http-file-system.md),
* [`ZipFileSystem`](zip-file-system.md)

and also allows you to create your [custom file system](custom-file-system.md).


NuGet Package: https://www.nuget.org/packages/TagBites.IO/

## Examples

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

### Restore a copy of the file.
```csharp
var file = FileSystemWithHistorySupport.Create().GetFile("file.txt");
var fileVersion = file.GetHistoryVersions(DateTime.Now.Date.AddDays(-7)).FirstOrDefault();
fileVersion.Copy(file, true) ;

```

### Synchronization of directory contents between file systems.
```csharp
var ftpFileSystem = FtpFilesystem.Create(/*parameters*/);
var localFileSystem = FileSystem.Local;
var directory1 = localFileSystem.GetDirectory("directory1");
var directory2 = localFileSystem.GetDirectory("directory2");
directory1.SyncWith(directory2, FileSystemSynchronizeMode.OneWay);
```