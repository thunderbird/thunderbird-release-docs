# DETAILED TEST INSTRUCTIONS

## A. Functional testing in new profile

**Use a non-English Thunderbird if possible**

1. (ideally) Do a clean (custom) install of candidate build, in a new user account on your OS (or in a VM), into a NEW program directory, and using a new Thunderbird profile. (On Windows, try a Windows user account that does NOT have admin privileges.)
2. Check Help | About that you are using the correct version, and no updates are offered.
3. If you are not testing build#1 of a release, at Help | More Troubleshooting Information check build ID - it should match date of build#n. Click on a couple items in the Troubleshooting page.
4. Mail - add a pop and an imap account, send from each account to the other, check that you can get+receive+reply to mail for both accounts.
5. Menu navigation - mouse over every menu, and also test submenus randomly of:
    - menu toolbar (View | Toolbars | Mail Toolbar) - File, Edit, View, Go, Message, Tools, Help, etc of 3-pane and compose window
    - hamburger menu (top right)
    - meatball menu (over folder pane)
    - context menus
6. Add-ons - install and use an add-on from a file downloaded from ATN or installed via Tools > Add-ons. The following add-ons should work in both beta and nightly:
    - <https://addons.thunderbird.net/en-US/thunderbird/addon/signature-switch/>
    - <https://addons.thunderbird.net/en-US/thunderbird/addon/profile-switcher/>
    - <https://addons.thunderbird.net/en-US/thunderbird/addon/markdown-here-revival/>
    - <https://addons.thunderbird.net/en-US/thunderbird/addon/gmail-conversation-view/> (an old standard, but may not always work on beta)
7. Themes: try the light and dark theme at Add-ons > Themes, perhaps <https://addons.thunderbird.net/en-US/thunderbird/addon/darkreader/>
8. (optional) RSS - add account, add a few feeds [4], see that you can read them.
9. (optional) News - add a news account, subscribe to a test newsgroup, post to the test newsgroup (note, your email address will become public unless you use a fake address).
10. (optional) Chat - if you are familiar with chat, please test some basic functions.
11. (optional) Calendar - Open calendar using spaces toolbar on the left. If you have no calendars create one. Create one event with at least one attendee other than yourself, and check that the attendee received the email invite. Check more functionality if you have time. If possible use the testcase attached to bug 1850732 which will cause hang on unfixed builds (extract and import to calendar).

### More in depth tests

1. Attachments: work with attachments - adding, opening, saving:
    - try attaching 10 small ones - send, read, and open
    - try attaching 5 large ones (for example 1mb) - send, read, and open
2. Message movement actions, using the messages with attachments above:
    - copy messages to local folder, make sure there is no corruption of the local folder at middle, and both ends
    - filter messages to local folder, make sure there is no corruption of the local folder at middle, and both ends
    - archive message
    - undo, redo delete message
3. Message compose: check that these work well: addressing UI, editing addresses, address autocomplete, LDAP lookup, reply to all and forward to all, contacts sidebar (F9).
4. Folder actions: delete, move, add, rename.
5. Force a crash, see that it shows up in Help > Troubleshooting, and that clicking the link gets to crash-stats.mozilla.com.
6. Check Help menu, and Help About ... that all links work, including what's new (however, release notes typically don't work for candidate builds, and/or may not yet be live).
7. Printing - print single message, and multiple messages.
8. (optional) OpenPGP. Setup Wizard:
    - Having 2 email accounts, 1 with OpenPGP configured, the other without any key.
    - Create a new Key, select it, and switch to the e2e page of the other account to confirm:
        - Notifications are cleared.
        - The new key only appears in the other account.
        - The key change doesn't affect other accounts.
    - Export both the public and private parts of the newly created key, separately to have 2 files.
    - Delete the newly created key.
    - Test the import of both public and private keys.
    - Test the set up of an external GnuPG key.

    Expiry date:
    - If you have a key that has expired, confirm the inline alert and inability to select it.
    - Update the expiration key.

    Encryption:
    - Encrypt a message, no signature.
    - Encrypt a message + signature.
    - Encrypt a message and attach public key.
    - Encrypt message, no public key.
    - Try to send the message to recipients with known and unknown public keys.

    Decryption:
    - Read an encrypted message sent to my currently valid key.
    - Confirm unable to read an encrypted message sent to a key I no longer use.
    - Confirm the icons reflect the correct message regarding encryption and signature status in the security popup pane.
    - Confirm the signature icon updates if the signature acceptance level is updated.

## B. Update testing - to test an update channel, change channel-prefs.js

- Channel choices are release, beta and nightly.
- Start using some previous version - copied from an existing program directory of the same channel, or install a previous release of the same channel.
- Add "-localtest" to channel name in program folder, edit `<apppath>/defaults/pref/channel-prefs.js` (you may need to add file write permissions), examples: beta-localtest, release-localtest, nightly-localtest.
- On Mac use framework folder instructions at <https://bugzilla.mozilla.org/show_bug.cgi?id=1883089#c19>
- Start Thunderbird.
- Do Help -> About to make Thunderbird check for the update. Restart when prompted.
- Check:
  - updated version starts
  - check version in Help -> About or application build ID at Help | Troubleshooting that it matches what you think it should have updated to

## Notes

[1] **Tricks to save time**

- On Windows, create a non-admin account at Control Panel\All Control Panel Items\User Accounts\Manage Accounts. Do your testing in that new (Windows) account.
- Download at same time both new and previous Thunderbird(s) from the archives.
- Keep previous download installation files for future use.
- Use `thunderbird -P` to create+test profiles separate from your production profile.
- Open Account Settings just once and create all accounts at the same time: imap, pop, news, and rss (for news, don't use your real email).
- Ask for help at <https://chat.mozilla.org/#/room/#maildev:mozilla.org>

[2] **Downloads**

- For past releases see <https://archive.mozilla.org/pub/thunderbird/releases/>
- For the specific candidate to be tested, see links at top of this message.

[3] **Test accounts** - You need not use your production profile/mail accounts. You might create test imap accounts on gmail.

[4] **Feed URL instructions and examples**

- Click Blogs & News Feeds, then Manage Subscriptions.
- Paste a URL (examples below) into Feed URL, click add, leave the dialog open.
- In Feed Subscriptions (open from previous step) click Blogs & News Feeds.
- Paste a URL into Feed URL, click add.
- Click Close.
- Examples:
  - <http://planet.mozilla.org/thunderbird/rss20.xml>
  - <http://rss.slashdot.org/Slashdot/slashdot>
  - <http://www.ghacks.net/feed/>
