# Desktop smoke test

Detailed test instructions for a Thunderbird desktop release candidate. See
[Tips](#tips) at the bottom for ways to save time, where to download builds, and
test accounts.

- [A. Functional testing in a new profile](#a-functional-testing-in-a-new-profile)
  - [More in-depth tests](#more-in-depth-tests)
- [B. Update testing](#b-update-testing)
- [Tips](#tips)
  - [Saving time](#saving-time)
  - [Downloads](#downloads)
  - [Test accounts](#test-accounts)
  - [Feed URLs](#feed-urls)

> [!NOTE] Use a non-English Thunderbird build if possible.

## A. Functional testing in a new profile

1. (Ideally) do a clean (custom) install of the candidate build, in a new user
   account on your OS (or in a VM), into a NEW program directory, and using a
   new Thunderbird profile. On Windows, try a user account that does NOT have
   admin privileges.
2. Check in **Help > About** that you are using the correct version, and that no
   updates are offered.
3. If you are not testing build 1 of a release, check the build ID at **Help >
   More Troubleshooting Information**. It should match the date of build n.
   Click on a couple of items in the **Troubleshooting** page.
4. Mail: add a POP and an IMAP account, send from each account to the other, and
   check that you can get, receive and reply to mail for both accounts.
5. Menu navigation: mouse over every menu, and randomly test submenus of:
   - the menu toolbar (**View > Toolbars > Mail Toolbar**): **File**, **Edit**,
     **View**, **Go**, **Message**, **Tools**, **Help**, etc. of the 3-pane and
     compose windows
   - the hamburger menu (top right)
   - the meatball menu (over the folder pane)
   - context menus
6. Add-ons: install and use an add-on, either from a file downloaded from ATN or
   via **Tools > Add-ons**. These add-ons should work in both beta and nightly:
   - [signature-switch](https://addons.thunderbird.net/en-US/thunderbird/addon/signature-switch/)
   - [profile-switcher](https://addons.thunderbird.net/en-US/thunderbird/addon/profile-switcher/)
   - [markdown-here-revival](https://addons.thunderbird.net/en-US/thunderbird/addon/markdown-here-revival/)
   - [gmail-conversation-view](https://addons.thunderbird.net/en-US/thunderbird/addon/gmail-conversation-view/)
     (an old standard, but may not always work on beta)
7. Themes: try the light and dark themes at **Add-ons > Themes**, and perhaps
   [darkreader](https://addons.thunderbird.net/en-US/thunderbird/addon/darkreader/).
8. (Optional) RSS: add an account, add a few feeds (see
   [Feed URLs](#feed-urls)), and check that you can read them.
9. (Optional) News: add a news account, subscribe to a test newsgroup, and post
   to it. Your email address will become public unless you use a fake one.
10. (Optional) Chat: if you are familiar with chat, test some basic functions.
11. (Optional) Calendar: open the calendar from the spaces toolbar on the left.
    If you have no calendars, create one. Create an event with at least one
    attendee other than yourself, and check that the attendee received the email
    invite. Check more functionality if you have time. If possible, import the
    testcase attached to
    [bug 1850732](https://bugzilla.mozilla.org/show_bug.cgi?id=1850732), which
    causes a hang on unfixed builds.

### More in-depth tests

1. Attachments: adding, opening, saving:
   - attach 10 small files, then send, read and open them
   - attach 5 large files (for example 1 MB each), then send, read and open them
2. Message actions, using the messages with attachments above:
   - copy messages to a local folder, and make sure the local folder isn't
     corrupted at the start, middle or end
   - filter messages to a local folder, and make sure the local folder isn't
     corrupted at the start, middle or end
   - archive a message
   - delete a message, then undo and redo
3. Compose: check that these work well: the addressing UI, editing addresses,
   address autocomplete, LDAP lookup, reply to all and forward to all, and the
   contacts sidebar (F9).
4. Folder actions: delete, move, add, rename.
5. Force a crash, and check that it shows up in **Help > Troubleshooting
   Information** and that clicking the link opens crash-stats.mozilla.org.
6. Check that all links in the **Help** menu and **Help > About** work,
   including **What's New**. Release notes usually don't work for candidate
   builds, and may not be live yet.
7. Printing: print a single message, and multiple messages.
8. (Optional) OpenPGP.

   **Setup wizard**, with 2 email accounts, 1 with OpenPGP configured and the
   other without any key:
   - Create a new key, select it, and switch to the end-to-end encryption page
     of the other account to confirm:
     - Notifications are cleared.
     - The new key only appears in the other account.
     - The key change doesn't affect other accounts.
   - Export the public and private parts of the new key separately, to get 2
     files.
   - Delete the new key.
   - Import both the public and private keys.
   - Set up an external GnuPG key.

   **Expiry date:**
   - If you have an expired key, confirm the inline alert and that you can't
     select it.
   - Update the key's expiration date.

   **Encryption:**
   - Encrypt a message, no signature.
   - Encrypt a message and sign it.
   - Encrypt a message and attach your public key.
   - Encrypt a message with no public key.
   - Send a message to recipients with both known and unknown public keys.

   **Decryption:**
   - Read an encrypted message sent to your currently valid key.
   - Confirm you can't read an encrypted message sent to a key you no longer
     use.
   - Confirm the icons in the security popup reflect the correct encryption and
     signature status.
   - Confirm the signature icon updates when the signature acceptance level is
     changed.

## B. Update testing

To test an update channel, change `channel-prefs.js`. The channels are
`release`, `beta` and `nightly`.

1. Start from a previous version of the same channel: copy an existing program
   directory, or install a previous release.
2. Add `-localtest` to the channel name in
   `<apppath>/defaults/pref/channel-prefs.js`, for example `beta-localtest`,
   `release-localtest` or `nightly-localtest`. You may need to add write
   permissions to the file. On Mac, follow the framework folder instructions in
   [bug 1883089](https://bugzilla.mozilla.org/show_bug.cgi?id=1883089#c19).
3. Start Thunderbird.
4. Open **Help > About** to make Thunderbird check for the update. Restart when
   prompted.
5. Check that:
   - the updated version starts
   - the version in **Help > About**, or the build ID in **Help >
     Troubleshooting Information**, matches what it should have updated to

## Tips

### Saving time

- On Windows, create a non-admin account at **Control Panel > All Control Panel
  Items > User Accounts > Manage Accounts**, and do your testing in it.
- Download the new and previous Thunderbird builds from the archives at the same
  time.
- Keep previous installation files for future use.
- Use `thunderbird -P` to create and test profiles separate from your production
  profile.
- Open **Account Settings** just once and create all accounts at the same time:
  IMAP, POP, news and RSS (for news, don't use your real email).
- Ask for help at <https://chat.mozilla.org/#/room/#maildev:mozilla.org>.

### Downloads

- For past releases, see
  <https://archive.mozilla.org/pub/thunderbird/releases/>.
- For the candidate being tested, see the links in the testing request.

### Test accounts

You don't need to use your production profile or mail accounts. You can create
test IMAP accounts on Gmail, for example.

### Feed URLs

1. Click **Blogs & News Feeds**, then **Manage Subscriptions**.
2. Paste a feed URL (see the examples below) into **Feed URL**, click **Add**,
   and leave the dialog open.
3. In **Feed Subscriptions**, click **Blogs & News Feeds**.
4. Paste another URL into **Feed URL** and click **Add**.
5. Click **Close**.

Example feeds:

- <http://planet.mozilla.org/thunderbird/rss20.xml>
- <http://rss.slashdot.org/Slashdot/slashdot>
- <http://www.ghacks.net/feed/>
