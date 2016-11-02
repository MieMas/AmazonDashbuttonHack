# AmazonDashbuttonHack
Szenario 1
-----------------------------------------------------------------------
	Sudo apt install python-scapy tcpdump
	Sudo apt install python-requests
	Script herunterladen

		Sudo python2 listen.py
		Für Wake-On-Lan:
			pip install ConfigParser
		Bisher über das heimische Netzwerk (Problematik!) – kein Drama, wenn kein Produkt zugeordnet ist!
		Kann verhindert werden durch:
			MAC-Adressen-Filter über den Router. 
			Oder separates WLAN nur für Dashbuttons.

Szenario 2 (basiert auf Szenario 1)
-----------------------------------------------------------------------

	Raspberry PI als WLAN-Accesspoint und DHCP-Server einrichten.
		sudo apt install hostapd dnsmasq

		/etc/network/interfaces
			allow-hotplug wlan0
			iface wlan0 inet static
			address 192.168.12.1
			netmask 255.255.255.0
			network 192.168.12.0
			broadcast 192.168.12.255	

		/etc/hostapd/hostapd.conf:
			interface=wlan0
			driver=nl80211
			ssid=raspi-iot
			hw_mode=g
			channel=8
			wpa=2
			wpa_passphrase=BitteEinGutesPasswort!
			wpa_key_mgmt=WPA-PSK
			wpa_pairwise=CCMP
			rsn_pairwise=CCMP

		/etc/default/hostapd
			DAEMON_CONF=/etc/hostapd/hostapd.conf

		DNSMASQ:
		/etc/dnsmasq.conf
			interface=wlan0
			dhcp-range=192.168.12.50,192.168.12.150,12h
		
		DASH-Button Einrichtung benötigt Internet (IP-Forwarding aktivieren):
			/etc/sysctl.conf
				net.ipv4.ip_forward=1
				net.ipv6.conf.all.forwarding=1
		
		Nach Neustart betreibt der Raspi sein eigenes WLAN (raspi-iot)
		Pakete werden aber vom Dashbutton noch nicht weitergeleitet (Firewall!):
			sudo iptables -t nat -A POSTROUTING \-o eth0 -j MASQUERADE
		Firewall-Regel ist nur temporär! Nach Neustart ist die Regel wieder weg (das ist gut!).
