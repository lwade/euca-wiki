Sometimes when testing images, it's useful to test completely outside of Eucalyptus, just to reduce the number of moving parts, or test something that's not yet a Eucalyptus feature.  One of the issues with this is that, depending on your network situation, you may need or want to set up your own subnet of IPs.  The libvirt documentation does explain how to do this, but sometimes it's good to have a step-by-step example.

My scenario:

I wanted to test live migration using libvirt and kvm.  It doesn't make a whole lot of sense to do live migration with NAT'ed IPs (unless I've got a floating IP set up between the host machines, which I don't).
My host machines are on a network which only hands out DHCP addresses by MAC reservation, so my VMs cannot get IPs from there.  I could have set up my own DHCP server, but that's not really the right solution, because libvirt already has virtual networking built in, with some help from dnsmasq.  It just takes a few steps to change the default config:

1. **Destroy the default network** -- If you have already started libvirt once, you'll have a default network setup up on virbr0, which assigns IP addresses in the 192.168.122.x block and NATs them.  You don't need this one.  So run:
'''
virsh net-destroy default
'''
1. **Edit the default network config** -- Technically you could add a new config and remove the default.xml symlink from /etc/libvirt/qemu/networks/autostart/ , but I just reused the file.  The following gives me a block of 64 IP addresses (61 usable), of which I have decided to allocate 9 by DHCP.
'''
cat <<EOF
<network>
  <name>default</name>
  <uuid>2696eec4-286d-4168-958b-42ef0be4ea3d</uuid>
  <bridge name="virbr0" mode="route" />
  <forward/>
  <ip address="10.110.7.1" netmask="255.255.255.192">
    <dhcp>
      <range start="10.110.7.2" end="10.110.7.10" />
    </dhcp>
  </ip>
</network>
EOF
'''
1. **Load the new network** into libvirt's config:
'''
virsh net-define /usr/share/libvirt/networks/default.xml
'''
1. **Verify that the bridge is up** -- ifconfig should show virbr0 starting with something like:
'''
virbr0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.110.7.1  netmask 255.255.255.192  broadcast 10.110.7.63
'''
1. **Ensure that packet forwarding is enabled in the kernel**
'''
echo 1 > /proc/sys/net/ipv4/ip_forward
'''
1. **Allow inbound connections** -- for any port to which you would like to allow inbound connections, you need to add a rule.  For example, I want to allow ssh:
'''
iptables -I FORWARD 3 --proto tcp --dport 22 --dst 10.110.7.0/26 -j ACCEPT
'''
1. **Create a VM** -- launch a VM with the network set to virbr0; I'll cover the gory details of that in another page.
