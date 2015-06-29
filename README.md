#TL;DR
To see how easy it is to get a build running with [Cake.Tin](https://github.com/CakeTin/Cake.Tin)

* Clone this repo (which has no Cake files)

* Run this powershell to get the files required to bootstrap the build:
	```PowerShell

	"build.ps1","caketin.zip"|%{Invoke-RestMethod -Uri "https://raw.githubusercontent.com/CakeTin/Cake.Tin.Bootstrapper/master/res/scripts/$($_)" -OutFile $_}
	```
* Run the PS script ./Build.ps1

#The slightly longer version

Cake (C# Make) is a build automation system with a C# DSL to do things like compiling code, copy files/folders, running unit tests, compress files and build NuGet packages.
Cake.Tin is a wrapper around Cake that enables using a standard C# project in Visual Studio for the build "script".

##So what's this Repo?

This repo has a very simple Hello World C# Console solution with a Nuget dependency (log4net). It has *no* build scripts or *any* Cake-related files.

By following these steps you can have it building with Cake in a few minutes

###1. Clone this repo 
if you haven't already.
###2. Get the required files
You need 2 files for a build (these will all you'll have to commit to you repo to use Cake):
* build.ps1
  * This is a bootstrapper powershell script that ensures you have Cake and required dependencies installed.
* build.cake
  * This is the actual build script. It doesn't have to be named this but this will be found by default.

An easy way to get these files is to download them using powershell. 
Open powershell, CD to the root of your repo and execute this command (this will not execute the powershell script, just download it):
```PowerShell
"build.ps1","caketin.zip"|%{Invoke-RestMethod -Uri "https://raw.githubusercontent.com/CakeTin/Cake.Tin.Bootstrapper/master/res/scripts/$($_)" -OutFile $_}
```

This downloads the files from [Cake.Tin Bootstrapper](https://github.com/CakeTin/Cake.Tin.Bootstrapper/)
###3. Run the build script
Still in powershell execute the script. 
```PowerShell
.\build.ps1
```

The script will unzip a very simple starter "script" to a Build folder compile and run it. 
The "script" finds any .sln in files in or below the root folder, restores any NuGet dependencies, and builds them.


Congratulations, you've run you first Cake script!

###4. Bonus points! - run the tests
The script is a fairly bare-bones implementation. But extending it is easy. For instance to run the extensive unit tests for the awesome application you need to add a test target:
```CSharp
Task("Run-Unit-Tests")
    .IsDependentOn("Build")
    .Does(() =>
{
    MSTest("./src/**/bin/" + configuration + "/*Test.dll");
});
```
This is using MSTest but out of the box you have XUnit and NUnit test helpers as well.

Adding the target doesn't necessarily run it unless another target is dependent on it or you call it explicitly.

In our case we can just change the default task to be dependent on our test task (which in turn is dependent on the build task):
```CSharp
Task("Default")
    .IsDependentOn("Run-Unit-Tests");
```
Running the build now will run the tests after building...
```PowerShell
.\build.ps1
```
