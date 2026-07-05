# Local file system

This file system is used to support the local file system based on `System.IO`. It is built into the `TagBites.IO` package, so no additional dependency is required, and it supports direct read/write operations and file/directory watching in addition to the standard file system operations.

## Usage
```csharp
FileSystem fileSystem = TagBites.IO.FileSystem.Local;
```

`FileSystem.Local` is a singleton shared across the process, rooted at the machine's file system root - use absolute paths (e.g. `C:/data/file.txt` or `/data/file.txt`) or paths relative to your working directory when getting links.
