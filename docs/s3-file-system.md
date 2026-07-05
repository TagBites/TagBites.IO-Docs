# S3 file system

This file system provides an implementation for the file system based on Amazon S3 (or any S3-compatible storage), using the official [AWSSDK.S3](https://github.com/aws/aws-sdk-net) client library.

Because S3 has no real directories - they are simulated using key prefixes - the file system is created with the `FileSystemFlags.IsDirectoryAsPrefix` flag.

## Usage

Amazon S3:
```csharp
var serviceUrl = "https://s3.eu-central-1.amazonaws.com";
var accessKey = "accessKey";
var secretKey = "secretKey";
var bucketName = "my-bucket";

FileSystem fileSystem = TagBites.IO.S3.S3FileSystem.Create(serviceUrl, accessKey, secretKey, bucketName);
// ... using like standard file system
```

## Cloudflare R2

R2 exposes an S3-compatible API, so a dedicated factory method builds the correct service URL from your Cloudflare account id:
```csharp
var accountId = "accountId";
var accessKey = "accessKey";
var secretKey = "secretKey";
var bucketName = "my-bucket";

FileSystem fileSystem = TagBites.IO.S3.S3FileSystem.CreateCloudflareR2FileSystem(accountId, accessKey, secretKey, bucketName);
// ... using like standard file system
```

Full implementation on github: [TagBites.IO.S3](https://github.com/TagBites/TagBites.IO.S3).
