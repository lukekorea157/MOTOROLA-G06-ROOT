# MOTOROLA-G06-ROOT
Rooting the Motorola G06 (Lagos) 

Hello and welcome to my handmade guide for rooting the Motorola G06 (Lagos)

This is based on my personal experience. It's not an official guide, and I can't guarantee everything will work exactly the same way it will work for you. Hopefully it saves someone else some time and headaches

WARNING BEFORE STARTING

- Unlocking the bootloader and rooting your device will completely wipe your storage. Make sure to back up everything before starting.
- Your warranty may be void, and there's always a risk of bricking your device. Everything is at your own risk.
- The init\_boot.img you patch MUST come from the exact firmware build currently installed on your phone.
- Only use firmware specifically made for the Motorola G06 (Lagos). Motorola devices may have anti-rollback protection, so downgrading can be risky.(Chatjipiti advice lol)
- After every OTA update, you'll need to patch the new firmware's init\_boot.img again.

What I used:

Device: Motorola G06 (Lagos) Android: 15 (API 35) Build ID: VVOB35.78-158 Security patch: January 2026 Kernel: 6.6.66-android15-8 (GKI) KernelSU Manager: Add your version here Bootloader unlock: Tanuki.sh (Linux tool)

If your model, variant, or build is different, don't flash my files. Follow the guide using your own stock firmware.

What you'll need:

- A PC with ADB and Fastboot installed: [https://developer.android.com/tools/releases/platform-tools](<https://developer.android.com/tools/releases/platform-tools>)
- A live Linux USB/SD card. I used Puppy Linux on a 2 GB SD card, but you can use Linux Mint or pretty much any Linux distro.
- Rufus, balenaEtcher, or EtchDroid to flash your Linux ISO: [https://rufus.ie/] [https://etcher.balena.io/][https://f-droid.org/en/packages/eu.depau.etchdroid/]
- KernelSU Manager: [https://github.com/tiann/KernelSU]
- Your phone's matching stock firmware ZIP (usually around 4-5 GB). You only need init\_boot.img from it nothing more, at least on KernelSU, so no need to extract 5gb of files.

Step 1 - Prepare your phone

The very first step is enabling Developer Options and USB Debugging.

1. Go to Settings \> About phone.
2. Tap Build Number several times until Developer Options are enabled.
3. Go to Developer Options and enable USB Debugging.
4. Connect your phone to your PC and authorize the computer when the popup appears.

Make sure ADB recognizes your phone before continuing.

Step 2 - Unlock the bootloader

Now comes the interesting part.

The official Motorola bootloader unlock website didn't work for me, so I used the unofficial Linux unlocking tool called Tanuki.sh.

I tried MTK Tools , but wasn't successful with those. Tanuki.sh was the only one that what worked for me.

Here's the XDA thread. Follow the instructions provided by its author:

[https://xdaforums.com/t/unlock-bootloader-motorola-motorola-g06-g06-power-lagos.4780825/]

You can use pretty much any Linux distro as long as you follow the thread correctly. I personally used Puppy Linux running from a 2 GB SD card.

WARNING: Unlocking the bootloader wipes your phone.

After successfully unlocking your device, mine required me to run fastboot -w manually. If yours bootloops after unlocking, this may help too:

fastboot -w 
fastboot reboot

This wipes user data, so only run it when appropriate and after backing up everything.

Step 3 - Find your build ID and download your stock firmware

After unlocking, go through the Android setup process just enough to re-enable USB Debugging and authorize your PC again.

Now we need to find your exact firmware build.

An easy way is to pull down your notification panel/status bar. On the bottom-left side, you should see something similar to:

15 (VVOB35.78-158)

That's my build ID, for example.

If you can't find yours:

1. Open Settings.
2. Go to About Phone.
3. Open Android Version.
4. Find Build Number and copy it.

Search Google for your exact build number together with "Moto G06 Lagos stock ROM".

Download the matching firmware ZIP. They're usually around 4-5 GB.

You don't need to extract everything. We only need init_boot.img.

IMPORTANT: Make sure the firmware matches the build currently installed on your phone. Don't use my image or someone else's patched image unless the device and exact build match.

Step 4 - Patch init_boot.img using KernelSU

Now comes the rooting part

Download and install KernelSU Manager:

[https://github.com/tiann/KernelSU]

1. Copy your original init_boot.img to your phone.
2. Open KernelSU Manager.
3. Choose the option to install or patch an image.
4. Select your stock init_boot.img.
5. Let KernelSU patch it.

You'll get a file with a name similar to:

kernelsu\_patched\_20261031\_042123.img

Now transfer that patched image back to your PC.

You can use USB file transfer or ADB. For example:

adb pull /sdcard/Download/kernelsu_patched_20261031_042123.img .

Replace the filename with the actual one KernelSU generated. If KernelSU saved it somewhere else, use that folder instead.

VERY IMPORTANT: Keep a copy of your original, untouched init_boot.img. You'll need it if something goes wrong or if you want to remove root.

Step 5 - Flash the patched image

With your patched image on your PC, we're almost there.

First, reboot into the bootloader:

adb reboot bootloader

Alternatively, unplug your cable, turn off the phone, and hold Power + Volume Down until you see Fastboot Mode.

Make sure you're in Fastboot Mode, not FastbootD or Recovery Mode. They're not the same thing.

Now check which slot your phone is currently using:

fastboot getvar current-slot

For example, mine returned:

current-slot: a Finished. Total time: 0.004s

If yours says a, flash using:

fastboot flash init_boot_a kernelsu_patched.img

If yours says b, use:

fastboot flash init_boot_b kernelsu_patched.img

Replace kernelsu_patched.img with the actual filename.

Once the flashing process finishes:

fastboot reboot

Don't flash both slots just because they're available. Follow the active-slot information and the device-specific instructions.

Step 6 - Verify root

After rebooting, open KernelSU Manager.

It should recognize the installed KernelSU environment and report:

Working

Congratulations\! Your Motorola G06 is now rooted\!

You don't need to use Direct Install again just because you manually flashed the patched image. Direct Install is a separate installation method, generally intended for an already-rooted device.

Troubleshooting

"Unknown error" when flashing to slot A or B

This was one of the annoying problems I encountered.

For me, slot A always flashed, while slot B always returned an unknown error.

My workaround was downgrading the stock ROM to the January 2026 security patch. I was previously on the September patch.

After that, I managed to get the phone onto slot A, confirmed it with:

fastboot getvar current-slot

Then I flashed the patched image there, and it worked.

I honestly don't know whether downgrading was strictly necessary or what exactly caused the problem.

BE CAREFUL WITH DOWNGRADING! Motorola devices can have anti-rollback protection.(chatjipiti advice haha but yeah) A signed firmware ZIP doesn't mean every older version is safe to flash. Verify firmware compatibility before attempting any downgrade.

Bootloop after flashing

Don't panic immediately.

Boot back into Fastboot Mode and restore the original stock init_boot.img to the affected active slot.

For example, if your active slot is A:

fastboot flash init_boot_a init_boot.img 
fastboot reboot

Use the corresponding _b partition if your active slot is B.

If the phone still doesn't boot, further recovery may be necessary. Make sure you have the correct stock firmware available.

After an OTA update

OTA updates CAN and may WILL overwrite your patched image, meaning you'll lose root.

Your build ID will also change.

You'll need to download the matching stock firmware for the new build, extract its new init\_boot.img, patch it again using KernelSU, and repeat the flashing process.

Never reuse an old patched image on a different firmware build.

Magisk boot verification error

I actually tried Magisk first (latest version at the time), but got a boot verification error.

I deleted the Magisk-patched image, restored the original stock image, and switched to KernelSU.

Magisk might work for you, but it didn't work for me.

Optional - Running GSIs using DSU Sideloader

If rooting the stock ROM keeps giving you problems, there's another thing you can experiment with: GSIs.

You can use DSU Sideloader:

[https://github.com/VegaBobo/DSU-Sideloader](<https://github.com/VegaBobo/DSU-Sideloader>)

A couple of things to know:

- You need an unlocked bootloader.
- DSU works more like a dual-boot setup.
- You'll generally need to restart from your stock ROM to switch back.
- Storage can be limited. In my case, I had around 46 GB available.

It's an interesting alternative if you want to experiment with different Android builds without completely replacing your stock installation.

Files

If I eventually upload my own patched image, make sure you check the device model, build ID, and SHA256 checksum before flashing anything.

Honestly, patching your own image is always preferable. There's no reason to blindly trust a random binary from somebody on the internet (including me).

Credits

- The XDA thread author for the Motorola G06 bootloader unlocking method.
- Tanuki.sh for the Linux unlocking tool.
- KernelSU: [https://github.com/tiann/KernelSU]
- DSU Sideloader: [https://github.com/VegaBobo/DSU-Sideloader]

And that's it

Hopefully this helps another Motorola G06 (Lagos) owner get their device rooted without spending hours figuring everything out.

Have fun, make backups, and please don't flash random firmware. :)
