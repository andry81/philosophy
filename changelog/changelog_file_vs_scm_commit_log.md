* changelog_file_vs_scm_commit_log.md
* 2025.12.04

1. DESCRIPTION  
2. REPOSITORY DIRECTORIES EXAMPLE  
3. FORMAT DESCRIPTION  
4. GENERIC CASE OR SHORTCUT MESSAGES  
5. EXPLANATION  
6. DEVELOPMENT TOOLS  
7. RELATED RESOURCES  
8. KNOWN ISSUES  
8.1. Winmerge Substitution Filter does not work as expected  

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
    A change without fix or new implementation. Can include functionality add or remove.
  * `refactor`:
    A change related to a file system file/directory move/rename or sources change without functionality change.

  > [!NOTE]  
  > All types here is a matter of a personal choice. Here is described only a generic set of changelog message types for a manual typing.
  > For example, you can use other changelog message types like: `notice`, `warning`, `error`, `hint`, `doc` and etc.
  > Each changelog message type has sence in a particular sources context.

  `<location>`:

  ```
  <file-path-list>
  ```

  or with a cross product of paths:

  ```
  <dir-path-list>[: <file-path-list>]
  ```

  Example:

  ```
  dirA/dir1, dirA/dir3, dirB/*, dirC: fileA, fileB, fileC-*.txt
  ```

  Resulted cross product:

  * dirA/dir1/fileA
  * dirA/dir1/fileB
  * dirA/dir1/fileC-*.txt
  * dirA/dir3/fileA
  * dirA/dir3/fileB
  * dirA/dir3/fileC-*.txt
  * dirB/*/fileA
  * dirB/*/fileB
  * dirB/\*/fileC-*.txt
  * dirC/fileA
  * dirC/fileB
  * dirC/fileC-*.txt

  Rules:

  * If the `<location>` expression does have only one section, has no `:` and `/` characters, then a string can be a directory or a file, depending on the context.
  * If the `<location>` expression has or has no a file or a directory name globbing character (`*`, `?`), except single component `**` or `*`, but does not have a slash `/` character, then the scope has a multi level recursion.
  * If the `<location>` expression has a directory name globbing character (`*`, `?`), then the level of recursion is dependent on the `*` character only: `**` - multi level, `*` - single level.

  So the `**/<file>` and `<file>` paths are equal, the `*<file>` and `<file>` paths has the same level of recursion, but `*/<file>` and `<file>` paths has a different level of recursion.
  To specifically limit the scope of a path, you have to particularly use a path with a slash character.

  Examples:

  * `<file>`, `**/<file>`         - a file somewhere in a source tree, a path with multi level recursion.
  * `/<file>`                     - a file in the root directory.
  * `<dir>`, `**/<dir>`           - somewhere in a directory of a source tree.
  * `/<dir>`                      - somewhere in a directory in the root directory.
  * `<dir>/<file>`                - a file somewhere in a directory in a source tree.
  * `<dir>/<dir>`                 - somewhere in a directory of a directory in a source tree.
  * `*/<file>`, `<dir>/*/<file>`  - partial path to a file with a single level recursion.
  * `*/<dir>`, `<dir>/*/<dir>`    - partial path to a directory with a single level recursion.
  * `<dir>/**/<file>`             - partial path to a file with multi level recursion beginning a directory.
  * `<dir>/**/<dir>`              - partial path to a directory with multi level recursion beginning a directory.

  > [!NOTE]  
  > There is no clear distinction between a file and a directory in the end of a path, because `<location>` depends on what the commit has.
  > So in case of an ambiguous path, which is not much frequent case, you can use the `<location>` with the leading slash or a slash in the middle.
  > For a precise location you must use the leading slash with the exact relative source tree path without the globbing characters.

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

  > [!NOTE]  
  > The `#xxx` is a common method to inject the commit message and commit hash
  > into the GitHub Issue conversation:  
  > https://stackoverflow.com/questions/1687262/link-to-the-issue-number-on-github-within-a-commit-message

  > [!WARNING]  
  > If you try to rewrite the repository with commits containing `#xxx` reference or complete issue url `https://github.com/USER/REPO/issues/NUMBER`, then the GitHub adds the backtrack link into the issue conversation again:  
  > `A repository rewrite involves reappend all backtrack links to rewrited commits with issue links` : https://github.com/orgs/community/discussions/49227

  Rules for blocks with dates:

  * Dates must be sorted in descending order.

  * Each block with a date is associated with one day change but not limited to
    a day. For example, if one change made by `2023.06.15` but another
    by `2023.06.16`, then it is better to split it into 2 blocks. Or it can be
    left as a single block if changes has not been recorded specifically to
    the day (squashed).

  * A block date can has an incomplete form like `2025-12-XX`, `2025-YY-XX`,
    `2025-12-??` and etc. The meaning is that the block order or completeness
    is not yet defined and can be changed or moved later.

  * More than one block can has the same date.

  * Each commit must be associated with at least one block.

  * `<location>` must use relative paths and can use wildcards.

  * Lines in each block must be sorted by `<type>`.

  * Lines must include changes only from the respective directory source and
    must exclude changes outside of the directory source:

    * Exclude external dependencies as a part of VCS like `svn:externals` in the
      SVN and `.gitmodules` file changes in the Git.

    * Exclude other VCS properties stored in the file system or behind it like
      `svn:ignore`/`svn:*` in SVN and
      `.gitignore`/`.gitattributes`/etc files in the Git.

    * Exclude VCS extensions like `.gitsvnextmodules` file and others.

    * The root changelog may include the changes from a custom git externals
      like [vcstool](https://github.com/dirk-thomas/vcstool) externals in the
      `.externals` file.

    > [!NOTE]  
    > The difference between a custom git externals and builtin
    > externals is that the custom externals are not part of a VCS and so
    > must be included in the changeset of a changelog file.

  * `changelog.txt`, `userlog.md` and others must include all lines from the
    same files in nested directories.

  * If a `changelog.txt` is extracted from another project or directory, then
    you can leave the old lines and split them from the new lines using:
    `===============================================================================`
    as a separator between the blocks.

  * A change can be represented with multiple lines of different `<type>`.
    For example, you can write a fix description for the `fixed:` line, but
    additionally add a set of `changed:` lines to describe what is changed
    beside the fix.

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
  `security` | :shield:, :lock:, :closed_lock_with_key: | `:shield:`, `:lock:`, `:closed_lock_with_key:`
  `new` | :new:, :star:, :star2:, :eight_pointed_black_star:, :stars:, :sparkle:, :sparkler:, :sparkles:, :small_blue_diamond:, :diamonds:, :gem:, :zap:, :ribbon: | `:new:`, `:star:`, `:star2:`, `:eight_pointed_black_star:`, `:stars:`, `:sparkle:`, `:sparkler:`, `:sparkles:`, `:small_blue_diamond:`, `:diamonds:`, `:gem:`, `:zap:`, `:ribbon:`
  `fixed` | :wrench:, :hammer_and_wrench:, :hammer:, :nut_and_bolt: | `:wrench:`, `:hammer_and_wrench:`, `:hammer:`, `:nut_and_bolt:`
  `changed` | :memo:, :pencil:, :pencil2:, :black_nib: | `:memo:`, `:pencil:`, `:pencil2:`, `:black_nib:`
  `doc` | :notebook:, :book:, :open_book:, :closed_book:, :page_with_curl:, :scroll:, :page_facing_up:, :books: | `:notebook:`, `:book:`, `:open_book:`, `:closed_book:`, `:page_with_curl:`, `:scroll:`, `:page_facing_up:`, `:books:`
  `notice` | :information_source:, &#8505;, &#9432;, 🛈 | `:information_source:`, `&#8505;`, `&#9432;`, `&#128712;`/`F0 9F 9B 88`
  `warning` | :warning: | `:warning:`
  `error` | :no_entry_sign:, :no_entry:, :bangbang:, :exclamation:, :x: | `:no_entry_sign:`, `:no_entry:`, `:bangbang:`, `:exclamation:`, `:x:`
  `hint` | :bulb:, :point_up: | `:bulb:`, `:point_up:`
  `refactor` | :twisted_rightwards_arrows:, :recycle:, :repeat:, :arrows_clockwise:, :arrows_counterclockwise: | `:twisted_rightwards_arrows:`, `:recycle:`, `:repeat:`, `:arrows_clockwise:`, `:arrows_counterclockwise:`

* `seclog.md`

  Optional filtered markdown variant of the `userlog.md` file which does
  include only security lines.

-------------------------------------------------------------------------------
4. GENERIC CASE OR SHORTCUT MESSAGES
-------------------------------------------------------------------------------
Because there exists `<type>` and `<location>` prefix, then you can leave a
short message related to a particular file or directory changes instead
of a detailed one. Such a message is issued as a shortcut to a detailed
message.

Here is collected a basic set of frequently used messages as examples:

<table>
  <tr>
    <th>
      Type
    </th>
    <th>
      Messages
    </th>
    <th>
      Meaning
    </th>
  </tr>
  <tr>
    <td>
      fixed
    </td>
    <td>
      execution&nbsp;[fixup]
    </td>
    <td>
      Basic minimal set of fixes to pass tests or fix the expected functionality or output after an execution.
      The result basically is tested for a correct value at least manually.
    </td>
  </tr>
  <tr>
    <td>
      fixed
    </td>
    <td>
      <ul>
        <li>minor&nbsp;fixup</li>
        <li>code&nbsp;fixup</li>
      </ul>
    </td>
    <td>
      Basic minimal set of fixes to fix a code expected functionality or an output.
      The result is NOT tested for a correct value, which means a code functionality may be altered or broken.
      Difference between the `minor` and `code` is that the `code` may has a not minor code fix.
    </td>
  </tr>
  <tr>
    <td>
      refactor
    </td>
    <td>
      <ul>
        <li>description&nbsp;[fixup]</li>
        <li>minor&nbsp;[refactor]</li>
      </ul>
    </td>
    <td>
      Description or comments fixup or refactor. In case of a code does NOT alter a functionality.
    </td>
  </tr>
  <tr>
    <td>
      changed
    </td>
    <td>
      <ul>
        <li>minor&nbsp;cleanup</li>
        <li>[code]&nbsp;cleanup</li>
      </ul>
    </td>
    <td>
      Remove of redundant or unused sources or a code. In case of a code does alter a functionality.
      Difference between the `minor` and `code` is that the `code` may has a not minor code change(s).
    </td>
  </tr>
  <tr>
    <td>
      changed
    </td>
    <td>
      <ul>
        <li>minor&nbsp;improvement(s)</li>
        <li>code&nbsp;improvement(s)</li>
      </ul>
    </td>
    <td>
      Set of improvements to change a code functionality or an output.
      Difference between the `minor` and `code` is that the `code` may has a not minor code change(s).
    </td>
  </tr>
  <tr>
    <td>
      fixed, changed
      </ul>
    </td>
    <td>
      missed&nbsp;change(s)
    </td>
    <td>
      Set of missed changes just late or expensive to amend.
      Difference between the `fixed` and `changed` is that the `fixed` is for a fixing change.
    </td>
  </tr>
  <tr>
    <td>
      changed
    </td>
    <td>
      <ul>
        <li>minor&nbsp;improvement(s)</li>
        <li>code&nbsp;improvement(s)</li>
      </ul>
    </td>
    <td>
      Set of improvements to change a code functionality or an output.
      Difference between the `minor` and `code` is that the `code` may has a not minor code change(s).
    </td>
  </tr>
  <tr>
    <td>
      changed
    </td>
    <td>
      back&nbsp;merge [from <project-name> project]
    </td>
    <td>
      Merge of changes from an external project used as a dependency.
    </td>
  </tr>
</table>

-------------------------------------------------------------------------------
5. EXPLANATION
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
  or one at a time SVN commit. In another words, without the changelog file for
  the Git system with the rebase or merge you have to manually edit and change
  comments for all the rest following commits, and for the SVN system even for
  each commit separately.

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

  > [!NOTE]  
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

* A branch rewrite operation does not rewrite the change log file as happens
  for the log in a commit's comments. You have to specifically rewrite the
  changelog file to rewrite and/or lose the log history. So the changelog file
  prevents accidental lost of the sources change history behind the rewrite,
  squash or merge operations.

  But on another hand it prevents an easy edit of the log history to intently
  cut the history together with a commit.

  > [!NOTE]  
  > This exists because a VCS has not yet be able to associate a part of a
  > changelog file with a particular commit, so you have to duplicate the log
  > history in a VCS log.

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

* As long as a changelog file contains all the directory changelog records
  including those that has been before the repository history, a branch or the
  directory existence, then you can use the file to search a first known good
  record to find an issue, a feature or a change outside of a VCS commit
  history log.
  In case of the Git repository history search, then you can use the changelog
  file to find an initial commit by date for the `git bisect` command.

* A message with the conjunction to `<type>` and `<location>` can form a
  shortcut message to a detailed one is described somewhere else.

* A block date can has an incomplete form like `2025-12-XX`, `2025-YY-XX` or
  `2015-12-??`. The meaning is that the block order or completeness is not yet
  defined and can be changed or moved later.

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

* Commits remove after rewrite or merge does not let the associated history
  from remove in the changelog file. You have to manually find and remove the
  log history.

  > [!NOTE]  
  > This exists because a VCS has not yet be able to associate a part of a
  > changelog file with a particular commit, so you have to duplicate the log
  > history in a VCS log.

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
6. DEVELOPMENT TOOLS
-------------------------------------------------------------------------------
Some development tools to help compare and merge `changelog.txt`, `userlog.md`
and `seclog.md`.

1. Notepad++ MultiReplace plugin

  Can replace multiple pairs of strings using regular expressions, intermediate
  variables and a micro code with conditional expressions in the `Replace with`
  input field.

  See for details:

  https://github.com/andry81/contools--notepadplusplus/tree/HEAD/deploy/plugins/MultiReplace

  You can use these MultiReplace lists to strip (undecorate) the `userlog.md`,
  merge it from the `changelog.txt` and then, redecorate it back:

  https://github.com/andry81/contools--notepadplusplus/tree/HEAD/deploy/plugins/MultiReplace/lists/winmerge

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
7. RELATED RESOURCES
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

https://gitmoji.dev/  
https://github.com/carloscuesta/gitmoji  

-------------------------------------------------------------------------------
8. KNOWN ISSUES
-------------------------------------------------------------------------------

-------------------------------------------------------------------------------
8.1. Winmerge Substitution Filter does not work as expected
-------------------------------------------------------------------------------
https://github.com/WinMerge/winmerge/issues/2221 :
`Poor compare experience with Substitution Filters`  
