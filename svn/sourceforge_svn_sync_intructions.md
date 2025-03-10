* sourceforge_svn_sync_intructions.md
* 2025.03.10

1. DESCRIPTION  
2. OFFICIAL DOCUMENTATION  
3. SERVER CREDENTIALS  
4. LOGIN CONSOLE COMMANDS  
5. SERVER CONSOLE COMMANDS  
5.1. svnadmin  
5.2. tar  
5.3. git  
6. SERVER URL COMMANDS  
7. CLIENT CONSOLE COMMANDS  
7.1. rsync  
7.2. svnrdump  

-------------------------------------------------------------------------------
1. DESCRIPTION
-------------------------------------------------------------------------------
The `sourceforge.net` SVN synchronization instructions.

-------------------------------------------------------------------------------
2. OFFICIAL DOCUMENTATION
-------------------------------------------------------------------------------

https://sourceforge.net/p/forge/documentation/Docs%20Home/  

https://sourceforge.net/p/forge/documentation/rsync/  
https://sourceforge.net/p/forge/documentation/svn/  
https://sourceforge.net/p/forge/documentation/SVN%20Import/  
https://sourceforge.net/p/forge/documentation/SSH/  

-------------------------------------------------------------------------------
3. SERVER CREDENTIALS
-------------------------------------------------------------------------------

### Login addresses

* {USER}@frs.sourceforge.net
* {USER}@shell.sourceforge.net

### SSH key type

* ssg-rsa

### Authentication

* passowrd + verification code

> [!NOTE]
> For the verification code use the android application: `2FAS Auth`.

### Known paths

* frs.sourceforge.net
  * /home/users/{USER}
  * /home/pfs/project/{PROJECT}
  * /home/pfs/svn/p

* shell.sourceforge.net
  * /home/users/{USER}
  * /home/users/N/NN/{USER}
  * /home/svn/p/{PROJECT}

-------------------------------------------------------------------------------
4. LOGIN CONSOLE COMMANDS
-------------------------------------------------------------------------------

### SSH

```cmd
ssh -t {USER}@shell.sourceforge.net create
```

-------------------------------------------------------------------------------
5. SERVER CONSOLE COMMANDS
-------------------------------------------------------------------------------

-------------------------------------------------------------------------------
5.1. svnadmin
-------------------------------------------------------------------------------

### SVN bare database dump into file

```cmd
svnadmin dump /home/svn/p/{PROJECT}/{REPO} > /home/users/{USER}/{REPO}-old.dump
```

### SVN bare database recreation

```cmd
rm -rf /home/svn/p/{PROJECT}/{REPO}  
svnadmin create --compatible-version 1.9 /home/svn/p/{PROJECT}/{REPO}
```

### SVN bare database load from dump file

```cmd
svnadmin load /home/svn/p/{PROJECT}/{REPO} < /home/users/{USER}/{REPO}-new.dump
```

-------------------------------------------------------------------------------
5.2. tar
-------------------------------------------------------------------------------

### SVN dump file compress into archive

```cmd
tar --xz -cf {REPO}-old.dump.tar.xz {REPO}-old.dump
```

### SVN bare database extract

```cmd
tar -xf {REPO}-new.dump.tar.xz -C /home/svn/p/{PROJECT}
```

### SVN dump file extract

```cmd
tar -xf {REPO}-new.dump.tar.xz {REPO}-new.dump
```

-------------------------------------------------------------------------------
5.3. git
-------------------------------------------------------------------------------

### Enable not fast-forward force push

```cmd
git config receive.denynonfastforwards false
```

OR

Use scripts:

* https://github.com/andry81/gitcmd/tree/HEAD/scripts/git_bare_config_allow_rewrite.sh
* https://github.com/andry81/gitcmd/tree/HEAD/scripts/git_bare_config_deny_rewrite.sh

-------------------------------------------------------------------------------
6. SERVER URL COMMANDS
-------------------------------------------------------------------------------

### Repository refresh

> https://sourceforge.net/p/{PROJECT}/{REPO}/refresh

-------------------------------------------------------------------------------
7. CLIENT CONSOLE COMMANDS
-------------------------------------------------------------------------------

-------------------------------------------------------------------------------
7.1. rsync
-------------------------------------------------------------------------------

### Download and sync

```cmd
rsync -rvz --delete-excluded --chmod=Du=rwx,Dgo=rx,Fu=rw,Fgo=r svn.code.sf.net::p/PROJECTNAME/REPOSITORY .
```

### Upload and sync

```cmd
rsync -rvz --delete-excluded . svn.code.sf.net::p/PROJECTNAME/REPOSITORY
```

-------------------------------------------------------------------------------
7.2. svnrdump
-------------------------------------------------------------------------------

### SVN remote repository dump into file

```cmd
svnrdump dump https://svn.code.sf.net/p/{PROJECT}/{REPO} > {REPO}-old.dump
```
