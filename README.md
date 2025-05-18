# HA-Router-Reset
<b>Home Assistant Router hard reset if hanging<br>
Reset des Internet router durch power cycling wenn er sich aufhängt oder bei Internet Ausfall</b>

Meine Fritzbox hängt ab und zu und weder Internet noch Wlan funktionieren. 
Durch Unterbrechen der Stromversorgung funktioniert alles wieder. 
Um das zu automatisieren habe ich in Homeassistant eine Automatisierung erstellt, die bei Stromausfall den Router über eine an das RasPi Wlan angeschlossene Schaltsteckdose aus- und 10 Sek später wieder einschaltet.

Requisiten:

- Raspberry Pi
- wlan0 verfügbar oder externer Wifi Stick z.B "Anadol Wifi USB Stick" o.ä. <br>
- Tasmota WLan SchaltSteckdose NOUS A1T o.ä.
- (anstelle von wlan wäre auch ein Zigbee Stick mit entsprechender Schaltsteckdose denkbar)
- Home Assistant<br> 

Installation:

- HA Integration Ping (ICMP), Adresse einstellen 8.8.8.8
- AddOn installieren: https://github.com/joaofl/hassio-addons/tree/master/hassio-hotspot und nach Anleitung konfigurieren
- Schaltsteckdose konfigurieren für das WLan des RasPi

<b>English:</b><br>
Reset the Internet router by power cycling when it hangs or when the Internet fails

My Fritzbox hangs from time to time and neither the Internet nor Wi-Fi work. By interrupting the power supply, everything works again. To automate this, I have created an automation in Homeassistant that switches the router off via a switch socket connected to the RasPi Wlan in the event of a power failure and switches it on again 10 seconds later.

Props:

- Raspberry Pi
- wlan0 available or external wifi stick e.g. “Anadol Wifi USB Stick” or similar.
- Tasmota WLan switching socket NOUS A1T or similar.
- (instead of wifi, a Zigbee stick with corresponding switch socket would also be conceivable)
- Home Assistant

Installation:

- HA Integration Ping (ICMP), set address 8.8.8.8
- Install AddOn: https://github.com/joaofl/hassio-addons/tree/master/hassio-hotspot and configure according to instructions
- Configure the switch socket for the WLan of the RasPi

Automation:

<pre># FritzBox Hard Reset wegen Internet Ausfall
alias: FritzBox Internet Down
description: ""
triggers:
  - entity_id:
      - binary_sensor.8_8_8_8
    to: "off"
    trigger: state
    from: "on"
    for:
      hours: 1
      minutes: 0
      seconds: 0
conditions: []
actions:
  - type: turn_off
    # Schaltsteckdose, im visuellen Editor konfigurieren 
    device_id: da44b5d192f832e526ae811c612c2ea1
    entity_id: 30125932e9b60481aee8d689284cc49b
    domain: switch
  - delay:
      hours: 0
      minutes: 0
      seconds: 10
      milliseconds: 0
  - type: turn_on
    # Schaltsteckdose, im visuellen Editor konfigurieren 
    device_id: da44b5d192f832e526ae811c612c2ea1
    entity_id: 30125932e9b60481aee8d689284cc49b
    domain: switch
  - action: system_log.write
    data:
      message: FritzBox Reboot wegen Internet Ausfall
      level: critical
  - action: notify.mobile_app_sm_g973f
    # Nachricht an Smartphone HA App
    data:
      message: FritzBox Reboot wegen Internet Ausfall
      title: Home Assistant
mode: single</pre>
