* git_sync_fork_tags.md
* 2025.03.23

1. DESCRIPTION  
2. CODE  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Script to synchronize a forked repository tags with the upstream.

-------------------------------------------------------------------------------
2. CODE
-------------------------------------------------------------------------------
Clone, for example, the fork repository `https://myhub.com/user/my-fork-1` into
`myforks/my-fork-1` directory.

Create script in the `myforks` directory.

**sync-tags--my-fork-1.bat**:

```bat
@echo off

setlocal ENABLEDELAYEDEXPANSION

set "SCRIPTNAME=%~n0"

pushd "%~dp0!SCRIPTNAME:sync-tags--=!" && (
  call :CMD git remote add upstream https://myhub.com/user/my-fork-1
  call :CMD git fetch --tags --force upstream
  call :CMD git push --tags --force origin
  popd
)

exit /b

:CMD
echo ^>%*
%*
```
