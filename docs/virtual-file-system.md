# Virtual file system

The virtual file system lets you compose directories and files from other file systems (or dynamically resolved content) into a single, read-only tree, exposed as a regular `DirectoryLink`. This is useful e.g. for building a merged view over several storages, or for [mounting a Windows drive](windrive.md) that combines multiple sources.

It is built into the `TagBites.IO` package - no additional dependency is required.

## Usage

Build a tree with `VirtualDirectory` and `VirtualDirectoryEntry`, mapping existing directories under new names (with optional aliases):
```csharp
using TagBites.IO.Virtual;

var directory1 = FileSystem.Local.GetDirectory("C:/Files");
var directory2 = FileSystem.Local.GetDirectory("C:/OtherFiles");

var virtualDirectory = new VirtualDirectory
{
    Entries =
    {
        new VirtualDirectoryEntry(directory1, "a", "aa1", "aa2"), // "a" is exposed as "a", "aa1" and "aa2"
        new VirtualDirectoryEntry("b") // an empty virtual sub-directory
        {
            Entries =
            {
                new VirtualDirectoryEntry(directory1, "a1"),
                new VirtualDirectoryEntry(directory2, "b1")
            }
        }
    }
};

DirectoryLink root = virtualDirectory.ToDirectory();
// root/a, root/aa1, root/aa2  -> directory1
// root/b/a1                   -> directory1
// root/b/b1                   -> directory2
```

An entry can wrap a `DirectoryLink`, a `FileLink`/any `IFileSystemLink`, a nested `VirtualDirectory`, or just a name for an empty virtual sub-directory.

### Dynamic (lazily resolved) content

For content that needs to be resolved on demand (e.g. entries loaded from a database or an external API), subscribe to `EntriesResolve` and, optionally, `IsUpToDateResolve` to control caching:
```csharp
var virtualDirectory = new VirtualDirectory();
virtualDirectory.EntriesResolve += (sender, e) =>
{
    e.Entries.Add(new VirtualDirectoryEntry(FileSystem.Local.GetDirectory("C:/Files"), "a"));
};
virtualDirectory.IsUpToDateResolve += (sender, e) => e.IsUpToDate = false; // always re-resolve

DirectoryLink root = virtualDirectory.ToDirectory();
```
Call `virtualDirectory.NeedRefresh()` to force the entries to be re-resolved on next access.

### Checking whether a link is virtual

```csharp
var link = root.GetFile("a/file.txt");

var isVirtual = VirtualFileSystem.IsVirtualLink(link);
var sourceLink = VirtualFileSystem.TryExtractSourceLink(link); // the real underlying link, if any
```
