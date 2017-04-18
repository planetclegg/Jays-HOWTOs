### How to connect to a raspi or any linux link-local machine directly, without knowing its IP address in advance.

#### Problem:  Headless raspi, unknown IP.  Need to ssh into it to shut it down or something.  

```text
# if its not been connected to a dhcp network or otherwise configured for static IP, 
# it probably has a Link-Local address of the form 169.254.x.x (https://en.wikipedia.org/wiki/Link-local_address)

# Connect a unix/linux/osx computer directly to it via ethernet (raspi supports auto-mdx, so does mac and most
# modern machines, but if you're unsure use a crossover cable or ethernet hub)

# first just try the following, for a freshly minted raspi install this usually works (because of zeroconf/avahi)

ssh pi@raspberrypi.local

# note that this assumes you have SSH enabled, which is no longer open by default on newer distros

# what follows is what to do if you have a problem with using the zerconf address

# note: wherever you see "eth0" below, substitute the correct network interface (eth0, wlan0, en0, etc).  
#       use "ifconfig" with no options to dump all interaces

# Force that machine to also have a link-local address, if it doesn't have one already:

ifconfig eth0 169.254.2.2   # .2.2 chosen "randomly"

# (on linux, had to "service network-manager stop"  to keep interface from reverting every couple of minutes)

# If raspi hasn't booted yet, you can probably sniff its arp packets on boot to discover its address with wireshark or tcpdump:

sudo tcpdump -i eth0 -v arp     # (must run as root!)

# also try consulting your local interface's arp table, in case it's has seen an arp message already:
arp -a -n -i eth0

# if the raspi has booted already, may need to use an arp scanner (nmap or arp-scan).  
# arp-scan is arguably simpler, install via:
#	   OSX: brew install arp-scan
#	   debian/ubuntu/etc:   apt-get install arp-scan
#	   Windows: may God have mercy on your soul

# Must be run as root!  -l == local network. --retry=1 to reduce retries if you're in a hurry

sudo arp-scan --verbose -l --retry=1      

# takes about 4 minutes on class b/link-local networks (e.g. 169.254.x.x),
# NOTE: this is darn handy for a quick survey of your local network.
# this will be super-quick on class C networks (e.g. 192.168.x.x), 
# you might have a LONG wait for class A (e.g. 10.x.x.x)
# alternative is "nmap -PR 169.254.0.0/16", but this seems to be slower..

# If the arp tests return nothing, can try pinging the ipv4 broadcast address for the subnet (didn't work for me)
ping -b 169.254.255.255   # << this doesn't seem to work.

# This did work for me: switch to ipv6 (ping6), and try pinging the ipv6 link-local broadcast address (note interface suffix)

ping6 ff02::1%eth0

# That should return some ipv6 addresses.  make sure to ignore the localhost's address as it will probably respond as well...

# one last alternative: ipv6 arp discovery with nmap (really geeky):
nmap -6 --script=targets-ipv6-multicast-*

# it may not be obvious, but you can ssh to an ipv6 address with -6 option (note optional interface suffix)

ssh -6 pi@fe80::1234::4567::abcd::9876%eth0

# NOTE: If the machine is connected to the network, similar approaches can work.   
# for iOS there is an app called "fing" that can do network scanning as well.

```


