# Yarn Dependency Issue

I've been seeing massive installation times during `yarn add`. `yarn` seems to try and fetch everything everytime, and then attempts to link a massive number of files.

## Pertinent Versions

`bash`:
```sh-session
$ yarn -v
1.3.2
$ node -v
v8.9.3
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 16.04.2 LTS
Release:        16.04
Codename:       xenial
$ asciinema --version
asciinema 1.4.0
```

`PowerShell`:
```sh-session
> conemu-cyg-64.exe --version
ConEmu cygwin/msys connector version 1.2.2
> wslbridge.exe --version
wslbridge 0.2.3
> Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' | Select-Object ProductName, CurrentMajorVersionNumber, CurrentMinorVersionNumber, ReleaseId, CurrentBuild, CurrentBuildNumber, BuildLabEx


ProductName               : Windows 10 Pro Insider Preview
CurrentMajorVersionNumber : 10
CurrentMinorVersionNumber : 0
ReleaseId                 : 1709
CurrentBuild              : 17025
CurrentBuildNumber        : 17025
BuildLabEx                : 17025.1000.amd64fre.rs_prerelease.171020-1626
```

## Duplication

I built [a runner script](runner-yarn-issue) to quickly test this. Well, not quickly, but at least simply. The script does several things (note: I kinda just used some random packages; I'm not trying to say anything about any of them):

1. Nukes the cache
    * `npm cache clean --force`
    * `yarn add` with a fresh `--cache-folder`
2. Creates a package with few dependencies
    * `moment`
    * `fs-extra` (to show how long adding a new package takes; same package as below)
3. Creates a package with many dependencies
    * `@angular/cli`
    * `fs-extra` (to show how long adding a new package takes; same package as above)

I've been running it through `asciinema` to quickly upload the full session. Windows [doesn't support `script`](https://github.com/Microsoft/WSL/issues/2053).

```sh-session
$ asciinema rec -t "Yarn Dependency Issue" -c ./runner-yarn-issue yarn-issue-demo.json
~ Asciicast recording started.
~ Hit Ctrl-D or type "exit" to finish.
...
```

### With a Fresh User

[![asciicast](https://asciinema.org/a/155214.png)](https://asciinema.org/a/155214)

[Full cast](https://asciinema.org/a/155214)

```sh-session
$ cd npm-small
$ npm install moment
added 1 package in 3.048s
$ npm install fs-extra
added 4 packages in 1.236s
$ cd yarn-small
$ yarn add --cache-folder=../yarn-cache moment
Done in 2.77s.
$ yarn add --cache-folder=../yarn-cache fs-extra
Done in 2.10s.
$ cd npm-large
$ npm install @angular/cli
added 994 packages in 141.28s
$ npm install fs-extra
added 116 packages and updated 1 package in 18.263s
$ cd yarn-large
$ yarn add --cache-folder=../yarn-cache @angular/cli
Done in 218.45s
$ yarn add --cache-folder=../yarn-cache fs-extra
Done in 105.43s.
```

### With Me

[![asciicast](https://asciinema.org/a/155210.png)](https://asciinema.org/a/155210)

[Full cast](https://asciinema.org/a/155210)

```sh-session
$ cd npm-small
$ npm install moment
added 1 package in 2.863s
$ npm install fs-extra
added 4 packages in 1.351s
$ cd yarn-small
$ yarn add --cache-folder=../yarn-cache moment
Done in 2.82s.
$ yarn add --cache-folder=../yarn-cache fs-extra
Done in 2.03s.
$ cd npm-large
$ npm install @angular/cli
added 994 packages in 142.135s
$ npm install fs-extra
added 116 packages and updated 1 package in 18.505s
$ cd yarn-large
$ yarn add --cache-folder=../yarn-cache @angular/cli
Done in 207.14s.
$ yarn add --cache-folder=../yarn-cache fs-extra
Done in 105.35s.
```
