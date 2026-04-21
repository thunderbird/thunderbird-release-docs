# Language Packs

## Checking Langpack Status

### Script (by John Bieling)

A script is available to check langpacks for a specific version:
https://gist.github.com/jobisoft/7a11996e4b374242d4f65d871957a70e

Example usage:

```
node check_lang.mjs --version 150.0 --default-only
```

### Checking ATN Directly

You can also check [ATN](https://addons.thunderbird.net/en-US/thunderbird/addon/tb-langpack-be/versions/) directly to verify whether a langpack is up-to-date.

## Known Issues

As of this writing, our push langpack tasks run successfully, but ATN sometimes fails to publish some langpacks. Until we can automate the verification, manually check that all langpacks were published after each release.

## Re-running a Langpack Task

If a langpack task needs to be re-run, you have to "Add new jobs" in Treeherder and specify the job(s), for example:

```
push-langpacks-shippable-l10n-linux64-shippable-2/opt
```
