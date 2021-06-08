# AP
Access Point
en
config t
Dot11 ssid [SSID_NAAM_1]
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
