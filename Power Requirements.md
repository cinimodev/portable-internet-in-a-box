## Power
My current bank is a [Jackery Explore 160](https://www.jackery.com/products/explorer-160-portable-power-station) that is rated at 167Wh. ~~However, I want to assume there is some loss in usage. I am estimating that it is 85% efficient, which puts it at an actual capacity of 142Wh.~~ I may be misunderstanding this. The efficiency loss may only come through the standard outlet when using a transformer. For example, when plugging in a laptop using the power brick, the brick is only 85% efficient. If I only plan to come out the USB ports on the power bank, I can assume 100% capacity.

For it to run for 4 hours a day for 7 days, that means I plan to use it for 28 total hours. At that rate, I can ~~use 5w~~ 6w.

To be on target, I have to take into account the router, server, and any storage. Then, I need to factor in the load on the devices when it is operational. 

#### Server device options
1. Pinebook Pro. A laptop that I can't seem to find a use for. I know it uses low power and can charge from USB. It also runs Armbian, so can use with Docker, etc.
2. Raspberry Pi 2 Model B. I have a couple just lying around and has the biggest ecosystem. I know I can setup everything I want on a RPI.
3. An Android phone. I spent a little time investigating this. Although I could just setup a samba server on an Android device and let people download from it, I don't like this option. I want something that is accessible through the clients browser. Also, I think most phones will exceed my power limits. 

#### Device testing

Current device power usage rate:
* Raspberry Pi 2 Model B (without attached storage) idle: 2w
* Pinebook Pro (without attached storage/screen off) idle: 5w, once the battery was fully charged.
* GL.inet Router: 1w

The GL.inet router is a no-brainer. Not only does it only use 1w and has a ton of features, I already own it.

The Pinebook Pro offers a lot of flexibility. Since it has a screen, I can fix issues faster. It also has its own battery, which extends how long it will last without power. I can also connect a traditional USB external hard drive in addition to having an SD card slot. But, it is right on my power limit. 

The RPI falls well below my power requirements, which makes it easy to pick. Being headless isn't that much of a problem, as long as I can get an IP address. The main issue is storage. The USB ports on the Pi cannot power an external drive, so I will have to use something like a USB stick or a SD card in a USB reader. I'm not sure how reliable that is or how much power it will draw. My guess is the power will be significantly less than a USB external HDD. 

## Pinebook Pro
This is the primary route. The Raspberry Pi 2 model B is 6 years older and only has 1 GB of RAM. The PBP has 4 GB of RAM, which I will need for having 7 containers running and up to 4 users accessing the data. 

I also decided to go with an SD card as the data storage, which uses significantly less power than a traditional external drive. The PBP has an SD card slot, which will be much faster than accessing the data from an SD card attached to USB  storage on the RPI. 

#### PBP power use
In order to drive down the amount of power being used, I want to slim the OS down as much as possible, disable unnecessary radios, and turn off the screen. This will use less power because the primary system will need less resources. 

First, I disabled booting to the XFCE desktop using the `armbian-config` tool baked into the OS. 

```
$ sudo armbian-config
```

Go to `System` and then `Desktop`.

![[Pasted image 20221218185035.png]]

While in this tool, I also changed the CPU settings to reduce the power draw. Go to `System` and then `CPU`.  I set it to the CPU speed to the lowest setting, and then capped the speed at `1416000` so it can't draw higher under load. It may slow things down, but I need to keep the power draw under control. 

![[Pasted image 20221218185348.png]]

Then, I set it to `powersave`, which dropped the power draw by 1-2 watts.

![[Pasted image 20221218185207.png]]

Next, I disabled hibernation/sleep when the laptop lid is closed. [These are the commands](https://www.tecmint.com/disable-suspend-and-hibernation-in-linux/) to do this:

```
$ sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

Now add these lines to `etc/systemd/logind.conf`

```
HandleLidSwitch=ignore
LidSwitchIgnoreInhibited=no
HandleLidSwitchDocked=ignore
```

Reboot to apply the changes.

These changes will make it so the laptop does not sleep when the laptop lid is closes. However, it also keeps the screen on, which uses an additonal 4-6 watts. I tried everything I could find to disable the screen through commands on the machine, but none worked. Both `xset` and `xrandr` will error out because it says they can't find a monitor. I also can't use `vbetool`  as it is not in the repos for Armbian. 

In order to disable the screen, I had to physically disconnect the cable from the motherboard. It is simple to attach/detach if anything comes up later. Cable is located here:

![[Pasted image 20221218192656.png]]

#### Real world power draw
When sitting idle, the total power draw on the Jackery power bank is somewhere between 1-5 watts. 

When accessing any of the services, the power draw is between 4-5 watts. 

The highest power draw is streaming from Jellyfin. When streaming, the power draw is roughly 6 watts. 

All of these numbers are with the GL.iNet router attached. 

#### Performance
At idle, resource usage is low. 

![[Pasted image 20221218190903.png]]

Even when streaming, the resource use is much lower than I anticipated. This is while streaming a movie:

![[Pasted image 20221218191456.png]]
