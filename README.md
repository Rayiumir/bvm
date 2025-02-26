# BVM
Windows 11 Virtual Machine on ARM Linux
![20250221_02h33m45s_grim](https://github.com/user-attachments/assets/e310dd9b-e444-4d6c-9dac-caa76f3aaf26)  
It's ready for beta testers. Please [report](https://github.com/Botspot/bvm/issues) good and bad results. Everything is a learning opportunity. Do not assume I am already aware of an issue, unless you can find it in [Issues](https://github.com/Botspot/bvm/issues), in which case please respond with something like "me too".  
Get started:
```
git clone https://github.com/Botspot/bvm
./bvm/bvm help
```
### Usage instructions
Read the help message and follow the instructions. BVM has simplified the VM-creation process from an elaborate list of manual instructions, to a sequence of completely automated steps. Between each step you have the opportunity to change what is happening, modify the config file, retry a step, or do whatever else you want. Unlike some of my other scripts that have a slight "don't try to understand how it all works" philosophy, BVM encourages you to hack around with it and build on top.

To get a fresh VM up and running, use a sequence like this:  
- `./bvm/bvm new-vm ~/win11`  
    This makes a config file: `~/win11/bvm-config` <--- Please read the config file!  
- `./bvm/bvm download ~/win11`  
    This downloads Windows and necessary drivers.  
- `./bvm/bvm prepare ~/win11`  
    This bundles everything up to get ready for first boot.  
- `./bvm/bvm firstboot ~/win11`  
    If the Windows install is interrupted, just run this step again. Be aware: when Windows finishes installing, the VM will shutdown. All .iso files and the unattended folder could be removed once this step is done.  
- `./bvm/bvm boot ~/win11`  
    Main command to use the VM. This works, but is a little laggy and lacks crucial features. It is recommended to boot the VM headless and then connect to it via RDP:  
- `./bvm/bvm boot-nodisplay ~/win11`  
    Then in a second shell, connect to the RDP service:  
- `./bvm/bvm connect ~/win11`  
    The connect mode has:
  - Better audio
  - Clipboard synchronization
  - File sharing (your home folder and any external drives are accessible from This PC)
  - Dynamic screen resizing
  - Usually a higher frame rate.
    Default connect mode uses the FreeRDP client, which is faster, but sometimes freezes during login. If this happens try the `connect-remmina` mode.
- Mount the Windows main hard drive with:  
    `./bvm/bvm mount ~/win11`  
    This can be useful for troubleshooting or deleting Windows Defender. Be aware: the VM needs to be shut down properly to mount in read/write mode.

Full list of modes: `new-vm`, `download`, `prepare`, `firstboot`, `boot`, `connect`, `mount`, `help`, `list-languages`, `boot-nodisplay`, `boot-ramfb`, `boot-gtk`, `connect-freerdp`, `connect-remmina`, `gui`  
What is that last one there? Oh I nearly forgot! ;) BVM has a graphical user interface too.  
![image](https://github.com/user-attachments/assets/6b4bef4f-b18a-44f0-aba3-1d9ea2f0f34a)  
Run the GUI with:
```
./bvm/bvm gui
```
Right now it is quite basic, but functional. It might stay that way, might not. Much of BVM's future depends on how much of an impact it makes in the community. If nobody uses it, then I will leave it to rot as with past projects that I thought were cool but nobody else did.  

### Tips:
- Use Wayland. This is not a hard requirement, but it makes a big difference.
- Use an ARM 64-bit Linux host OS. This is a hard requirement.
- Use Debian Bookworm or a new-ish Ubuntu image. Debian Bullseye may or may not work.
- Your VM can be a folder anywhere, so it could be on an external SSD drive if you have one available.
- If you encounter an issue, please [open an issue.](https://github.com/Botspot/bvm/issues)

### Other notes:
- It seems to not be working on Raspberry Pi 4. Not sure why. If QEMU gets stuck on the BIOS, let me know what hardware you are using. I am not sure if we are just missing a dependency, or if we need an earlier windows version for anything older than the Pi 5.
- If you have past experience with QEMU, I would like to ask for your ongoing help to help build out more features. Surely somebody will want to do serial passthrough, bluetooth passthrough, or something really specific that I will have no idea how to implement.
- GPU driver: Full 2D/3D acceleration may be possible. First of all if you are Jeff Geerling and have a secondary GPU connected to your Raspberry Pi, in theory Windows could talk to it using GPU passthrough. Maybe.  
    For everyone else, it may be possible using this unfinished series of PRs spanning multiple projects: https://github.com/virtio-win/kvm-guest-drivers-windows/pull/943 Also see https://wiki.archlinux.org/title/QEMU/Guest_graphics_acceleration#Virgil3d_virtio-gpu_paravirtualized_device_driver and this other route which may be more mature: https://github.com/tenclass/mvisor-win-vgpu-driver  
    If someone reading this is actually good with "real programming languages," instead of only knowing shell script like myself, feel free to compile it and check if it works on ARM. (And let the community know how it goes!)
- USB passthrough is not quite working yet. The option is ready in the config file, but it will not work until I find an elegant way to change the ownership of USB devices to the current user, or run QEMU as root. This should not be hard.
- One exciting possibility is the chance to break out individual windows programs and integrate them directly into your desktop, using a RDP mode called RemoteApp. See a screenshot of this [here.](https://forums.raspberrypi.com/viewtopic.php?t=384433) Unfortunately it is [very inconsistent](https://github.com/FreeRDP/FreeRDP/issues/11218) right now, and I deemed it too unstable to include as a feature at the moment.

### Ask me anything!
- Is this legal?  
   Yes. By default the VM does receive a license key, but it is a free license for VMs, offered by Microsoft for this purpose.
- Is this unique?  
    Yes and no. Other projects share some similar ideas. The [quickemu project](https://github.com/quickemu-project/quickemu) probably comes closest, but it is only for x86 PCs and not ARM-compatible. There is also the [UTM project](https://getutm.app/), which I am not very familiar with. The most unique thing here, in my opinion, is the extent to which it is automated. I could string the main modes back to back, leave it running, and in a couple hours there would be a fully-installed VM ready to use, assuming nothing goes wrong. Usually you would need to be watching over it to press a key in a 5-second time window to boot into the installer, or click through the installation, but BVM bypasses the keypress by patching the Windows installer ISO in a very unconventional way (see the `patch_iso_noprompt` function), and bypasses manual installation using an autounattend.xml file.  
- What was the timeline for making this?  
    I have wanted to do a KVM Windows VM on a Pi ever since I was a high-schooler with a Pi3, so 5+ years. The task is not trivial, and no tutorials have existed until quite recently. Occasionally someone would get it working, but leave behind very incomplete instructions and not respond to questions. While I messed with the concept in the past on several occasions and had partial success, I never "cracked" the code completely until I made up my mind to figure it out this month. (February 2025) This has been a multi-week effort of near-total dedication to rooting out issues. ChatGPT has been a huge life-saver especially when it came to the obscure Windows stuff.
- Why did you make this?  
    First, it will benefit me personally quite a bit over the long term. As a die-hard Raspberry Pi user without easy access to another computer, now I have an alternative to Wine when I need to run a Windows-only program, such as Lego Mindstorms.
    But also, I genuinely want to help you all out. The ARM desktop community is special, but small. The more people we can keep in this community, the better it can become for everyone. Some will oppose the promotion of a Microsoft product and say it defeats the point of FOSS. But for most folks, it's either Linux with compromise, or no Linux at all. Therefore I see tremendous value in all projects that bring a usable ARM desktop experience within reach for beginner users.
