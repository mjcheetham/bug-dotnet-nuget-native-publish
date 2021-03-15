### Repro Instructions

1. Build the native package

```shell
cd NativePackage
nuget pack
```

2. Publish the managed app

```shell
cd ../ManagedApp
dotnet publish -r win-x86
```

#### Actual Behaviour

An error occurs. When publishing with a specific runtime (e.g. `-r win-x86`)
then the contents of `runtimes/<RID>/*` are trying to be copied, **without
preserving the directory structure** to the publish output directory, and fails.

```shell
error NETSDK1148: Found multiple publish output files with the same relative path: /Users/USER/.nuget/packages/nativepackage/0.1.0/runtimes/win-x86/native/bar.txt, /Users/USER/.nuget/packages/nativepackage/0.1.0/runtimes/win-x86/native/foo/bar.txt. [/Users/USER/repos/bug-dotnet-nuget-native-publish/ManagedApp/ManagedApp.csproj]
```

#### Expected Behaviour

The publish command succeeds and output is as below:

```shell
ManagedApp/bin/Debug/net5.0/win-x86/publish$ tree
.
├── ManagedApp.deps.json
├── ManagedApp.dll
├── ManagedApp.exe
├── ManagedApp.pdb
├── ManagedApp.runtimeconfig.json
...
├── bar.txt
├── foo
    └── bar.txt
...
└── ucrtbase.dll

0 directories, N files

```

#### Remarks

Note that publishing without specifying a runtime works OK. The native files are
copied to the publish output directory without expanding, creating several
runtime directories:

```shell
ManagedApp/bin/Debug/net5.0/publish$ tree
.
├── ManagedApp.deps.json
├── ManagedApp.dll
├── ManagedApp.pdb
├── ManagedApp.runtimeconfig.json
└── runtimes
    ├── win-arm64
    │   └── native
    │       ├── bar.txt
    │       └── foo
    │           └── bar.txt
    ├── win-x64
    │   └── native
    │       ├── bar.txt
    │       └── foo
    │           └── bar.txt
    └── win-x86
        └── native
            ├── bar.txt
            └── foo
                └── bar.txt
```
