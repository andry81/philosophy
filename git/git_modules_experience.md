* git_modules_experience.md
* 2025.03.10

1. DESCRIPTION  
2. INVESTIGATION  
3. CONSLUSION  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Git modules experience.

-------------------------------------------------------------------------------
2. INVESTIGATION
-------------------------------------------------------------------------------
https://lore.kernel.org/git/1716310675.20230122233403@inbox.ru/  

  `svn externals replacement` : https://github.com/chronoxor/gil/issues/6  
  `svn complete replacement for externals` : https://github.com/dirk-thomas/vcstool/issues/243  
  `nested submodules detection w/o .gitmodules file` : https://github.com/gitextensions/gitextensions/discussions/10644 (https://github.com/gitextensions/gitextensions/issues/10642)  
  `[Discussion] nested submodules detection w/o .gitmodules file` : https://github.com/ingydotnet/git-subrepo/issues/575  

  `Zip archive to include submodule` : https://github.com/dear-github/dear-github/issues/214  
  `[PATCH] archive: add –recurse-submodules to git-archive command` : https://git.github.io/rev_news/2022/11/30/edition-93/, https://lore.kernel.org/git/pull.1359.git.git.1665597148042.gitgitgadget@gmail.com/  

-------------------------------------------------------------------------------
3. CONSLUSION
-------------------------------------------------------------------------------
Seems [vcstool](https://github.com/dirk-thomas/vcstool) and the forks is the
best choice here to avoid `.gitmodules` file in first place.

Discussion:  
`Status of vcstool` : https://github.com/dirk-thomas/vcstool/issues/242  

Forks:  
https://github.com/aaronplusone/vcstool/tree/feature-sparse-checkouts  
https://github.com/MaxandreOgeret/vcstool2 (https://pypi.org/project/vcstool2/)  

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
