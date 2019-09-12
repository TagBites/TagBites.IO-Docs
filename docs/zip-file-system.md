# ZIP file system

This file system provides implementation for file system based on the ZIP file structure using [SharpZipLib](https://github.com/icsharpcode/SharpZipLib) library.

## Usage
```csharp
var fullName = "C:/test";

using(FileSystem fileSystem = TagBites.IO.Zip.ZipFileSystem.Create(fullName))
{
    // ... using like standard file system
}
```

> Remember to use `using` statement or call `Dispose` method after usage.

Full implementation on github: [TagBites.IO.Zip](https://github.com/TagBites/TagBites.IO.Zip).