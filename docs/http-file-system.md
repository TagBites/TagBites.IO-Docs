# HTTP file system

This file system provides an implementation for file system based on the HTTP protocol.

The system requires that each directory has a .dirls file that is in a format:

```csharp
Line format: D/F   Length   Created    Modified    Hash Algorithm    Hash    Name
```

and is created by the `TagBites.IO.Http.HttpFileSystem.CreateDirectoryInfo` method recursively for all directories.

## Usage
```csharp
FileSystem fileSystem = TagBites.IO.Http.HttpFileSystem.Create("address");
// ... using like standard file system
```

Full implementation on github: [TagBites.IO.Http](https://github.com/TagBites/TagBites.IO.Http).