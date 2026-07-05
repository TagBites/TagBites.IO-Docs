# Dropbox file system

This file system provides an implementation for the file system based on Dropbox, using the official [Dropbox.Api](https://github.com/dropbox/dropbox-sdk-dotnet) SDK.

## Usage

Using a long-lived OAuth2 access token:
```csharp
FileSystem fileSystem = TagBites.IO.Dropbox.DropboxFileSystem.Create(oauth2Token);
// ... using like standard file system
```

Using a refresh token together with your Dropbox app key/secret (recommended, as access tokens obtained this way are refreshed automatically):
```csharp
FileSystem fileSystem = TagBites.IO.Dropbox.DropboxFileSystem.Create(oauth2RefreshToken, appKey, appSecret);
// ... using like standard file system
```

> The access/refresh token is obtained through the [Dropbox OAuth2 flow](https://developers.dropbox.com/oauth-guide) for an app registered in the [Dropbox App Console](https://www.dropbox.com/developers/apps).

Full implementation on github: [TagBites.IO.Dropbox](https://github.com/TagBites/TagBites.IO.Dropbox).
