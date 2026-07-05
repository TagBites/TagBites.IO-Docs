# Google Cloud Storage file system

This file system provides an implementation for the file system based on a Google Cloud Storage bucket, using the official [Google.Cloud.Storage.V1](https://github.com/googleapis/google-cloud-dotnet) client library.

## Usage
```csharp
var bucketName = "my-bucket";
var jsonCredential = File.ReadAllText("service-account.json");

FileSystem fileSystem = TagBites.IO.Google.GoogleFileSystem.Create(bucketName, jsonCredential);
// ... using like standard file system
```

`jsonCredential` is the content of a [service account key file](https://cloud.google.com/iam/docs/keys-create-delete) (JSON) for a service account with access to the bucket.

Full implementation on github: [TagBites.IO.Google](https://github.com/TagBites/TagBites.IO.Google).
