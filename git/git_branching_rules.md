* git_branching_rules.md
* 2023.12.26

1. DESCRIPTION
2. RULES

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
Git branches organization.

-------------------------------------------------------------------------------
2. RULES
-------------------------------------------------------------------------------
In the order of a server hook scripts execution:

1. There is must already exists branches, where a user can push.
   There would be a pre-commit hook which will block to push to an unexisted or a restricted branch.

   **Example**:

      > existed branches:     `master`, `dev`, `feature/*`, `release`<br />
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

   **Note**:

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

4. [OPTIONAL] All commits must contain a current branch name in the metadata.
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
   This particular check on different branch name can be turned off by a configuration variable.

   **Note**:

   > Such functionality is relied upon `git commit` command and may be omitted or not used by a user. So this must be somehow integrated into local user environment to automatically create such metadata in each user commit.
   > This will hold additional history of branches been committed and deleted. So is required the mechanism to print/show such branches in a user local environment additionally to the generic Git branches.
