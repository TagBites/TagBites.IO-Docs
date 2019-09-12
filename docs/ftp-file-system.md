# FTP file system

This file system provides an implementation for the file system based on the FTP protocol using [FluentFTP](https://github.com/robinrodricks/FluentFTP) library.

## Usage
```csharp
var address = "address";
var username ="username";
var password ="password";

using(FileSystem fileSystem = TagBites.IO.Ftp.FtpFileSystem.Create(address, username, password))
{
    // ... using like standard file system
}
```
> Remember to use `using` statement or call `Dispose` method after usage.

Full implementation on github: [TagBites.IO.Ftp](https://github.com/TagBites/TagBites.IO.Ftp).