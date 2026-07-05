# Windows drive (WinDrive)

`TagBites.IO.WinDrive` mounts any `DirectoryLink` - local, FTP, ZIP, [virtual](virtual-file-system.md), or any other TagBites.IO file system - as a real Windows drive letter, backed by [Dokan](https://github.com/dokan-dev/dokan-dotnet). This lets ordinary Windows applications (Explorer, Office, ...) work with the directory as if it were a regular disk.

> Requires the [Dokan driver](https://github.com/dokan-dev/dokany/releases) to be installed on the target machine, and targets `net6.0-windows`.

## Usage
```csharp
var directory = FileSystem.Local.GetDirectory("D:/Files");

using var drive = new WinDrive(directory)
{
    Name = "My Drive",
    IsReadOnly = false,
    IsSingleThread = false,
    IsNetworkDrive = false
};

drive.Mount();
// ... drive is now available, e.g. as "V:\"
drive.Dismount();
```

## Options

| Member | Description |
| --- | --- |
| `Name` | Drive label shown in Windows. |
| `MountPoint` | Drive letter (e.g. `"V:\\"`) or an NTFS folder path to mount into. If not set, `"V:\\"` is used, falling back to the first free letter from `Z` down. |
| `IsReadOnly` | Mounts the drive with write protection. |
| `IsSingleThread` | Processes Dokan events on a single thread. Not recommended - can easily become a bottleneck. |
| `IsNetworkDrive` | Mounts as a network drive instead of a removable/fixed disk. |
| `AccessDenyFiles` | A `WinDriveAccessDenyFileCollection` of file/name patterns that should be hidden from the drive (e.g. `Thumbs.db`, `desktop.ini`). Use `AddWindowsFiles()` and `AddCodeFiles()` to add common presets (Windows shell files, `.git`, etc.). |
| `AccessDenyProcesses` | A `WinDriveAccessDenyProcessCollection` of process names that are denied access to the drive (e.g. to keep a syncing client like Dropbox from touching it). |
| `IsMounted` | Gets whether the drive is currently mounted; setting it calls `Mount()`/`Dismount()`. |

## Combining with a virtual file system

Since `WinDrive` accepts any `DirectoryLink`, it can be combined with a [virtual file system](virtual-file-system.md) to expose several sources merged into one drive:
```csharp
using TagBites.IO.Virtual;

var directory1 = FileSystem.Local.GetDirectory("D:/TestFiles");
var directory2 = FileSystem.Local.GetDirectory("D:/OtherFiles");

var virtualDirectory = new VirtualDirectory
{
    Entries =
    {
        new VirtualDirectoryEntry(directory1, "a"),
        new VirtualDirectoryEntry(directory2, "b")
    }
};

using var drive = new WinDrive(virtualDirectory.ToDirectory()) { Name = "Merged Drive" };
drive.AccessDenyProcesses.Add("dropbox");
drive.AccessDenyFiles.AddWindowsFiles().AddCodeFiles();

drive.Mount();
```

Full implementation on github: [TagBites.IO.WinDrive](https://github.com/TagBites/TagBites.IO.WinDrive).
