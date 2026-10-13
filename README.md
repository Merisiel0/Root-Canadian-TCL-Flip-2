# Rooting of a Canadian TCL Flip 2
This page contains the results of my research and tentatives to root my Canadian TCL Flip 2.

Apparently, my model can't be rooted with the usual neutron.img and most documentation and tutorials I found were either not clear enough or had steps that simply didn't work with my model. I'd say the most recurring problem was the enabling of ADB by dialing `*#*#DEBUG#*#*` (`*#*#33284#*#*`), which simply doesn't work on this model.

After figuring it out, I decided to make this guide for two reasons. First, to help others, second, to get help for my next endeavour with this phone. If anyone is experienced with the Linux kernel, boot stages of computers and reverse engineering, read the [Next Endeavour](next-endeavour) section.

## Disclaimer
I have much experience on Linux and terminals and a strong distaste of Windows. This whole guide assumes you have Linux and knows a bit about terminals. I myself used a CachyOS KDE laptop for this.

## Phone Details
The Canadian TCL Flip 2 is a specific model, I've found few other people who attempted to crack it. If your phone's details match mine, then my method should work for you.

| Property         | Value    |
|------------------|----------|
| Model number     | 4058E    |
| Hardware version | 03       |
| Kernel version   | 4.14.186 |
| Software version | QK6V     |

## Downloads
You'll need the following files:
- [mtk-bootseq.py](https://gist.github.com/neutronscott/2e4179af74c2fadec101a184fbb6a89e)
- fastboot and adb (On Arch: sudo pacman -S android-tools)
- [The latest neutron.img by Ludolulu](https://github.com/neutronscott/flip2/issues/16#issuecomment-4034902594)
- [a4accessibility.apk, esfileexplorer.apk, mrepo.apk and reschanger.apk](https://mega.nz/folder/zLR2USCY#AKdEeXgjX8bljmgPPUABaw/folder/OagFjSqb)
- [F-Droid.apk](https://f-droid.org/en/packages/org.fdroid.fdroid/)
- [AuroraStore's apk](https://auroraoss.com/files) (Go into AuroraStore/Release)

These files are also available in this repository's *files* folder.

## Steps
1. Poweroff your phone and remove the battery.
2. Open a terminal and run `watch -n 0.2 "ls /dev | grep ACM"`
3. Plug the phone and notice you can see `ttyACM0` appear and disappear twice. This is the BROM stage of booting, followed by the preloader stage. If you put the battery back now, it'll continue the booting process with the Android OS. We want to interrupt that process so it instead enters fastboot.
4. Prepare this command in another terminal, DON'T RUN IT YET: `sudo python3 mtk-bootseq.py FASTBOOT /dev/ttyACM0`
5. Unplug and plug the phone in again. Wait for the first ttyACM0 to appear, then immediately start the previous command once it disappears. This is to run the command over the preloader stage and not the BROM stage.
6. Put the battery back into the phone, it should boot to a black screen showing `=> FASTBOOT`.
7. Make sure your computer detects it: `fastboot devices`
8. Enable flashing: `fastboot flashing unlock`
9. Flash neutronscott's image: `fastboot flash boot neutron.img`
10. Reboot your phone: `fastboot reboot`
11. Dial `*#*#217703#*#*` to bring up a list of apps and install Magisk.
12. Enable Zygisk in Magisk's settings. You might need scrcpy to click on the settings icon.
13. On the phone, download [ADBio's apk](https://f-droid.org/en/packages/io.github.yoshi1123.adbio/) from F-Droid's website. I recommend typing in `adbio` then navigating the UI instead of hand-typing the whole url.
14. Once downloaded, install it by clicking on the download notification, it won't work from the filesystem.
15. You should now be able to connect your phone (battery in and opened) to your laptop and see it by running: `adb devices`
16. Enable developper settings: `adb shell settings put global development_settings_enabled 1`
17. Next install a4accessibility.apk, esfileexplorer.apk, mrepo.apk and reschanger.apk using this command: `adb install <file_name>.apk`
18. Next you'll need app stores. F-Droid is great for open-source apps, but Aurora store has better support for proprietary apps. I recommend installing both. 

## Now what?
If you got here, congrats! Your phone is now rooted and you can install any app you want. Careful though, you only have one gigabyte of space and one gigabyte of ram, the more apps you download, the more space is taken and the more services eat up your ram. This also lowers how long your battery lasts. Don't let this keep you from trying things though, you can always uninstall what's too heavy.

I greatly recommend reading adb's help (`adb --help`) to discover what new powers you've just gained.

You can read the sources, some people change their resolution, others their screen density. Explore and discover what fits you best.

You can also explore your phone's internal file system with `adb shell <insert_command>`. Although I haven't discovered how to gain root access with this method yet...

There's this [pretty cool mouse](https://github.com/Offline-DC/FlipMouse) originally developped by Tyler Boni and now maintained by [Offline-DC](https://offline.community/).

## Sources
- [neutronscott's github repository](https://github.com/neutronscott/flip2/wiki)
- [Tyler Boni's guide](https://dev.to/tylerboni/escaping-the-digital-treadmill-how-to-daily-drive-a-flip-phone-in-2025-tcl-flip-2-17el)
- [DietDroid's guide](https://dietdroid.com/2025/06/18/tcl-flip-2-debugging-root-apk-installation/)
- [Johnny Stene's guide](https://web.archive.org/web/20250520104657/https://stene.xyz/flip2/)
- [walkingcak3's jtechforums thread](https://jtechforums.org/t/figuring-out-flashing-tcl-flip-2/4524?page=4)
- [TCL-hub's github issue in neutronscott's repository](https://github.com/neutronscott/flip2/issues/16#issuecomment-2199068574)
- [Megazip](https://mega.nz/folder/zLR2USCY#AKdEeXgjX8bljmgPPUABaw)

## Next Endeavour
Phone is rooted, cool. But wouldn't it be nice to have full control over it? And I mean **full** control. Having no knowledge about boot stages nor how reverse engineering works, I've been experimenting with AI to try and figure out a way to build and boot the Linux kernel on this phone. Unfortunately I don't have much time at the moment to learn these things, I'll probably come back one day, but if anyone makes progress, do update me!
