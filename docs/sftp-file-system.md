# SFTP file system

This file system provides an implementation for the file system based on the SFTP protocol using [SSH.NET](https://github.com/sshnet/SSH.NET) library.

## Usage

Password authentication:
```csharp
var address = "address";
var username = "username";
var password = "password";

using(FileSystem fileSystem = TagBites.IO.Sftp.SftpFileSystem.Create(address, username, password))
{
    // ... using like standard file system
}
```

Private key authentication:
```csharp
using Renci.SshNet;

var keyFile = new PrivateKeyFile("path/to/private-key");

using(FileSystem fileSystem = TagBites.IO.Sftp.SftpFileSystem.Create(address, username, keyFile))
{
    // ... using like standard file system
}
```
> Remember to use `using` statement or call `Dispose` method after usage.

## Host key verification

By default - the same as in SSH.NET itself - the server's host key is trusted without verification. To pin an expected key, pass a `SftpConnectionConfig` with `ExpectedHostKeyFingerprintSha256` set to the fingerprint reported by the server (e.g. via `ssh-keyscan` or `ssh -o FingerprintHash=sha256`); the connection is rejected if the presented key doesn't match:
```csharp
var connectionConfig = new SftpConnectionConfig(address, username, password)
{
    ExpectedHostKeyFingerprintSha256 = "ohD8VZEXGWo6Ez8GSEJQ9WpafgLFsOfLOtGGQCQo6Og",
    ConnectionTimeout = TimeSpan.FromSeconds(10)
};

using(FileSystem fileSystem = TagBites.IO.Sftp.SftpFileSystem.Create(connectionConfig))
{
    // ... using like standard file system
}
```

## Limitations

- The SFTP protocol has no server-side recursive listing, so recursive listing is performed by walking subdirectories one by one instead of a single call.
- Direct stream access (`OpenRead`/`OpenWrite`) is read-only, same as the [FTP file system](ftp-file-system.md).
- Like FTP, S3, Dropbox and Google Cloud Storage, this file system does not support real change watching - see [change notifications and watching](change-notifications.md) for what `CreateWatcher()` does and doesn't detect here.

Full implementation on github: [TagBites.IO.Sftp](https://github.com/TagBites/TagBites.IO.Sftp).
