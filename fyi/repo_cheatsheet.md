# Local Mozilla checkout cheatsheet

...in no particular order. There are a million things to know/learn with Mozilla
repo tooling, but the features and tricks covered in this cheatsheet are used
fairly frequently.

## Discard unwanted changes

Discard both staged and unstaged changes to a file:

```sh
git restore --staged --worktree <filename>
```

...or pick which changes to discard, interactively:

```sh
git restore -p <filename>
```

## Rebase a patch to the tip of comm-central

```sh
moz-phab patch <phabricator ID>
git fetch origin
git rebase origin/main
moz-phab
```

## Submit a patch to an existing Phabricator revision

1. Run `git commit --amend`.
2. Add the revision URL to the extended commit message:

   ```text
   Differential Revision: https://phabricator.services.mozilla.com/<phabricator ID>
   ```

## Manually port an incompatible patch to the repo

1. Make the changes to the repo.
2. Stage everything, including new and deleted files:

   ```sh
   git add -A
   ```

3. Commit with the message from the top of the patch file.
4. _Make sure to credit the original patch author!_

   ```sh
   git commit --amend --author 'Bender Rodríguez <imfortypercent@proton.me>'
   ```

## Amend a commit

For the most recent commit:

1. Make the changes.
2. Run `git commit --amend -a`.
3. Run `moz-phab`.

For an older commit:

1. Make the changes.
2. Commit them as a fixup, then fold it into the original commit:

   ```sh
   git commit -a --fixup <commit hash>
   git rebase -i --autosquash <commit hash>~
   ```

3. Run `moz-phab`.

## Lint and fix changes

```sh
./mach commlint --fix -n <list of files>
```

## Back out a commit

```sh
git revert --no-commit <commit hash>
git commit -m "Backed out changeset <commit hash> (bug <bug #>) rs=backout a=<your username>"
```

## Push a bustage fix

```sh
git commit -m "No bug - <explanation> rs=bustage-fix a=<your username>"
```

## Generate an optimized taskgraph

```sh
./mach taskgraph optimized -v -p project=comm-central --root=comm/taskcluster
```

## Useful git commands to know

| Command              | What it does                                              | Mercurial equivalent |
| -------------------- | --------------------------------------------------------- | -------------------- |
| `git commit --amend` | Change the most recent commit                             | `hg amend`           |
| `git rebase -i`      | Reorder, edit, squash or drop commits                     | `hg histedit`        |
| `git clean -fd`      | Delete untracked files (use `-n` first for a dry run)     | `hg purge`           |
| `git reflog`         | Find earlier states of a branch, to recover from mistakes | `hg oops`            |
