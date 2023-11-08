* changelog_file_vs_scm_commit_log.md
* 2023.11.08

1. DESCRIPTION
2. REPOSITORY DIRECTORIES EXAMPLE
3. FORMAT DESCRIPTION
4. EXPLANATION

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Changelog files organization.

-------------------------------------------------------------------------------
2. REPOSITORY DIRECTORIES EXAMPLE
-------------------------------------------------------------------------------

```
<root>
 |
 +- /`dir1`
 |  |
 |  +-`changelog.txt`
 |  |  # 
 |  |  # main changelog text file filtered for directory `dir1`
 |  |
 |  +-`userlog.md`
 |  |  # 
 |  |  # user log markdown file filtered for directory `dir1`
 |  |
 |  +-`seclog.md`
 |     # 
 |     # security log markdown file filtered for directory `dir1`
 |
 +-`changelog.txt`
 |  # 
 |  # main changelog text file
 |
 +-`userlog.md`
 |  # 
 |  # user log markdown file
 |
 +-`seclog.md`
    # 
    # security log markdown file
```

-------------------------------------------------------------------------------
3. FORMAT DESCRIPTION
-------------------------------------------------------------------------------

* `changelog.txt`:

  ```
  <date>:
  * <type>: [<location>:] <single-line-comment>

  <date>:
  * <type>: [<location>:] <single-line-comment>

  ...

  <date>:
  * <type>: [<location>:] <single-line-comment>
  ```

  or

  ```
  <date>:
  * <type>: [<location>:] <single-line-comment>
            <single-line-comment>
            <single-line-comment>
            ...
  ```

  `<date>`:

  ```
  YYYY.MM.DD:
  ```

  or

  ```
  YYYY-MM-DD:
  ```

  `<type>`:

  In sorted order:

  * `security`:
    Change of sources with fix related to a security only.
  * `fixed`:
    Change of sources with fix of something not related to a security.
  * `new`:
    Change of sources with first time new implementation.
  * `changed`:
    Change without fix or new implementation. Can include functionality add or
    remove.
  * `refactor`:
    Change related to a file system file/directory move/rename or sources
    change without functionality change.

  `<location>`:

  ```
  <file-path-list>
  ```

  or

  ```
  <dir-path-list>[: <file-path-list>]
  ```

  Example:

  ```
  dirA/dir1, dirA/dir3, dirC/*, dirC: fileA, fileB, fileC-*.txt
  ```

  Rules for blocks with dates:

  * Dates must be sorted in descending order.

  * Each block with a date is associated with one day change but not limited to
    a day. For example, if one change made by `2023.06.15` but another
    by `2023.06.16`, then it is better to split it into 2 blocks. Or it can be
    left as a single block if changes has not been recorded specifically to
    the day.

  * More than one block can has the same date.

  * Each commit must be associated with a block.

  * `<location>` must use relative paths and can use wildcards.

  * Lines in each block must be sorted by `<type>`.

  * Lines must include changes only from the respective directory source and
    must exclude changes outside of the directory source:

    * Exclude external depedencies as a part of VCS like `svn:externals` in
      SVN and `.gitmodules` file change in Git.

    * Exclude other VCS properties stored in the file system or behind it like
      `svn:ignore`/`svn:*` in SVN and
      `.gitignore`/`.gitattributes`/etc files in Git.

    * Exclude VCS extensions like `.gitsvnextmodules` file and others.

  * `changelog.txt`, `userlog.md` and others must include all lines from the
    same files in nested directories.

* `userlog.md`:

  Optional filtered markdown variant of the `changelog.txt` file which does
  include only a most visible user changes.

  Format example:

  ```
  > :information_source: this log lists user most visible changes

  > :warning: to find all changes use [changelog.txt](https://github.com/USER/REPO/tree/HEAD/changelog.txt) file

  > :information_source: Legend: :shield: - security; :wrench: - fixed; :new: - new; :pencil: - changed; :twisted_rightwards_arrows: - refactor

  ## YYYY.MM.DD:
  * :shield: security: ...
  * :wrench: fixed: ...
  * :new: new: ...
  * :pencil: changed: ...
  * :twisted_rightwards_arrows: refactor: ...
  ```

* `seclog.md`

  Optional filtered markdown variant of the `userlog.md` file which does
  include only security lines.

-------------------------------------------------------------------------------
4. EXPLANATION
-------------------------------------------------------------------------------
Use an external file or files to describe the directory source changes in time.

Advantages (Pros) of using a separate changelog file for changes only in the
sources in a separate directory and all subdirectories (an inclusive log):

* Changes are stored separately from the source control system, so they can be
  retrieved, read or written separately from the source control system, and
  also, they become shared if the source filess are stored or were stored in
  several source control systems simultaneously or sequentially.

* The change file is a part of the sources, and accordingly, it can be modified
  and merged along with other sources, which does not require special access
  rights (except for access rights to the source files itself).

  Examples:

  * In the SVN version control system, in order to change the comment message
    of a commit, you need to register a server hook to get privileges to change
    comments. To do this, you must initially have SVN administrator rights and
    access to the SVN database on the server side.

  * In the Git version control system, in order to change the comment message
    of a commit, it is necessary to rewrite the commit history after it
    (blockchain costs), which is a destructive operation and, also, may require
    special privileges on different hubs and integration systems.

  Thus, it is possible to add/modify/correct comments on changes in the past
  without any additional privileges.

* The change file can be relatively easily changed (corrected/added/deleted)
  anywhere in the file (and not just for the last (Git) commit or one at a time
  (SVN) commit) in subsequent commits (the log in the control system, for
  example, for the Git system had to would change with rewriting of all
  dependent commits, and for the SVN system - for each commit separately).

* When moving sources from a source control system to a source control system,
  or when moving a directory with sources within a source control system, the
  history of this directory is also moved, because the change file, as a rule,
  lays in the root of this directory and moves along with the sources.

* Changes that does not include source code changes can only be left to the
  source control system, which makes it easier to find changes only in the
  source control system (changes in the source code may be not duplicated
  there).

Disadvantages (Cons) of using a separate change file:

* Changes to files that are not directly related to the source code may not be
  included or registered in the change file (see the list above). This is
  written down as a disadvantage, because in some or exceptional cases it makes
  sense to do so.

* Errors in commits like "forgot to add file/directory",
  "forgot to change file/property" cause a new commit in version control with a
  message like "missed change", which is not included in the change file and
  thus , not all source changes are reflected in the change file. In the Git
  system, it is also usually possible to change the last commit, and changes
  before the last commit are usually expensive (the destructive operation of
  rewriting all dependent commits).

* As a rule, change files are located independently of each other, which leads
  to duplication of text if it is necessary to duplicate all changes in all
  change files from nested directories in change files from parent directories.

* Often, changes associated with code refactoring do not get into change files
  from nested directories, because a refactoring can be global with a search in
  all files, including nested directories, and such changes are simply
  unprofitable to selectively duplicate in all nested change files. This causes
  change records to be skipped in change files from nested directories.

* It is necessary to cut and transfer all changes not directly related to
  source files from all change files if they need to be left only in the logs
  of the version control system.

* Due to the ability to change the file of changes in subsequent commits
  anywhere, there is a desynchronization with changes in the version control
  log if they are duplicated there (records are simply inconvenient or
  expensive to change again in the version control logs).

* It is possible to accidentally commit a change file destined for the next and
  another commit. For example, at the time of the commit, continuing to edit it
  (for example, in SVN this is possible, in Git it is not, because you need to
  first add the file to the stage specifically for the commit)

Conclusions.

   The change file shows, ceteris paribus, a more convenient logging model in
   different version control systems, because is an universal tool in the
   general case, where the advantages outweigh the disadvantages.
