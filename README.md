# eXgit
JGit-based plugin for eXist-DB

provides XQuery access to git

Currently, this is in an early beta state. **Use at your own risk!** Especially, test on a non-productive system before anything else. Please report all issues you may find.

## Build from Source

1. clone the Repo
2. cd into repo's rootdirectory
3. run `mvn clean package`

## Install
### Option 1: use the xar
If maven runs successfully, the project root should contain a file named `exgit.xar` which can be deployed directly via the package manager.
If this does not work, try option 2.


### Option 2: copy and install manually
The packaged version includes all third-party JARs necessary to run eXgit. By using it you agree to the licenses under which these software packages are published;
all licenses can be found in `THIRD-PARTY.TXT`.

1. copy `target/exgit-full.jar` into your exist-db home directory `$eXist-db HOME$/lib/user`
2. register eXgit module in `$exist-db HOME$/conf.xml`. Within `exist/xquery/builtin-modules` add:
    `<module uri="http://exist-db.org/xquery/exgit" class="org.exist.xquery.modules.exgit.Exgit"/>`
1. restart eXist.

`target/exgit.jar` does not contain any dependencies; you are responsible to provide all dependencies for the module to work.

## Usage
The very first step is to import the module:

    import module namespace exgit="http://exist-db.org/xquery/exgit" at "java:org.exist.xquery.modules.exgit.Exgit";

If all went well, eXide should be offering content completion while eXist's function documentation should contain
a complete documentation of the module (mind you that it might be necessary to rebuild the documentation after adding
or upgrading the module).

Currently, the following functions are available:

1. `exgit:export($repoDir, $collection)` – write `$collection` from the database to the local git repo `$repodir`
1. `exgit:commit($repoDir, $message)` – equal to `git commit -a -m'$message'` invoked in  `$repodir`
1. `exgit:commit($repoDir, $message, $authorName, $authorMail)` – same as above, explicitly setting the author
1. `exgit:push($repoDir, $remote, $username, $password)` – push to `$remote` (e.g. 'origin') supplying your user credentials
1. `exgit:pull($repoDir, $remote, $username, $password)` – pull from `$remote` (e.g. 'origin') supplying your user credentials
1. `exgit:import($repoDir, $collection)` – read XML, CSS, XQuery and JS from the repo and store them in the given collection
1. `exgit:clone($repo, $repoDir)` – clone a remote $repo into local $repoDir
1. `exgit:clone($repo, $repoDir, $branch)` – clone $branch from remote $repo into local $repoDir
1. `exgit:checkout($repoDir, $commit)` – checkout $commit from local $repoDir
1. `exgit:tags($repository)` – list all tags in $repository; if it starts with “http” or “ssh“, a list of tags in the remote repo ist returned; else, it is assumed to point to a local repo and a fetch is executed prior to listing

### Example script - to run in eXide

```XQuery
xquery version "3.1";
import module namespace exgit="http://exist-db.org/xquery/exgit" at "java:org.exist.xquery.modules.exgit.Exgit";

let $repoDir := "C:\Users\user\Desktop\gittest"
let $collection := "/db/apps/dsebaseapp/data"
let $export := exgit:export($repoDir, $collection)
let $message := "changes in data"
let $commit := exgit:commit($repoDir, $message)
let $push := exgit:push($repoDir, "origin", "username", "password")
let $pull := exgit:pull($repoDir, "origin", "username", "password")
let $import := exgit:import($repoDir, $collection)

return $import
```

## Caveats and future
Currently, HTTP is used as transport and the user credentials have to be supplied within the XQuery.
One of the next steps is to a) add SSH as a mode of transport and b) support to store credentials
or an SSH key within the collection configuration so that sensitive information does not have to be
included in the XQuery as plain text.

## Legal Notice

In the fully packed ('shaded') jar ('-with-dependencies.jar'), third party software is included.  All licenses can be found in `THIRD-PARTY.TXT`.