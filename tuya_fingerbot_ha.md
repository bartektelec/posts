# Setting up Tuya Fingerbot with Home assistant

Got a AliExpress package delivered home today, and there was a Tuya Fingerbot BLE. I thought I ordered a zigbee version, but apparently not.
It is a struggle to get set up. First I had to add bluetooth support for my HA deployment. Done it by installing dbus broker and bluez on my raspberry
```sh
apt install dbus-broker
systemctl enable dbus-broker.service
apt install bluez
```

and specifying it as a volume in my deployment:
```yaml
volumeMounts:
  - mountPath: /run/dbus
    name: ha-dbus
volumes:
  - name: ha-dbus
    hostPath:
      path: /run/dbus
```

the official tuya integration for home assistant didn't work for me - it would show the device but no options to control it.
I later figured out Tuya has two identical apps with two separate infrastructures... nice.
Smart life and Tuya Smart apps look exactly the same but aren't interchangable.

Here is an integration for communicating with a Tuya device through BLE directly, instead of doing it through cloud:
https://github.com/PlusPlus-ua/ha_tuya_ble

A Tuya Cloud account will be needed, not only that I also needed to connect my "App account" to it:
```
1. Navigate to Tuya cloud: https://eu.iot.tuya.com/ (mine is on the eu region, yours could be in another)
2. Login
3. Click Cloud icon
4. Click Devices in top menu
5. Click Link Tuya App Account in submenu
6. Click unlink, 
7. Click Add App Account button, 
8. opened the Smart Life App and captured QRCODE that tuya website was showing 
9. grant all devices all permissions (read, write, management)
```

finally I managed to log in with the BLE integration, but now I am getting a "Device not added to Tuya cloud" error message.
Great, the fingerbot is visible there but marked as "Offline". Fuck.
After few hours of try and error my device doesn't want to pair anymore. Maybe will give it another try tomorrow.
