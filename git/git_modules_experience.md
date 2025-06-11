* git_modules_experience.md
* 2025.06.11

1. DESCRIPTION  
2. INVESTIGATION  
3. CONSLUSION  
4. USAGE  
5. RELATED ISSUES  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Git modules experience.

-------------------------------------------------------------------------------
2. INVESTIGATION
-------------------------------------------------------------------------------
kernel.org:

  * https://lore.kernel.org/git/1716310675.20230122233403@inbox.ru/  

Github:

  * `svn externals replacement` : https://github.com/chronoxor/gil/issues/6  
  * `svn complete replacement for externals` : https://github.com/dirk-thomas/vcstool/issues/243  
  * `nested submodules detection w/o .gitmodules file` : https://github.com/gitextensions/gitextensions/discussions/10644 (https://github.com/gitextensions/gitextensions/issues/10642)  
  * `[Discussion] nested submodules detection w/o .gitmodules file` : https://github.com/ingydotnet/git-subrepo/issues/575  

  * `Zip archive to include submodule` : https://github.com/dear-github/dear-github/issues/214  
  * `[PATCH] archive: add –recurse-submodules to git-archive command` : https://git.github.io/rev_news/2022/11/30/edition-93/, https://lore.kernel.org/git/pull.1359.git.git.1665597148042.gitgitgadget@gmail.com/  

Stackoverflow:

  * `How to use svn2git on repository` : https://stackoverflow.com/questions/43843107/how-to-use-svn2git-on-repository/77767104#77767104
  * `svn to git migration with nested svn:externals` : https://stackoverflow.com/questions/42793618/svn-to-git-migration-with-nested-svnexternals/56649569#56649569  
  * SubGit details for a one time SVN to Git migration: https://stackoverflow.com/questions/79524070/git-svn-places-git-folder-in-the-same-directory-as-the-project-parent-folder-in/79525532#79525532  

-------------------------------------------------------------------------------
3. CONSLUSION
-------------------------------------------------------------------------------
Seems [vcstool](https://github.com/dirk-thomas/vcstool) and the forks is the
best choice here to avoid `.gitmodules` file in first place.

> [!NOTE]
>
> Original repository: https://github.com/dirk-thomas/vcstool
>
> Discussion: `Status of vcstool` : https://github.com/dirk-thomas/vcstool/issues/242

> [!NOTE]
>
> There is another forks with or without continue of original repository
> development:
>
> * https://github.com/aaronplusone/vcstool/tree/feature-sparse-checkouts
> * https://github.com/MaxandreOgeret/vcstool2 (https://pypi.org/project/vcstool2/)
> * https://github.com/ros-infrastructure/vcstool

> [!NOTE]
>
>  Mine list of `vcstool` related forks:
>
>  * https://github.com/orgs/andry81-forks/repositories?type=source&q=vcstool
>
> Maximum list of the original repository forks:
> 
> * https://github.com/dirk-thomas/vcstool/forks?include=active%2Carchived%2Cinactive%2Cnetwork&page=1&sort_by=stargazer_counts
>   (sorted by stars)
> * https://useful-forks.github.io/?repo=dirk-thomas/vcstool
>   (sorted by stars)
> * https://devnoname120.github.io/useful-forks/?repo=dirk-thomas/vcstool
>   (sorted by stars and ahead commits)

On another hand the `.gitmodules` file might be required in some circumstances
and it's presence (and vcstool file too as well if hashes are used) in the
default branch is not preferred, because will interfere with source files
commits and may be left in the desync state because of a rewrite in a
submodule, which will require a rewrite in all dependent repositories and
basically is pain in arse. So better just push the `.gitmodules` file out of a
default branch into standalone branch where you can rewrite without affecting
the sources.

For example, use `master` branch to store sources without submodules and
`master-modules` (or `master-all`) branch to store sources plus `.gitmodules`
file to checkout default branch with head submodules or with `freezed`
submodules. Later you can just rewrite `master-modules` without affecting the
source files from `master` (there won't be merges from `master-modules` to
`master`, only from `master` into `master-modules`).

This approach can use both the `vcstool` as a more convenient tool by default
and the `.gitmodules` file, where it is might be required because of
circumstances. For example, for a ZIP archive (`Download ZIP` button) as were
noted [here](https://github.com/dear-github/dear-github/issues/214).

-------------------------------------------------------------------------------
4. USAGE
-------------------------------------------------------------------------------
https://github.com/andry81/externals

-------------------------------------------------------------------------------
5. RELATED ISSUES
-------------------------------------------------------------------------------
* `Changelog files organization` : https://gist.github.com/andry81/d278e6d129ca1af326eafb67470a2ae3
* `Git branches organization` : https://gist.github.com/andry81/44bb1375ad327fa4717119784c0526a6
* `GitHub credentials notable details and changes` : https://gist.github.com/andry81/4dc954fc98a84807195080c6d2c5bc72
