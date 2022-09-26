---
title: "Azure Devops Artifacts Upstream Source Oddness"
date: 2022-09-23T10:44:20+01:00
tags: [nuget, azure, devops, artifacts, upstream source]
draft: true
---

I recently developed a project outside of a client's environment.  Now I'm at the stage I want to bring it into a clients, I worked with a colleague and he pulled the private git repo to his cloud VM development machine.

When we did a dotnet restore

	- You CANNOT add package from private feed by `dotnet add package xunit --version 2.4.2`
	- You CAN restore and it will add it to your private feed
		○ The package ref must first exist in the csproj file
	- Sequence:
		○ Add from api.nuget.org - `dotnet add package Moq --version 4.18.2 -s https://api.nuget.org/v3/index.json`
		○ Remove .nuget\packages\moq
		○ Then `dotnet restore`:
			§ You get :
			 dotnet restore                                                                                     in pwsh at 10:28:23
			  Determining projects to restore...
			  Retrying 'FindPackagesByIdAsync' for source 'https://pkgs.dev.azure.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/f
			  lat2/moq/index.json'.
			  Response status code does not indicate success: 401 (Unauthorized).
			  Retrying 'FindPackagesByIdAsync' for source 'https://pkgs.dev.azure.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/f
			  lat2/moq/index.json'.
			  Response status code does not indicate success: 401 (Unauthorized).
			  Retrying 'FindPackagesByIdAsync' for source 'https://pkgs.dev.azure.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/f
			  lat2/moq/index.json'.
			  Response status code does not indicate success: 401 (Unauthorized).
			  Retrying 'FindPackagesByIdAsync' for source 'https://pkgs.dev.azure.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/f
			  lat2/moq/index.json'.
			  Response status code does not indicate success: 401 (Unauthorized).
			  Retrying 'FindPackagesByIdAsync' for source 'https://pkgs.dev.azure.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/f
			  lat2/moq/index.json'.
			  Response status code does not indicate success: 401 (Unauthorized).
			C:\Users\KITCHENG\source\pg\nuget\nuget.csproj : error NU1301: Failed to retrieve information about 'Moq' from remote source 'https://pkgs.dev.azur
			e.com/garrardkitchen/_packaging/e859c2f8-bcb9-4e6e-bfe1-6fc8e54e7ac3/nuget/v3/flat2/moq/index.json'.
			  Failed to restore C:\Users\KITCHENG\source\pg\nuget\nuget.csproj (in 7.25 sec).
		○ Then comment out package ref in csproj
		○ Then `dotnet restore`:
			 dotnet restore                                                                                     in pwsh at 10:28:33
			  Determining projects to restore...
			  All projects are up-to-date for restore.
		○ Then uncomment and `dotnet restore` again
			dotnet restore                                                                                     in pwsh at 10:28:36
			  Determining projects to restore...
			  Restored C:\Users\KITCHENG\source\pg\nuget\nuget.csproj (in 3.18 sec).
		○ Then finally, package appears in devops nuget feed
