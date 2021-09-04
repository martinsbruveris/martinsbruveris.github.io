---
layout: post
title: Adventures with Android
date: 2017-09-16T11:48:24+00:00
author: Martins Bruveris
tags: android
---
A geocaching adventure gone wrong saw my phone disappear into the depths of a Latvian river and so I was in need of a replacement phone. My parents were kind enough to let me use one of their old phones and after unlocking it and acquiring a new sim card—both tasks ending up being more time-consuming than they should have been—nothing stood between me and the enjoyment of a new phone.

<!--more-->

Nothing, that is, except for the installation of an open source, custom Android distribution. One can of course debate the necessity of this move and whether it is not just a waste of time. I will not do so, except for saying that I like to feel that it is I who is in control of my phone and not Sony, Google or someone else. I like to start with a clean phone and not one that has five Sony-made apps preinstalled "for my convenience". Here, for example, is an <a href="http://www.androidpolice.com/2015/06/06/android-m-will-never-ask-users-for-permission-to-use-the-internet-and-thats-probably-okay/">explanation</a>, why Android does not allow one to control apps' internet access by default.

Having gone through the installation process three years ago with my old phone, I thought two or three hours should be plenty of time to unlock the bootloader, install a fresh Android and get on with life. Oh, how naive we sometimes are...

My Android of choice was <a href="https://lineageos.org/">LineageOS</a>, the spiritual <a href="https://lifehacker.com/cyanogenmod-is-dead-and-its-successor-is-lineage-os-1790554964">successor</a> of CyanogenMod. The process started out reasonably clearly. First, one needs to unlock the bootloader. When requested, Sony does provide the unlock key together with warnings that unlocking the phone voids the warranty, is dangerous and that from now on I am on my own. To demonstrate the general user-friendliness of it all, let me mention that the unlocking itself is done via the intuitive command

{% highlight bash %}
$ fastboot -i 0xfce oem unlock 0xUNLOCK_CODE
{% endhighlight %}

while the phone is in bootloader mode and connected to the computer via USB.

Unlocking the phone wipes its contents, a lesson I had forgotten since last time and had to relearn by experience. With the phone unlocked, one then installs a <a href="https://www.androidcentral.com/what-recovery-android-z">recovery environment</a>, in this case <a href="https://twrp.me/">TWRP</a>, and then uses it to install LineageOS. Finding the right version of TWRP for my phone was the first challenge. The latest version, 3.1.1., simply did not want to work and the only version that did was 2.8.7. Fine, so be it. With TWRP installed and running, I followed the instructions, wiped the phone, loaded the LineageOS installer and pressed install.

However, instead of installing the process stopped with some error messages. Some googling seemed to suggest that I was using a too old version of TWRP to install a too new version of Android (7.1). So, back to installing the recovery. Some more googling seemed to suggest that my current firmware was too old to support the newer version of TWRP... So, I restore the system from a backup and go about updating the firmware. This introduces me to tools like FlashTool and XperiFirm, which download the up to date firmware and package it for installation. The packaging process turned out to take about two hours.

By now it is 4am when I press the button to flash the new firmware and this is when things get interesting. The installation process aborts with an error. Apparently, I forgot to check a box in the settings, allowing software from "unknown sources" to be installed. And now I have a phone that does not boot, so checking the box is not an option any more; nor does it boot into recovery, so restoring from backup is not possible either. I decide to call it a night and go to sleep.

The following day, I realize that I will not get to a working phone with Linux alone. So I boot into Windows, install the official Sony firmware update tool, restore the phone and start again: new firmware with all boxes ticked, then latest version of TWRP, then finally the installation of LineageOS and the Google Apps bundle. About three hours and it is done!

What have I learned? Nowadays, installing Ubuntu on a computer is simple and straight forward. Installing a custom Android distribution, on the other hand, is an endeavour requiring technical skills, dedication and lots of googling. All in all, it took me about eight hours. Information is found in websites of varying trustworthiness. There is a new language to be learned, vocabulary to be absorbed.

Reinstalling the system means rebooting the phone variously into recovery, bootloader or normal mode, each requiring different buttons to be pressed at different times. When rebooting into recovery mode fails, one is then left with the mystery: is it because the wrong buttons were pressed or because the installation of the recovery environment failed?

There is also a general lack of certainty, whether some piece of information applies to my given situation. There is no uncertainty in the instructions. They are always very certain. But do they apply to my phone with Android Nougat or just to Kitkat? Is the firmware version important? The version of FlashTool I use to package the firmware? And what are the consequences of a mismatch between phone and instructions? Can one really brick a phone beyond repair?

Such a steep learning curve, almost a learning cliff, will deter most people from engaging with their phones below surface. Once there was Windows Xp, where it was much easier to reach below the surface. It was not difficult to access the registry, mess around with drivers and network settings. In fact, troubleshooting was part of daily life. Now the surface seems much more polished and much more difficult to penetrate. Getting "under the hood" is more difficult and less encouraged. This is a pity.

Was it worth it? Probably.
