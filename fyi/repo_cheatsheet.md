## Local Mozilla checkout cheatsheet

...in no particular order. There are a million things to know/learn with Mozilla
repo tooling, but the features and tricks covered in this cheatsheet are used
fairly frequently.

**Discard unwanted staged changes**

1. `hg revert -C <filename>`

- **...or for interactive mode**

  `hg revert -C -i`

**Rebase a patch to the tip of comm-central**

1. `moz-phab patch <phabricator ID>`
2. `hg rebase -s tip -d comm`
3. `moz-phab`

**Submit patch to existing Phabricator revision**

1. `hg metaedit`
2. Add revision URL to extended commit message

   ```less
   Differential Revision: https://phabricator.services.mozilla.com/<phabricator ID>
   ```

**Manually porting incompatible patch to repo**

1. Make changes to repo
2. `hg addremove`
3. Commit with message from top of patch file
4. *Make sure to credit the original patch author!*

	```bash
	hg commit --amend --user 'Bender Rodríguez <imfortypercent@proton.me>'
    ```

**Amending a changeset**

1. `hg up <changeset hash>`
2. Make changes
3. `hg amend`
4. `moz-phab`

**Lint and fix changes**

```bash
./mach commlint --fix -n <list of files>
```

**Backing out a changeset**

```bash
hg backout <revision> -m "Backed out changeset <revision> (bug <bug #>) rs=backout a=<your username>"
```

**Pushing a bustage fix**

```bash
hg commit -m "No bug - <explanation> rs=bustage-fix a=<your username>"
```

**Generating optimized taskgraph**

```bash
./mach taskgraph optimized -v -p project=comm-central --root=comm/taskcluster
```

**Useful Mercurial commands to know**

- `hg amend`
- `hg histedit`
- `hg purge`
- `hg oops`