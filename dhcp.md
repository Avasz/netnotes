# DHCP

A DHCP server is the one that assigns dynamic IP addresses to the clients connected with its network. If a DHCP server is running and is properly configured then the clients joining the network will automatically be assigned with a valid IP address. If there is no DHCP server running, then clients have to manually assign static IP, netmask, subnet, gateway etc. for their machines.   


**First of all, a dhcp server is required. I use isc-dhcp-server and the following configurations are for the same. The following stuffs are written in configuration file of dhcp server, dhcpd.conf located somewhere in /etc/dhcpd/dhcp.conf. Create a backup of default or existing dhcpd.conf file before editing it.**

*Create a file dhcpd.leases in /var/state/dhcp/, it stores informations about current leases assigned by the server. DHCP server doesn't create it by default.*

	touch /var/state/dhcp/dhcpd.leases


The dhcp server can be started by various system commands.  
For debian based:
	
	/etc/init.d/isc-dhcp-server start  

or

	service isc-dhcp-server start

Find the suitable command for your distro, it maybe different from distro to distro.  


To invoke DHCP server on selected interface you can use:

	dhcpd eth0
	dhcpd eth1
	dhcpd wlan0, etc

In general the syntax is:

	dhcpd interface_name

___
Overview of a sample and simple dhcpd.conf file.  

### 1. Prevent DHCP server from receiving DNS info from client.

	ddns-update-style none; 
	
*Security feature*  

### 2.  Set a domain name & name server

	option domain-name "yourmachinename";

### 3. DNS server

	option domain-name-servers 8.8.8.8, 8.8.4.4;

### 4. Lease time.  
*It is the time assigned to the client who does not request for specific lease time. It is in seconds.*  

	default-lease-time 2000;  
	max-lease-time 3600;

### 5. Authorative
*Authorative indicates that the server should send DHCPNACK to misconfigured clients*

### 6. Configuration

	subnet 192.168.1.0 netmask 255.255.255.0 {
	range 192.168.1.10 192.168.1.250;
	option subnet-mask 255.255.255.0;
	option broadcast-address 192.168.1.255;
	option routers 192.168.1.254;
	}


### 7. Check syntax of dhcpd.conf file

	dhcpd -t  

or

	dhcpd -t /path/to/dhcpd.conf

### 8. Start/Stop/Restart DHCP server

	service isc-dhcp-server start
	service isc-dhcp-server stop
	service isc-dhcp-server restart
	service isc-dhcp-server status


### 9. Additional stuffs

a. Create a dhcpd.leases file, it is not createdby default.  


	sudo touch /var/lib/dhcpd/dhcpd.leases


b. Deny declines to prevent from DoS attack.  

	
	deny declines;

c. The following stuffs should be valid and correct, else it won't work


	option domain-name  
	option domain-name-server    
        option routers

### 10. Final configuration file

	ddns-update-style none;
	authorative;
	deny declines;
	option domain-name "yourmachine";
	option domain-name-servers 8.8.8.8, 8.8.4.4;
	default-lease-time 3600;
	max-lease-time 4500;

	subnet 192.168.1.0 netmask 255.255.255.0 {
	range 192.168.1.10 192.168.1.250;
	option subnet-mask 255.255.255.0;
	option broadcast-address 192.168.1.255;
	option routers 192.168.1.254;
	}

### 11. Binding IP address and clients
*Adding blocks like these to dhcpd.conf*  

	host admin_pc {
	hardware ethernet 00:99:88:77:44:33;
	fixed-address 192.168.1.24;
	}
	
*To deny IP address we can do the following*

	host client_nam {
	hardware ethernet 00:11:22:33:44:44;
	ignore booting;
	}

Thus, example configuration file may look like:  

	ddns-update-style none;
	authorative;
	deny declines;
	option domain-name "yourmachine";
	option domain-name-servers 8.8.8.8, 8.8.4.4;
	default-lease-time 3600;
	max-lease-time 4500;

	subnet 192.168.1.0 netmask 255.55.255.0 {
	range 192.168.1.10 192.168.1.250;
	option subnet-mask 255.255.255.0;
	option broadcast-address 192.168.1.255;
	option routers 192.168.1.254;
	}
	
	host admin_pc {
	hardware ethernet 00:99:88:77:44:33;
	fixed-address 192.168.1.24;
	}

### 12. Auto starting DHCP server on boot  
It actually depends on your distro.  
For ubuntu,mint,debian *(not sure if all works in all, but at least one should work)*  etc you may use: 
		
		update-rc.d isc-dhcp-server enable

or, if you have chkconfig installed

		chkconfig isc-dhcp-server on

or, add the line in /etc/rc.local before exit 0;

		/etc/init.d/isc-dhcp-server start

or
		
		dhcpd interface_name


For arch linux you can:

	systemctl enable dhcpd
	
**UPDATE**  
`systemctl start isc-dhcp-server` to start dhcp server  
`systemctl enable isc-dhcp-server` to auto start dhcp server at boot  

