* github_credentials.md
* 2025.03.10

1. DESCRIPTION  
2. CREDENTIALS  
2.1. Windows Credential Manager  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
GitHub credentials notable details and changes.

-------------------------------------------------------------------------------
2. CREDENTIALS
-------------------------------------------------------------------------------

-------------------------------------------------------------------------------
2.1. Windows Credential Manager
-------------------------------------------------------------------------------
Starting around the end of August 2024 the GitHub has changed the Git `git:`
and `https:` protocols authentication based on a single record in the
`Windows Credential Manager` panel.

Now is required 2 records instead of 1 as was before.

https://github.com/orgs/community/discussions/133133#discussioncomment-10443908  

Otherwise the error is persist:

```
remote: Invalid username or password.
fatal: Authentication failed for 'https://github.com/USER/REPO/'
```

This happens because:

1. > credential.helper=wincred

2. `Windows Credential Manager` has a single record:

  ```
  Address: git:https://github.com
  User: USER
  Pass: PASS
  Persistence: Enterprise
  ```

To fix this you must add the second record:

```
Address: git:https://USER@github.com
User: USER
Pass: PASS
Persistence: Local computer
```

> [!WARNING]
> This must be with persistence `Local computer`, otherwise won't work!

> [!WARNING]
> This can not be added through the `Windows Credential Manager` nor
> `cmdkey.exe` utility.

> [!NOTE]
> The details how to add through the PowerShell:  
> https://serverfault.com/questions/920048/change-persistence-type-of-windows-credentials-from-enterprise-to-local-compu

> [!NOTE]
> Scripts to add the second record:  
> https://github.com/andry81/contools--admin/tree/HEAD/scripts/Wincred

Another fix would be to install the `Git Credential Manager`:  
https://github.com/git-ecosystem/git-credential-manager  

The installation does update `Windows Credential Manager` records once and
changes the Git credential helper variable:

> credential.helper=manager

> [!WARNING]
> The variable `credential.helper=manager` is required to update the
> `Windows Credential Manager` records on each Git command call within the Git
> authentication attempt.

> [!NOTE]
> The `Git Credential Manager` adds and updates the second record automatically
> in the installation and after.

> [!NOTE]
> If the GitHub PAT is expired, then `Git Credential Manager` automatically
> removes it from the `Windows Credential Manager` records list.
