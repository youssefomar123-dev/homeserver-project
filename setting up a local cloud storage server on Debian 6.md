# Setting up a Cloud Storage Server on Debian

**Caution: some major parts of this turorial are wrong, so I'm gonna edit them when I'm done irl**

1. Download Debian from its website

    The big download button will give you the latest stable, x86_64, netinst iso

    It's a netinst (net install), so you'll need a stable internet connection while installing it

---

2. Install it ("Graphical install" is usually a reasonbale option)

    Make sure that:
    - You choose English, just to be able to follow this tutorial easily without any translation issues
    - You do not set a root password (just for convenience)
    - You keep all the partitioning stuff on its default (unless you know what you're doing)
    - When it comes to mirrors, choose United States as the country, then choose "deb.debian.org" (just to get the decision limbo off your mind)
    - Both "standard system utilities" and "SSH server" are checked, so you don't waste your time chasing uninstalled packages later

    *If you installed it with a desktop environment (e.g. GNOME, KDE), just open the terminal and continue the tutorial as is*
    
    *You'll have an almost identical experience*

---

3. Run `sudo apt update && sudo apt upgrade -y` to make sure everything is up to date

    `sudo`---> run it as root (the highest admin privileges)
    
    `apt`---> the frontend of Debian's package manager `dpkg` that downloads the requested packages

    `apt update`---> updates the local repo list of what are the latest stable versions of every downloadble package

    `&&`---> to run both commands sequentially

    `apt upgrade`---> upgrades the already installed packages to their latest stable

    `-y`---> presses **Y** automatically instead of asking you to agree on installing the new packages

    *It is almost always preferred to install and update packages as root, to make sure it applies to all users, and to make sure that anything in the backround that can be only done as root is done*

---

4. Install the following packages
    
    `ufw`---> uncomplicated firewall to secure your system

    *Note that I only listed one package, even though there is another in my mind that will slightly increase convenience*

    *Why:*

    *Since it's a server, we need to secure it well, so we need to narrow down attack surface as much as possible*

    ---

5. Set a static local IPv4 address

    *Since it's a server, it needs a static IPv4 address in your home network, so other devices (like your phone) can access it easily*

    Run `sudo nano /etc/network/interfaces` (must be `sudo` to be able to edit it)
    
    The file that describes the network interfaces available on your system will show up

    **It will be something like this:**

    `# This file describes the network interfaces available on your system`

    `# and how to activate them. For more information, see interfaces(5).`

    <br>

    `source /etc/network/interfaces.d/*`

    <br>

    `# The loopback network interface`

    `auto lo`

    `iface lo inet loopback`

    <br>

    `# The primary network interface`

    `allow-hotplug enp0s3`

    `iface enp0s3 inet dhcp`

    `# This is an autoconfigured IPv6 interface`

    `iface enp0s3 inet6 auto`

    *Note that:*

    - *`enp0s3` is the name of my network interface, so it could be different in your case*

    - *We are on DHCP (Dynamic Host Configuration Protocol), which is basically that the router gives the device a local IPv4 address automatically*

        *And we don't want that, because it can be changed automatically anytime*

    - *Every line that starts with a "#" is a comment (like comments in programming languages)*

        *So you can use it to disable unused lines instead of deleting them*

    Now, go to your router's admin page, and look for the range of it's DHCP pool (it's usually between `x.x.x.2` and `x.x.x.100`, on a subnet mask of `255.255.255.0`)

    Then pick a number outside that pool (I'll pick `x.x.x.240`), but do NOT pick:

    `x.x.x.0` because..., honestly, I don't remember what it's reserved for

    `x.x.x.1` because it's the gateway (the router's own local address)

    `x.x.x.255` because it's the broadcast address, used for manual testing of the network and used by DHCP

    **Now, edit the file to this:**

    `# This file describes the network interfaces available on your system`

    `# and how to activate them. For more information, see interfaces(5).`

    <br>

    `source /etc/network/interfaces.d/*`

    <br>

    `# The loopback network interface`

    `auto lo`

    `iface lo inet loopback`

    <br>

    `# The primary network interface`

    `# allow-hotplug enp0s3`

    `# iface enp0s3 inet dhcp`

    `auto enp0s3`

    `iface enp0s3 inet static`
    
            address 192.168.1.240 #Start every line inside this box with a single space
            netmask 255.255.255.0
            gateway 192.168.1.1
            dns-domain debiand2 #Replace "debiand2" with your machine's hostname
            dns-nameservers 192.168.1.1 1.1.1.1 1.0.0.1 #The numbers are: <your gateway> <preferred dns server 1> <preferred dns server 2>

    `# This is an autoconfigured IPv6 interface`

    `iface enp0s3 inet6 auto`

    Save and exit (Ctrl+O then Enter then Ctrl+X)

---

6. Testing the network

    Run `sudo systemctl restart networking.service` to restart the network

    Run `ip address`, you'll see a bunch of info

    Look for the new IPv4 that have been set

    You'll find it right before the first broadcast address `x.x.x.255`

    If it's there, good

    Now, `ping 1.1.1.1` (or whatever address you want) to test the internet connection

    If it's working, good

---

7. Firewall

---

## Setting up the main software solution (server side)

e.g. NextCloud

I'm forced to pause because of hardware issues

> To be continued
