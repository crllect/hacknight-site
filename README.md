# **__Wsg__**  <img src="https://komarev.com/ghpvc/?username=crllect&color=e70052&style=for-the-badge&label=Bugs+In+My+Code" alt="crllect" />
<!-- used colors: 24410c, e70052, 25252a, 141414, ce244c -->
# SKIOVOX Writeup

**I would like to preface with the fact that I did not create SKIOVOX, however, I am developing a PoC (Proof of Concept) chrome extension to show how this could be abused in chromeOS**

---

## Summary:

SKIOVOX allows for an unblocked browser running as root in a seperate partition of the drive while in any kiosk app. Each app having its own partition. Along with being able to be used for playing games, it can also access the internet during any kiosk based standerdized test, which for us, would be both the SAT and the MCAS.


# Stage 1: Starting the exploit

### Note

Before you start, you must be properly connected to a WiFi network that has automaticly connect enabled.

### Step 1.1

First, sign out or restart your Chromebook to get to the login screen. Then, turn off your Wi-Fi using the control panel in the bottom right (can be opened using `alt - shift - s`, important later). Don’t forget or disconnect from any networks, just turn it off completely. This may not work if the policy dictates forced conenction to any given network, so you may need to force a disconnect via other means.

### Step 1.2

If prompted to enter a password (which will happen on certain older models of chromebooks), enter one. If having no internet breaks this, powerwash, then do not login initially.

### Step 1.3

Click on one of the apps in the “apps” section.

- Most of them work with this exploit
- Every app will esentially function with its own partition of the drive
- While when running TOP or any other command make it seem like the kiosk app has root privaleges, the device is still managed, and cannot enable linux.
  - You can enable linux storage by attempting to run any debian installer, then just hitting cancel. You can now right click on any folder and add it linux shared storage.

## **Instantly after you click on any app, do `alt+shift+s`** 

If you did it fast enough, the quick settings should appear. If it didn’t, logout or restart your chromebook.
If done correctly, you have partially loaded elements of standard ChromeOS into a kiosk environment.
### Step 1.4

Wait until you get a “network unavailable” screen.

### Step 1.5

The quick settings should still be open. Click accessibility, then click the question mark. The toolbar will now close. If you couldn’t do this step, try again with another app.

## The next steps will very from device to device, version to version and policy to policy.

If you see a “back” button on the network error page:

- Go to stage 2A

Otherwise:

- Go to stage 2B or 2C

They have different steps!

### Note

You should have already typed in your password (step 1.2) by the time you’re here. Remember that the steps in stage 2A are only for users who see a back button on the network error page.

# Stage 2A: For users with a back button

### Step 2A.1

Click “add other WiFi network”. Don’t type anything in. Instead, immediately:

- Press the escape key twice, which should bring you to the login screen with your password still typed in
- Press enter to log in

These steps must be done within ~4 seconds after pressing the “add network” button.

### Step 2A.2

You may see a screen saying “multiple sign-in is disabled.” If you get this, simply press the escape key on your keyboard to close this menu, nice job google.

### Step 2A.3

There may be an open window belonging to your school profile. This window will have your filter extensions installed. Close this window if you like. There should be another one behind it.

### Step 2A.4

You should be able to see a window with no managed extensions installed. This window is slightly bugged. To fix this, click the three dots in the upper right corner of Chrome and select “new window”. Use this window instead.

# Stage 2B: For users without a back button

### Step 2B.1

Press the “diagnose” button. The diagnostics window will now open, although you shouldn’t be able to see it.

### Step 2B.2

Click “add other WiFi network” to turn your WiFi back on. Don’t type anything in, just wait until the diagnostics page shows up.

This is known to be inconsistent; try a few times with a few apps or try 2C.

 - **Note**: Certain apps may be more bugged then others. During my time messing with this exploit, I found out a lot of interesting things about how the chromeOS kiosk systme works, and just how broken it is. In CPSD, the support.google.com page will be blocked, dont know why. This may be the case with other districts as well, will require more extensive testing.

### Step 2B.3

Click WiFi, then click the settings hyper link. Settings should now open.

### Step 2B.4

A settings window will appear with a chrome window behind it. Policies still apply to settings, but setting the behind window to floating will hide the settings window from view. Dont know why either, but I like to keep it open. While attempting other nested exploits, I've noticed that the settings window is not considered a window, but a popup. Most likely created with some kind of `window.create()`. Thats only my best guess though. The behind window shouldn’t have any district’s extensions installed. Openning terminal will fail to load because it cant load any environment. I already tried using crostini and linux. Sadly to no avail. However, you can make empty virtual containers with alotted sizes. In other partitions of the drive, all of the activity will be listed under `system`, which is pretty funny. Openning crosh and running any kind of command, i.e. whoami, top, will reveal that you are running as root. And that no process is running under chronos. Which is the chromeOS user profile.

# Stage 2C: Also for users without a back button

### Step 2C.1

Click “add other WiFi network” to turn your WiFi back on. Don’t type anything in, just wait until the kiosk app loads.

### Step 2C.2

Press `ctrl+alt+z` to open text-to-speech. This may be blocked for you. Note that a noise will likely be made when you run this shortcut.

 - **Note**: The original version of this exploit relied soly on text-to-speech (chromeVox). Thats where the VOX part came from in SKIOVOX.

### Step 2C.3

Hold the search key. Press the O key, then the T key (Not simultaniosly). A tutorial window should appear for chromeVox.

### Step 2C.4

Click “resources”. Three links will show up; you can click any of them. Your browser should now open.

Once your browser is open, you can turn off chromeVox spoken feedback by running `ctrl+alt+z` again.

---

# Stage 3: Making the experience smoother

### Issues with this exploit

- It’s unclear how to add a Google account and install extensions
- Most keyboard shortcuts don’t work
- It’s hard to move or resize the window(s)
- General quirkeness

### The solution

The current maintainer of the exploit made the Skiovox Helper extension, which restores all of this functionality: [https://github.com/bypassiwastaken/skiovox-helper](https://github.com/bypassiwastaken/skiovox-helper)

 - **Note**: Because the browser itself is running under kiosk privileges, it can turn on developer mode for certain chrome services, not for the entire device. This is powerful enough to install unpacked chrome extensions, such as the one listed above.

### Other notes

The main difference between the stages is:

- 2A can open multiple windows
- 2B and 2C cannot

If your screen keeps falling asleep after five seconds, try using another kiosk app. Every app has its own extensions, history, settings, etc. This is part of how kiosks in chrome work. All different aspects are different layered windows. For example, college board has 4 windows open. The main college board window is basically just a webview, and the block screen is just an html button that when clicked puts the main window into focus. Its considered an app by chromeOS, and in order to have permission to move windows and change focus, it also has an extension installed with all permissions. Pretty interesting.

### Opening device settings

If you only want to edit network settings, try `chrome://network#select`.

Again, device settings are mostly useless because device policies still apply, but…

#### If using 2A:

- Click the settings icon instead of the help icon on step 1.5

#### If using 2B or 2C:

- Simply go to `chrome://os-settings`
