## Configure your own github environment

These are the steps you would take to configure a fork of thunderbird-android
in order to run the shippable_builds workflow.

You won't be able to run all of the workflow jobs of course. For example you
can't upload to FTP or the play store.

- Generate keys and put them in a directory on your machine:
  - `keytool -genkey -v -keystore debug.keystore -storepass android -alias androiddebugkey -keypass android -keyalg RSA -keysize 2048 -validity 10000`
    - taken from
      https://coderwall.com/p/r09hoq/android-generate-release-debug-keystores
  - create `debug.properties` file with the following contents:
      ```
      keyAlias=androiddebugkey
      keyPassword=android
      storePassword=android
      ```
  - for each of the .properties and .jks files in setup_release_automation, create a symlink: e.g.
      `ln -s debug.properties k9.release.signing.properties ln -s debug.keystore k9.release.signing.properties`
- Create and export a classic token according to
  https://github.com/settings/tokens
  - `export GITHUB_TOKEN=<token>`
- Create the release environment in your fork:
  - `python3 ../thunderbird-android/scripts/ci/setup_release_automation -r <your-username>/thunderbird-android`
