---
layout:     post
title:      "Spoofing RFID Key Cards"
subtitle:   "Forgot your gym pass? Use your phone to get in"
date:       2018-07-16 12:00:00
categories: tidbits
author:     "Dominic Peters"
use_math: true
---

<div class="avatar bigAvatar" style="background-image: url('{{ site.baseurl }}/img/fit4less/keycard_2.jpg');">
</div>
After recently having my friend forget their gym pass, and receiving yet another email telling me that my pass would be revoked if I let someone else in again, I came to thinking that there must be a better way to get into the gym. As it turns out, there is another way. The key cards used by fit4less and goodlife rely on a technology called RFID (Radio Frequency IDentification). Specifically, they use the 13.56 Mhz high frequency RFID band, perhaps better known as NFC (Near Field Communication). This is the same technology that allows you to board a bus by tapping your bus pass, 'tap and pay' with your credit card, or enter your hotel room with your keycard. Mobile payment services such as Apple Pay and Google Pay also use NFC, thus most modern smartphones are equipped with NFC hardware. In this blog post, I explain the steps I took to investigate and ultimately spoof the RFID keycards used by fit4less, and hopefully shed some light on the flaws that made the exploit (if one can really call it that) so easy.

### Procedure

First thing's first, I needed to know the manufacturer and model of the tag. NFC Tools is an Android app that allows you to do just that.

![]({{ site.baseurl }}/img/fit4less/nfc-tools.png){:class="center" width="350px"}

As you can see, the Manufacturer is NXP and model is MIFARE Ultralight. The app also revealed the tag's UID (Unique IDentifier, an address unique to each device), a parameter that would become very valuable later.

A quick look at the datasheet reveals that these devices are really quite simple. They have a 512 bit memory area, comprised of the 7 byte UID (read only), 384 bit read/write area, and a small region for locking said read/write area to read only. Further down, the memory layout is described. 

> * The unique 7-byte serial number (UID) and its two check bytes are programmed into the first 9 bytes of memory covering page addresses 00h, 01h and the first byte of page 02h.
* The bits of byte 02h and 03h of page 02h represent the field programmable read-only locking mechanism. Each page from 03h (OTP) to 0Eh can be individually locked by setting the corresponding locking bit Lx to logic 1 to prevent further write access. After locking, the page becomes read-only memory
* Page 03h is the OTP page and it is preset so that all bits are set to logic 0 after production. 
* Pages 04h to 0Fh are the user read/write area. After production the data pages are initialized to the following values: Page 04h is initialized to FFh Pages 05h to 15h are initialized to 00h

Using another Android app, 'mifare ultralight tool' we can read tag's entire memory area. It is organized into 16 pages of 4 bytes. (Bytes are displayed as 2 digit hex values)

![]({{ site.baseurl }}/img/fit4less/mifare-ultralight-tool.png){:class="center" width="350px"}

Based on the datasheet description, the tag's memory is set to its default values, meaning it was never written to after production. This can mean only one thing- that the tag's UID is the authenticating mechanism used by the reader. Essentially, when you're given a new tag, the employee simply scans it and asssociates its UID to your gym membership account. Then, when you scan your tag at the gate, it looks up the UID in a database and lets you in if the address is associated with an active membership. If you lose your tag and get a new one, the new tag's UID will be associated with your account, rendering the old one useless.

In theory then, if you can clone the UID onto another card the gate should let you in. NFC tag manufacturers thought of this and made the UID memory area read only. Counterfeight cards can be bought with a programmable UID, but I didn't want to wait 2 months for a shipment from China. On the other hand, smartphones have NFC card emulation software and hardware built in- they need it for Apple Pay and Android Pay and whatever other NFC services are out there. Unfortunately, much like a MAC address for networking, you can't just change the NFC UID on command. While the implementation ultimately depends on the chipset, the UID is usually randomly generated at each use of NFC. Also like a MAC address, it can be spoofed with the right tools. (Insert evil laughter)

In this case, it required rooting my phone and installing Magisk. At that point, I could install 'Card Emulator Pro' and use it to set any arbitrary static UID. Once set, the UID remains active until a device reboot. This means that to unlock the gate at the gym, I simply have to hold my phone against the reader and wait for the click of the gate. It really couldn't be easier. As an added bonus, since Google Pay doesn't use UID in it's authentication, I can still pay for things with my phone when the spoofed UID is active.

![]({{ site.baseurl }}/img/fit4less/card-emulator.png){:class="center" width="350px"}


### Explanation

The reason why this works is that these tags were never meant to be used as access management devices. In the words of the manufacturer themselves:
> It is intended for use as single trip or limited use tickets in public transportation networks, loyalty cards or day passes for events as a replacement for conventional ticketing solutions such as paper tickets, magnetic stripe tickets or coins.

These tags are meant for situations in which there is no real requirement for security, either because cloning the tag would be of no benefit to someone (eg a loyalty card) or that it is only meant to be single use, where the system's backend is responsible for blacklisting a tag's UID after use. An example of this would be an RFID event pass, where once the person scans the tag and enters the premises, the system blacklists the UID so it can no longer be used to get in.
In the fit4less model, the same UID is used to authenticate a person each time they enter the gym. The tag is only deactivated if it is replaced or the membership runs out. 
Imagine you have a coupon for a free burger at McDonalds. You walk into McDonalds, give your coupon to the employee, and in return they give you a big mac. Under the fit4less model, they give you your burger, but they allow you to keep the coupon and make as many copies of it as you like. Of course, secure systems exist to allow the same RFID card to be used multiple times. Credit cards, campus cards, reloadable bus passes, etc are all used over and over. To make this possible, the RFID card must integrate a secret encryption key. Then, all sensitive data stored on the card is encrypted and can only be decoded by the identical key in the reader. This prevents the possibility of cloning/spoofing a card unless the key can somehow be mathematically calculated or exposed. Using a similar analogy, imagine you won a year's supply of free pizza. You'd like to share this perk with your friends, but the single employee isn't blind- he'll only give out the pizza if he sees that it's you ordering. Until human cloning is possible, there's no way you can trick him into giving your friends pizza.
Terrible analogies aside, NXP actually has a product for this exact purpose, called the MIFARE classic. Per the datasheet, its appplications include:
> * School and campus cards
* Access management
* Public transportation

As for why fit4less/goodlife didn't choose to use these, one can only assume that they were either penny pinching or figured people were too ignorant to notice. And they're probably right.

##### Mandatory: I don't condone using this to clone someone else's keycard, nor will I be held responsible if fit4less bans you for doing so









