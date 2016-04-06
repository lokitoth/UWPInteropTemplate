# UWPInteropTemplate
A template project to be able to activate Windows Runtime (Universal) classes and APIs that are callable from Desktop applications.

## How it works

The primary bit that lets the toolchain know this is going to interop with the Universal Windows Platform is the `TargetPlatformVersion`:

    <PropertyGroup>
      <TargetPlatformVersion>10.0</TargetPlatformVersion>
    </PropertyGroup>

Once you have this in the project, VS would let you reference Windows Runtime Assemblies. The problem is that if you reference them, you still need to reference the `System.Runtime.WindowsRuntime` assembly - which at first glance does not seem to exist anywhere. You need to manually browse to the ReferenceAssemblies folder and grab it. Unfortunately there are several versions, and it is not clear which one to use - especially if you are adding it after adding a reference to WinRT types from inside VS.

The easiest way to make this work is to use the one at: C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETCore\v4.5\. Note you will still use this even for a .NET Framework 4.6 codebase.

    <Reference Include="System.Runtime.WindowsRuntime, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089, processorArchitecture=MSIL">
      <SpecificVersion>False</SpecificVersion>
      <HintPath>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETCore\v4.5\System.Runtime.WindowsRuntime.dll</HintPath>
    </Reference>

However, this one will not work properly with the individually imported assemblies via the Visual Studio UI. What you need to import is the Windows.winmd that is the union metadata for all of the Universal Runtime APIs.

    <Reference Include="Windows, Version=255.255.255.255, Culture=neutral, processorArchitecture=MSIL">
      <SpecificVersion>False</SpecificVersion>
      <HintPath>C:\Program Files (x86)\Windows Kits\10\UnionMetadata\Windows.winmd</HintPath>
    </Reference>

## Usage

You can use this in applications as well as libraries. In the case of libraries, so long as you do not leak the UWP types outside of the assembly, you only need to add the target platform and runtime references these from the library project, and can consume the library as you normally would, without more detailed knowledge of what it depends on.
