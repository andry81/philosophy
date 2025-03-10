* git_branching_rules.md
* 2025.03.10

1. DESCRIPTION  
2. RULES  
3. FILES  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Git branches organization.

-------------------------------------------------------------------------------
2. RULES
-------------------------------------------------------------------------------
All rules here must have an optionality flag to enable/disable a particular
hook and its children.
In case of a specific child check, there would be a nested variable to
enable/disable such a check.

1. There is must already exists branches, where a user can push.
   There would be a pre-commit hook which will block to push to an unexisted or a restricted branch.

   **Example**:

      > existed branches:     `master`, `dev`, `feature/*`, `release`  
      > restricted branches:  `master`, `release`

      A user can push to these branches:

      * `master`
      * `dev`
      * `feature/*`
      * `release`

      A user can not push to these branches:

      * `main`              UNEXISTED
      * `dev/*`             UNEXISTED
      * `feature`           INVALID FORMAT
      * `release/*`         UNEXISTED

      A user can not push not merged commits in these branches:

      * `master`
      * `release`

      All commits in restricted branches must contain at least one parent as a second from any other branch.

    Branch meaning:

      1. All users can create not merge commits only in `dev` and `feature/*` branches.
         Creation in the `feature/*` means many commits feature developing.
         The `dev` exists for the rest commits, like a fix, merge from other branches or short term changes.
         If a feature is not big enough to allocate a branch in `feature/*`, then a user can make not merged commits directly in the `dev`.

      2. The `dev` branch is the place to run tests from, so each commit in the `dev` can trigger a pipeline.
         If you create a temporary commit, then you should not push into `dev`, otherwise use `feature/*` for that.

      3. After the tests has passed or commits are reviewed, then the user can merge it into the `release` or `master` branches.

      4. The `release` branch does contain commits for the next release, while the `master` can contain all commits including those after the next release.
         For more complex case the `release/<version>` can be used instead.
         In all other cases the `master` holds all completed and tested or reviewed commits.

   > [!NOTE]  
   > For simple cases with only one developer all the branches can be reduced into only 2: `master` and `dev`, where the `master` can hold not stable or not tested commits and `dev` can hold incomplete commits. The stable or release state of a branch can be marked by tags.

   To create a branch on the remote can only a privileged user like a team lead user.
   To do so the server can cantain the list of previliged users be able to create branches. And branch pattern list, where not privileged users can create branches (`feature/*`).

2. All commits must be from a being pushed user.
   If some commits authored by different user, then they must be rejected to push.
   There would be a pre-commit hook which will block to push from a different user.

   **Example**:

   > User `user` does push to branches:
   >
   > * `dev`               OK
   > * `feature/blabla`    REJECTED
   ><br />
   > The `feature/blabla` contains a commit from a different user other than that who is pushing.

   All commits must be rejected because some of being pushed commits are rejected.

   Each user must push self authored commits only.

3. All commits must contain a unique user name.
   The server must contain the user list with user names and emails with the push privileges.
   The list must always contain only unique names with unique emails.
   There would be a pre-commit hook which will block to push a commit with not unique user name.

   **Example**:

   > User `user` with email `user-A@mail.com` does push to branches:
   >
   > * `dev`               REJECTED
   > * `feature/blabla`    REJECTED
   ><br />
   > The `dev` and `feature/blabla` contains a commit with a user `user` with different email `user-B@mail.com`.

   All commits must be rejected because some of being pushed commits are rejected.

   Check must be made versus the repository instead of versus the list because the list can change over the time and contain not unique user versus the repository. To intercept that, we always check versus the repository.

   But, to speed up the check we can test versus the list at first AND versus the repository at second.

   If the list does contain a user with not unique name or email versus the repository, then the administrator user must fix the list.

4. All commits must contain a current branch name in the metadata.
   If some commits does not contain a branch name or does contain unexisted/unreachable from being pushed branch name, then they must be rejected to push.
   There would be a pre-commit hook which will block to push such commits.

   **Example**:

   > User `user` does push to branches:
   >
   > * `dev`               OK
   > * `feature/foo`       REJECTED
   ><br />
   > The `feature/foo` contains a commit without a branch name or with a branch `feature/boo` in the metadata, which is either unexist or is not being pushed.

   All commits must be rejected because some of being pushed commits are rejected.

   To push a commit with a different branch name in the metadata, you must include that branch in a push. Otherwise it looks like you try to push an incomplete set of commits.

   > [!NOTE]  
   > Such functionality is affected by `git commit` command and may be omitted or not used by a user. So this must be somehow integrated into local user environment to automatically create such metadata in each user commit.
   > This will hold additional history of branches been committed and deleted. So is required the mechanism to print/show such branches in a user local environment additionally to the generic Git branches.

   > [!NOTE]  
   > Such metadata can help in an appropriate branch colorization in the GUI software, because branch names in a commit metadata will exist even if a Git branch name contained the commit is removed or renamed a long time ago.

5. The hooks over a changelog file in the source tree (see details described in the `changelog_file_vs_scm_commit_log.md`).

5.1. All commits must contain a changelog file at least in the root directory and must contain changes in those changelog files where directories with a changelog file contains the commit changes.
     There would be a pre-commit hook which will block to commit if a commit does not have a changelog file in the root directory or does not have changes in those changelog files where directories with a changelog file contains the commit changes.

   **Example**:

   > User `user` does push to branches:
   >
   > * `dev`               REJECTED
   > * `feature/blabla`    REJECTED
   ><br />
   > The `dev` and `feature/blabla` contains a commit without a changelog file or a changelog file does not contain appropriate changes.

   All commits must be rejected because some of being pushed commits are rejected.

   Each user must record or reflect the changes made in each commit in the appropriate changelog file.

   Does not matter which one changes contained in a changelog file.
   Is matter if a changelog file exists, then it must has changes for a directory it contained in if the directory files are changed in the commit.

5.2. The server must contain the user list with user names and emails with the push privileges as described before.
     All commits with a changelog file must contain an authors list per each changelog line with changes description.
     There would be a pre-commit hook which will block to commit if one, a set or all of these are not met in the order of appearance:
   * a commit does not have an authors list in changes of at least in one changelog file.
   * a not merge commit does not have the author which is authored the commit in the list of authors in changes of a changelog file.
   * a merge commit does not have the author which is authored a parent (or a parent of the parent and so on recursively until a not merge commit) commit in the list of authors in changes of a changelog file of the merge commit.

   **Example**:

   > User `user` does push to branches:
   >
   > * `dev`               REJECTED
   > * `feature/blabla`    REJECTED
   ><br />
   > The `dev` and `feature/blabla` contains a not merge commit with a changelog file which does contain changes with description but without the author user name in at least one authors list.

   All commits must be rejected because some of being pushed commits are rejected.

   Each user must record or reflect only the its own changes in each not merge commit. If not, then a not merge commit must be split and been committed by different authors.
   This particular check for a not merge commit can be turned off by a configuration variable.

   Each user must merge only those not merged commits which authors does contain in changelog file authors list of a resulted merge commit. If at least one parent commit is authored by the author not in at least one authors list of a merge commit changlog file, then a merge commit must be rejected.
   This particular check for a merge commit can be turned off by a configuration variable.

   The rule exists to control the changes reflection within a changelog file from a parent not merge commit in case of a merge commit and within a changelog file from a not merge commit.

   > [!NOTE]  
   > Such functionality is affected by `git commit` command and may be omitted or not used by a user. So this must be somehow integrated into local user environment to help user to automatically generate an appropriate author list in changes of each changelog file before made a commit.

   > [!NOTE]  
   > A merge commit may contain changes not been recorded or reflected in a parent commit (a change description without an author). Or a change description may change after the merge (a change with the same author but with altered description). This rule does not track or reject such implications.


-------------------------------------------------------------------------------
3. FILES
-------------------------------------------------------------------------------
Here would be described several files related to the sources branching.

1. `ISSUES.txt`

   Describes the issues list in a project. Must be placed in the root directory in a single branch only, to avoid problems with the merge and duplication.

2. `TODO.txt`

   Describes the todo list in a section of a project.
   Can be placed in a subdirectory in a project. Must not duplicate items from a parent directory.
   Can be placed in multiple branches to describe a particular todo list in a branch.
   You can create a temporary branch said `dev/todo-thing` with a change in the `TODO.txt` if do not want to put the change in a history.
