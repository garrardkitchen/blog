---
title: "Dotnet Setup From Command Line"
date: 2022-01-18T17:06:51Z
draft: true
---

Create sln:

```powershell
dotnet new sln -n Sample
```

Add new projects to /src folder:

```powershell
dotnet new web -n Sample.Web -o src/Sample.Web/
dotnet new xunit -n Sample.Web.Tests -o src/Sample.Web.Tests/
```

Add new projects to sln:

```powershell
dotnet sln add .\src\Sample.Web\Sample.Web.csproj
dotnet sln add .\src\Sample.Web.Tests\Sample.Web.Tests.csproj
cd .\src\Sample.Web\
```

Add packages to projects:

```powershell
dotnet add package Refit --version 6.1.15 -s https://api.nuget.org/v3/index.json

dotnet add package MediatR --version 10.0.1 -s https://api.nuget.org/v3/index.json

dotnet add package MediatR.Extensions.Microsoft.DependencyInjection --version 10.0.1 -s https://api.nuget.org/v3/index.json

dotnet add package FluentValidator --version 2.0.4 -s https://api.nuget.org/v3/index.json
```

```powershell
dotnet restore

dotnet build

dotnet publish
```

Where to find your NuGet defaults:

```powershell
C:\Users\<username>\AppData\Roaming\NuGet
```