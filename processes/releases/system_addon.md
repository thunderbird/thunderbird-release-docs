# Built-in and System Add-On Release Process

1. Land the new version as a built-in add-on to Thunderbird `main`

2. Release the new version as a system add-on to daily

3. Beta:
   1. Release the new version as a system add-on to beta
   2. Uplift the built-in add-on to Thunderbird beta **only** to resolve
      high-impact issues (fallback for users who don't have access to the system
      add-on)

4. Await go/no-go criteria from beta testing

5. Release/ESR:
   1. Release the new version as a system add-on to release/esr
   2. Uplift the built-in add-on to Thunderbird release/esr **only** to resolve
      high-impact issues (fallback for users who don't have access to the system
      add-on)

# How to Create a New Release

1. Connect to the Mozilla VPN

2. Go to [Ship-It](https://shipit.mozilla-releng.net/)
	* Open the **Releases** menu
	* Open the **Extensions** submenu
	* Click **New**

3. Select `thundermail` in the **Extension** drop-down menu

4. Select the revision you'd like to release in the **Revision** drop-down menu

5. Click **CREATE RELEASE**
	* Click **SUBMIT**
	* Click **CLOSE**
	* Ship-It will then take you to the Pending Releases page

6. On the release you just created, click **Build**, followed by **SCHEDULE**
	* Once the build is running, you can check it in Taskcluster by clicking **Build** again

7. Once the build is complete, click "Ship" and sign off
	* *Two authorized users will need to do this*

8. Go to [Balrog](https://balrog.mozilla.org/)
	* In the product drop-down, select thundermail

9. Click **RULES** in the top-right of the screen

10. Select "TBSystemAddons" in the **Product : Channel** drop-down

11. Click **UPDATE** on the channel rule you'd like to release to

12. Update **Mapping** field with the name of your new release
	* This field uses autocomplete, so your new release should show up when you start typing

10. Click 💾 in the bottom-right of the screen