# msbuild-projectref-private-glitch
Demo for an inconvenience with msbuild reference copying

# Overview
![References overview][refpic]

Solid lines denote project references with `Private` set to `True`.
Dashed lines denote project references with `Private` set to `False`.
Dash-dot lines denote project references with `Private` undefined.

# Issue
With MSBuild 14.0, `ClassLibrary0` is not copied to `ConsoleApplication6`'s output directory.
This is consistent for builds from inside Visual Studio 2015, as well as from the command line.

# Analysis
Using Kirill Osenkovs [MSBuild Structured Log Viewer][analysis-tool].
In the build of `ConsoleApplication6`, MSBuild realizes it needs `ClassLibrary0`. However, for whatever reasons it cannot find the reference:
![References overview][analysis1]

A partial explanation might be this message when building `ClassLibrary2`.
![References overview][analysis2]

# Resolution
From the analysis, it's quite easy to fix the issue:
Set `Private = True` for the project reference from `ClassLibrary2` to `ClassLibrary1`.

[refpic]: media/references.png "Referenes overview"
[analysis-tool]: https://github.com/KirillOsenkov/MSBuildStructuredLog
[analysis1]: media/build-analysis-1.png "Build analysis 1"
[analysis2]: media/build-analysis-2.png "Build analysis 2"
