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

A partial insight is given by this message when building `ClassLibrary2`.
![References overview][analysis2]

We now understand and accept that `ClassLibrary0` won't be copied to `ClassLibarary2`.

Note that this means that the `Private` flag is actually [tri-state][quotegravell], which can be quite a surprise for Visual Studio users acustomed to the boolean `Copy Local` property.

However, this doesn't explain why `ClassLibrary0` is not copied to `ConsoleApplication6`, since there is a complete chain of project references having `Private` set to `True` straight from `ConsoleApplication6` to `ClassLibrary0`.
Therefore I opened an [issue on MSBuild][issue].

# Resolution
From the analysis, it's quite easy to fix the issue:
Set `Private = True` for the project reference from `ClassLibrary2` to `ClassLibrary1`.

Consider to automatically avoid project references with `Private` undefined:
- [Define a default for `Private`][reso-1]
- Author a prebuild action which fails if the project has project references with `Private` undefined.

[refpic]: media/references.png "Referenes overview"
[analysis-tool]: https://github.com/KirillOsenkov/MSBuildStructuredLog
[analysis1]: media/build-analysis-1.png "Build analysis 1"
[analysis2]: media/build-analysis-2.png "Build analysis 2"
[quotegravell]: http://stackoverflow.com/questions/14923804/assembly-being-copied-local-when-it-shouldnt-be#comment20939785_14923854
[issue]: https://github.com/Microsoft/msbuild/issues/1845
[reso-1]: http://stackoverflow.com/a/1686086
