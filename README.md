Een Cisco access-point configureer je bijna net zo als een switch. Het apparaat kent buiten de bekende gigabit-interface(gi0)en de consolepoort echter nog drie bijzondere interfaces, namelijk:
- Interface dot11Radio 0
	Deze interface vertegenwoordigd de 2,4 Ghz radio. Alle instellingen die je op deze interface    
 	configureert of kan configureren hebben alleen betrekking op de 2,4Ghz radio en antenne van de 
 	access-point.

- Interface dot11Radio 1
 	Deze interface vertegenwoordigd de 5 Ghz radio. Alle instellingen die je op deze interface configureert    
 	of kan configureren hebben alleen betrekking op de 5 Ghz radio en antenne van de access-point.

- Interface BVI1
 	Dit is de ‘Bridge Virtual Interface 1’ interface. Deze interface is bedoeld als management 
 	interface.
Zie het configureren van een access-point als het werken met bouwstenen(lego). Je creëert verschillende blokken die je weer met elkaar verbind door er een brug tussen te zetten.
	
Configureren van de AP - Stap 1
Configureer het SSID en map(verbind) deze met de respectievelijke  VLANS..

We creëren hier dus een ‘blok van bouwstenen’. Het blok wat we hier onderbouwen heet het Wi-Fi SSID blok. Alles wat met een SSID te maken heeft configureren we in dit bouwwerkje. Zoals je hieronder opmerkt kunnen we meerde ‘SSID’ bouwblokken maken.  In de onderstaande configuratie ‘map’(verbind) je een SSID met een vlan. Het SSID wordt dus voorzien van een VLAN-TAG. 


ap# Enable
ap# Conf t
ap(config)#Dot11 ssid [SSID_NAAM_1]
ap(config-ssid)# Vlan [ID_1]
ap(config-ssid)# authentication open
ap(config-ssid)# authentication key-management wpa version 2
ap(config-ssid)# wpa-psk ascii 7 ciscopassword
ap(config-ssid)# Mbssid Guest-mode
ap(config-ssid)# End
ap(config)#  	
ap(config)#Dot11 ssid [SSID_NAAM_2]
ap(config-ssid)# Vlan [ID_2]
ap(config-ssid)# authentication open
ap(config-ssid)# authentication key-management wpa version 2
ap(config-ssid)# wpa-psk ascii 7 ciscopassword
ap(config-ssid)# Mbssid Guest-mode
ap(config-ssid)# End
ap(config)#  	
  	
 
Stap 2 – Configureer de encryptie voor de verschillende SSIDs met de respectievelijke  VLANs
Hier onder zetten we de zo juist gecreëerde bouwblokken op de 2,4 Ghz radio van het AP.  Met andere woorden we configureren  de zojuist gecreëerde SSID’s, op een radio interface. In dit geval gebruiken we Interface dot11 0. De ‘0’zegt ons dat we nu enkel de SSID’s configureren op de 2,4 Ghz radio van het AP.


AP#conf t
AP(config)# Int dot11 0
AP(config-if)# Mbssid
AP(config-if)# encryption vlan [ID_1] mode ciphers aes-ccm
AP(config-if)# encryption vlan [ID_2] mode ciphers aes-ccm
AP(config-if)# ssid [SSID_NAAM_1]
AP(config-if)# ssid [SSID_NAAM_2]
AP(config-if)# no shut
	
	
Stap 3 – Configureer de sub interfaces voor de 2.4 GHZ radio en Ethernet
De onderstaande configuratie werkt een beetje hetzelfde als een ‘router-on-a-stick’ configuratie. Er worden zowel voor de Wi-Fi radio interface als voor de Gigabit-interface, sub interfaces gemaakt met het corresponderende vlan nummer. Om te zorgen dat juiste sub interface van de radio communiceert met de juiste sub interface van de gigabit interface wordt er een ‘brug’ gebouwd tussen de beide sub interfaces dit wordt gedaan door beide sub interfaces onderdeel te maken van dezelfde ‘bridge group’. Het ID of nummer van de ‘bridge group’  moet uniek zijn, het is overzichtelijk en makkelijk om voor het ‘bridge group‘ nummer het vlan nummer te gebruiken waar de sub interface ook mee verbonden zijn. 
AP# configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
AP(config)# interface Dot11Radio0.[vlan_ID_1]
AP(config-subif)# encapsulation dot1Q [vlan_ID_1]
AP(config-subif)#bridge group [vlan_ID_1]
AP(config-subif)# interface GigabitEthernet0.[vlan_ID_1]
AP(config-subif)#bridge group [vlan_ID_1]
AP(config-subif)# encapsulation dot1Q [vlan_ID_1]
AP(config-subif)# end
AP# write memory
AP(config)# interface Dot11Radio0.[vlan_ID_2]
AP(config-subif)# encapsulation dot1Q [vlan_ID_2]
AP(config-subif)#bridge group [vlan_ID_2]
AP(config-subif)# interface FastEthernet0.[vlan_ID_2]
AP(config-subif)#bridge group [vlan_ID_2]
AP(config-subif)# encapsulation dot1Q [vlan_ID_2]
AP(config-subif)# end
AP# write memory
Configureer de switch
Configureer switchport 21 op je switch als trunk poort en configureer de beide Wi-Fi vlans op de trunk. Letop!!!! Echt goed opletten!! Je moet zo meteen ook nog een management-interface configureren op het access-point. Dit wordt gedaan door de BVI-interface te configureren. Deze interface wordt niet voorzien van een VLAN ID. Hiervoor is het noodzakelijk dat het management vlan(99), dat we op de switch reeds gecreëerd hebben, beschikbaar is op de trunkpoort als ‘native vlan’. Op een trunk poort wordt in principe al het verkeer dat getrunkt wordt voor zien van een ‘VLAN-TAG’. Het ‘native vlan’ vangt al het verkeer op dat over de switchpoort gecommuniceerd wordt zonder ‘vlan-tag’.

ST_SW_01> enable
ST_SW_01# conf t
ST_SW_01(config)#int fa1/0/21
ST_SW_01(config-if)# switchport trunk allowed vlan 20,25 (voorbeeld)
ST_SW_01(config-if)# switchport trunk encapsulation dot1q 
ST_SW_01(config-if)# switchport mode trunk
ST_SW_01(config-if)# switchport trunk native vlan 99
end
Het beheren van het AP via de management interface
Dit doe je door een IP adres te configureren op de BVI interface van het AP. Let op!! Je moet nog wel de VTY line op de juiste wijze (SSH) configuren.
ap> enable
ap# Conf t
ap# Int bvi 1
ap(config-if)#	ip address 10.10.99.252 255.255.255.0
ap(config-if)#	no shut
ap(config-if)#	end





Ga verder met opdracht 4.3 van SnelTransport




