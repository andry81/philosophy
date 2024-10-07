* changelog_file_vs_scm_commit_log.md
* 2024.10.07

1. DESCRIPTION
2. REPOSITORY DIRECTORIES EXAMPLE
3. FORMAT DESCRIPTION
4. EXPLANATION
5. DEVELOPMENT TOOLS
6. RELATED RESOURCES
7. KNOWN ISSUES
<br />7.1. Winmerge Substitution Filter does not work as expected

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
  * <type>: [<location>:] [<self-hosted-issues>:] [(<authors>)] <single-line-comment> [... [(<authors>)] <single-line-comment>]

  <date>:
  * <type>: [<location>:] [<self-hosted-issues>:] [(<authors>)] <single-line-comment> [... [(<authors>)] <single-line-comment>]

  ...

  <date>:
  * <type>: [<location>:] [<self-hosted-issues>:] [(<authors>)] <single-line-comment> [... [(<authors>)] <single-line-comment>]
  ```

  or

  ```
  <date>:
  * <type>: [<location>:] [<self-hosted-issues>:]
            [(<authors>)] <single-line-comment>
            [(<authors>)] <single-line-comment>
            [(<authors>)] <single-line-comment>
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

  In the order of appearance:

  * `security`:
    A change of sources with fix related to a security only.
  * `fixed`:
    A change of sources with fix of something not related to a security.
  * `new`:
    A change of sources with first time new implementation.
  * `changed`:
    A change without fix or new implementation. Can include functionality add
    or remove.
  * `refactor`:
    A change related to a file system file/directory move/rename or sources
    change without functionality change.

  > :information_source: Note:<br/>
  > All types here is a matter of a personal choice. Here is described only a generic set of changelog message types for a manual typing.
  > For example, you can use other changelog message types like: `notice`, `warning`, `error`, `hint`, `doc` and etc.
  > Each changelog message type has sence in a particular sources context.

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
  dirA/dir1, dirA/dir3, dirB/*, dirC: fileA, fileB, fileC-*.txt
  ```

  `<self-hosted-issues>`:

  Issues number list, where issues are self hosted (does not need a full url
  link).

  ```
  #1, #02, #003
  ```

  `<authors>`:

  List of authors. Each `<single-line-comment>` can have has its own authors
  list. Better to prefix each author user name with `@` character and surround
  by the parentheses.

  ```
  (@user1, @user2, @user3) ...
  ```

  If you want to put the issue full url link, then you should put it in the
  comment body in the brackets and after the comment text:

  ```
  * <type>: ... (https://github.com/USER/REPO/issues/NUMBER1, https://github.com/USER/REPO/issues/NUMBER2, ...)
  ```

  Or put it in the next lines without brackets:

  ```
  * <type>: ...
            https://github.com/USER/REPO/issues/NUMBER
  ```

  Better to put a message together with the issue url link after the link:

  ```
  * <type>: ...
            https://github.com/USER/REPO/issues/NUMBER: `<issue-url-message>`
  ```

  > :information_source: Note:<br/>
  > The `#xxx` is a common method to inject the commit message and commit hash
  > into the GitHub Issue conversation:<br/>
  > https://stackoverflow.com/questions/1687262/link-to-the-issue-number-on-github-within-a-commit-message

  > :warning: Warning:<br/>
  > If you try to rewrite the repository with commits containing `#xxx` reference or complete issue url `https://github.com/USER/REPO/issues/NUMBER`, then the GitHub adds the backtrack link into the issue conversation again:<br/>
  > `A repository rewrite involves reappend all backtrack links to rewrited commits with issue links` : https://github.com/orgs/community/discussions/49227

  Rules for blocks with dates:

  * Dates must be sorted in descending order.

  * Each block with a date is associated with one day change but not limited to
    a day. For example, if one change made by `2023.06.15` but another
    by `2023.06.16`, then it is better to split it into 2 blocks. Or it can be
    left as a single block if changes has not been recorded specifically to
    the day (squashed).

  * More than one block can has the same date.

  * Each commit must be associated with a block.

  * `<location>` must use relative paths and can use wildcards.

  * Lines in each block must be sorted by `<type>`.

  * Lines must include changes only from the respective directory source and
    must exclude changes outside of the directory source:

    * Exclude external depedencies as a part of VCS like `svn:externals` in the
      SVN and `.gitmodules` file changes in the Git.

    * Exclude other VCS properties stored in the file system or behind it like
      `svn:ignore`/`svn:*` in SVN and
      `.gitignore`/`.gitattributes`/etc files in the Git.

    * Exclude VCS extensions like `.gitsvnextmodules` file and others.

    * The root changelog may include the changes from a custom git externals
      like [vcstool](https://github.com/dirk-thomas/vcstool) externals in the
      `.externals` file.

    > :information_source: Note:<br/>
    > The difference between a custom git externals and builtin
    > externals is that the custom externals are not part of a VCS and so
    > must be included in the changeset of a changelog file.

  * `changelog.txt`, `userlog.md` and others must include all lines from the
    same files in nested directories.

  * If a `changelog.txt` is extracted from another project or directory, then
    you can leave the old lines and split them from the new lines using:
    `===============================================================================`
    as a separator between the blocks.

* `userlog.md`:

  The optional filtered markdown variant of the `changelog.txt` file which does
  include only a most visible user changes.

  Example:

  ```
  > :information_source: this log lists user most visible changes

  > :warning: to find all changes use [changelog.txt](https://github.com/USER/REPO/tree/HEAD/changelog.txt) file

  > :open_book: Explanation: https://gist.github.com/andry81/d278e6d129ca1af326eafb67470a2ae3

  > :scroll: Legend: :shield: - security; :wrench: - fixed; :new: :sparkles: - new; :pencil: - changed; :twisted_rightwards_arrows: - refactor

  ## YYYY.MM.DD:
  * :shield: security: ...
  * :wrench: fixed: ...
  * :new: new: :sparkles: ...
  * :pencil: changed: ...
  * :twisted_rightwards_arrows: refactor: ...
  ```

  Formatted example:

  > :information_source: this log lists user most visible changes

  > :warning: to find all changes use [changelog.txt](https://github.com/USER/REPO/tree/HEAD/changelog.txt) file

  > :open_book: Explanation: https://gist.github.com/andry81/d278e6d129ca1af326eafb67470a2ae3

  > :scroll: Legend: :shield: - security; :wrench: - fixed; :new: :sparkles: - new; :pencil: - changed; :twisted_rightwards_arrows: - refactor

  ## YYYY.MM.DD:
  * :shield: security: ...
  * :wrench: fixed: ...
  * :new: new: :sparkles: ...
  * :pencil: changed: ...
  * :twisted_rightwards_arrows: refactor: ...

  Other changelog message types and icon examples:

  Type | Icon | Code
  -|-|-
  `new` | :star:, :star2:, :sparkles: | `:star:`, `:star2:`, `:sparkles:`
  `fixed` | :hammer_and_wrench:, :hammer: | `:hammer_and_wrench:`, `:hammer:`
  `changed` | :memo: | `:memo:`
  `doc` | :notebook:, :open_book:, :page_with_curl:, :scroll:, :books: | `:notebook:`, `:open_book:`, `:page_with_curl:`, `:scroll:`, `:books:`
  `notice` | :information_source:, &#8505;, &#9432;, 🛈 | `:information_source:`, `&#8505;`, `&#9432;`, `&#128712;`/`F0 9F 9B 88`
  `warning` | :warning: | `:warning:`
  `error` | :no_entry_sign:, :no_entry:, :bangbang:, :exclamation:, :x: | `:no_entry_sign:`, `:no_entry:`, `:bangbang:`, `:exclamation:`, `:x:`
  `hint` | :bulb:, :point_up: | `:bulb:`, `:point_up:`

* `seclog.md`

  Optional filtered markdown variant of the `userlog.md` file which does
  include only security lines.

-------------------------------------------------------------------------------
4. EXPLANATION
-------------------------------------------------------------------------------
Use an external file or files to describe the directory source changes in time.

Advantages (Pros) of using a separate changelog file for changes only from the
sources in a separate directory and all subdirectories (an inclusive log):

* Changes are stored separately from the source control system, so they can be
  retrieved, read or written separately from the source control system, and
  also, they become shared if the source files are stored or were stored in
  several source control systems simultaneously or sequentially.

* A changelog file is a part of the sources, and accordingly, it can be
  modified and merged along with other sources, which does not require special
  access rights (except for access rights to the source files itself).

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

* A changelog file can be relatively easily changed (corrected/added/deleted)
  anywhere in the file in subsequent commits. Not just for the last Git commit
  or one at a time SVN commit. The log in a VCS, for example, for the Git
  system had to would change with rewriting of all dependent commits, and for
  the SVN system - for each commit separately.

* When moving sources from a source control system to a source control system,
  or when moving a directory with sources within a source control system, the
  history of this directory is also moved, because the changelog file, as a
  rule, lays in the root directory and moves along with the root directory.
  If a file or a directory is registered in the changelog and is moved to a
  different repository or to a directory with different changelog file, then
  the history in the changelog file can be moved (and merged) too.

* The changes that does not include source code changes can only be left to the
  source control system, which makes it easier to find changes only in the
  source control system.

  > :information_source: Note:<br/>
  > The changes in the source code in such case can be ignored in a VCS log.
  > But because a VCS has not yet be able to associate a part of a changelog
  > file with a particular commit, you have to duplicate them in a VCS log.

* You can make temporary branches with only one big commit long to store
  already squashed Git changes (accumulated commit). In that case you use the
  Git commit command with ammend operation to accumulate changes. The changelog
  file in such case can store a real not squashed history and then can be
  merged with another branch changelog to merge history from an accumulated
  commit branch.
  This method is useful if you want then to use the Git squash on commits which
  will be used for Git rebase or merge without actually the squash operation
  (the not squashed history already saved for a squashed or accumulated
  commit).

* The changelog file from one branch (parent branch) can contain the changelog
  for another branch (child branch).
  For example, you need to store a 3dparty sources with the author changelog
  file, a readme file and etc, but you can not or must not add your own
  changelog file, readme file and other files into the author sources (a clean
  author sources in a child branch). In such case you can create a parent
  branch with your changelog file, readme file, patch files and etc and record
  changes from a child branch in the changelog file from a parent branch.

* Retains the change order of the sources after a branch Git rebase because
  the Git rebase command will automatically merge a changelog file with
  retained blocks date sequence.

* You can save a commit information in a changelog file without actually commit
  it to save the details at the moment in the hand, when the source changes are
  not ready to commit yet.

* You can record multiple changes in date blocks in one sequence, but commit
  and push them partially on the server in another depending on what you have
  done.

  For example, you can commit a changeset in the block with the date
  `2024.10.06`, but then commit a changeset in the block with the date
  `2024.10.07` into a different branch, where the changeset `2024.10.06` is
  absent.

  The changeset with the date `2024.10.06` can not be tested at the moment
  without changes in the changeset with the date `2024.10.07`, but changes
  in the changeset with the date `2024.10.07` can be tested and pushed
  independently. So you rebase and push the changeset with the date
  `2024.10.07` at first and the `2024.10.06` at the second.

  The external changelog file can show the real sequence of changes in time
  behind the commits shuffle.

* Each author can maintain its own changelog file in a branch and then merge it
  with others. The list of authors can be automatically generated by a hook
  script on the client side. And a push can be rejected by a server side hook
  if a commit does have has at least one changelog line without the authors
  list or without the author have being pushed the commit.

Disadvantages (Cons) of using a separate changelog file:

* The changes to files that are not directly related to the source code may not
  be included or registered in the changelog file (see the list above). This is
  written down as a disadvantage, because in some or exceptional cases it makes
  sense to do so.

* Errors in commits like "forgot to add file/directory",
  "forgot to change file/property" cause a new commit in a VCS log with a
  message like "missed change", which is not included in the changelog file and
  thus, not all source changes are reflected in a changelog file. In the Git
  system, it is also usually possible to change the last commit, and changes
  before the last commit are usually expensive (the destructive operation of
  rewriting all dependent commits).

* As a rule, changelog files are located independently to each other, which
  leads to duplication of text if it is necessary to duplicate all changes in
  all changelog files from nested directories in the changelog files from
  parent directories.

* Often, the changes associated with code refactoring does not get into
  changelog files from nested directories, because a refactoring can be global
  with a search in all files, including nested directories, and such changes
  are simply unprofitable to selectively duplicate in all nested changelog
  files. This causes changeset records in the changelog files from nested
  directories to be skipped to record them.

* It is necessary to cut and transfer all changes not directly related to
  source files from all changelog files if they needs to be left only in the
  logs of a VCS.

* Due to the ability to change the changelog file in subsequent commits
  anywhere, there exists a desynchronization issue with changes in a VCS log if
  they are duplicated there (records are simply inconvenient or expensive to
  change again in a VCS log).

* It is possible to accidentally commit a changelog file with a changeset block
  destined for the next or another commit.

  For example, at the time of the commit, continuing to edit it (for example,
  in SVN this is possible, in Git some times, because you need to first add the
  file to the stage specifically for a commit)

Conclusions.

   The changelog file shows, ceteris paribus, a more convenient logging model
   in different version control systems, because is an universal tool in the
   general case, where the advantages outweigh the disadvantages.

-------------------------------------------------------------------------------
5. DEVELOPMENT TOOLS
-------------------------------------------------------------------------------
Some development tools to help compare and merge `changelog.txt`, `userlog.md`
and `seclog.md`.

1. Notepad++ MultiReplace plugin

  Can replace multiple pairs of strings using regular expressions, intermediate
  variables and a micro code with conditional expressions in the `Replace with`
  input field.

  See for details:

  https://github.com/andry81/tacklebar/tree/HEAD/deploy/notepad%2B%2B/plugins/MultiReplace

  You can use these MultiReplace lists to strip (undecorate) the `userlog.md`,
  merge it from the `changelog.txt` and then, redecorate it back:

  https://github.com/andry81/tacklebar/tree/HEAD/deploy/notepad%2B%2B/plugins/MultiReplace/Lists/winmerge

2. Winmerge

  To compare `changelog.txt`, `userlog.md` and `seclog.md` between each other,
  you can use `WinMerge` lines substitution regular expression filter from the
  `Tools` menu:

  Find what | Replace with | Regular expression
  -|-|-
  ^##\s+(\d\d\d\d([.-])\d\d\2\d\d:) | \1 | [x]
  ^(\*\s+):[^:]+:\s+([a-z]+:\s+)(:[^:]+:\s*)* | \1\2 | [x]

  After that you will be able to merge `changelog.txt` into `userlog.md` and
  `seclog.md`.

-------------------------------------------------------------------------------
6. RELATED RESOURCES
-------------------------------------------------------------------------------
### Complete list of github markdown emoji markup

https://gist.github.com/rxaviers/7360908

### Emoji Mart

https://missiveapp.com/open/emoji-mart
https://github.com/missive/emoji-mart

### emoji-cheat-sheet

https://github.com/ikatyang/emoji-cheat-sheet

### Git Commit Message StyleGuide

https://github.com/slashsbin/styleguide-git-commit-message

### Gitmoji

https://gitmoji.dev/<br />
https://github.com/carloscuesta/gitmoji

-------------------------------------------------------------------------------
7. KNOWN ISSUES
-------------------------------------------------------------------------------
&nbsp;
-------------------------------------------------------------------------------
7.1. Winmerge Substitution Filter does not work as expected
-------------------------------------------------------------------------------
https://github.com/WinMerge/winmerge/issues/2221 :
`Poor compare experience with Substitution Filters`
