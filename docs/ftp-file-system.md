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

By default, connections use explicit FTPS (`FtpEncryptionMode.Explicit`) with 3 retry attempts and auto-passive data connections. For full control over the connection (encryption mode, retry attempts, custom port, proxy, etc.), pass a `FtpConnectionConfig` - which extends FluentFTP's `FtpConfig` - instead:
```csharp
var connectionConfig = new FtpConnectionConfig(address, username, password)
{
    EncryptionMode = FtpEncryptionMode.None,
    RetryAttempts = 5
};

using(FileSystem fileSystem = TagBites.IO.Ftp.FtpFileSystem.Create(connectionConfig))
{
    // ... using like standard file system
}
```

Full implementation on github: [TagBites.IO.Ftp](https://github.com/TagBites/TagBites.IO.Ftp).