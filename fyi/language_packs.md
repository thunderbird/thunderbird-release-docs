# Language Packs

## Checking Langpack Status

### tb-release check-langpacks

`tb-release check-langpacks`, from
[thunderbird-release-tools](https://github.com/thunderbird/thunderbird-release-tools/tree/main/releases/atn),
checks the brand name and upload date of the langpack ATN offers by default
for a Thunderbird version, for each locale. It was ported from a script by
John Bieling. See the
[thunderbird-release-tools README](https://github.com/thunderbird/thunderbird-release-tools#tb-release)
to install `tb-release`.

Example usage:

```
tb-release check-langpacks --version 150
```

### Checking ATN Directly

You can also check
[ATN](https://addons.thunderbird.net/en-US/thunderbird/addon/tb-langpack-be/versions/)
directly to verify whether a langpack is up-to-date.

## Known Issues

As of this writing, our push langpack tasks run successfully, but ATN sometimes
fails to publish some langpacks. Until we can automate the verification,
manually check that all langpacks were published after each release.

## Re-running a Langpack Task

If a langpack task needs to be re-run, you have to "Add new jobs" in Treeherder
and specify the job(s), for example:

```
push-langpacks-shippable-l10n-linux64-shippable-2/opt
```
