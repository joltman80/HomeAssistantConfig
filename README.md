# Home Assistant Generic Configuration

We want to use VS Code on a remote machine to view and edit configs in Home Assistant.  Follow these steps to make that happen.

## Home Assistant Config
After the initial Home Assistant OS install, follow these steps:

1.  Install the 'Terminal & SSH" Add On from SETTINGS > ADD-ONS.
2.  After the install, choose to START ON BOOT, WATCHDOG and AUTO UPDATE.
3.  Open Terminal & SSH and choose CONFIGURATION from the top.
4.  On your local machine, generate an SSH keypair.  Install the PUBLIC key in the AUTHORIZED KEYS section.
5.  In the Packages section of Terminal & SSH, add the following:

```console
gcompat libstdc++ curl grep procps
```

_NOTE: I had to add "procps" after an update sometime around April 2024 that broke SSH.  Used this [thread](https://community.home-assistant.io/t/how-to-use-remote-python-debugging/714317/3).
6.  Under the SERVER config section, add this:

```console
tcp_forwarding: true
apks: []
sftp: true
compatibility_mode: true
```
7.  Under NETWORK, choose port 22.
8.  Go back to the INFO tab and restart the Add-On.

## VS Code Config

In VS Code, go to EXENTSIONS, and install the REMOTE EXPLORER and REMOTE - SSH extensions.

Once installed, you will have new extensions installed in the primary sidebar.  Choose the REMOTE EXPLORER button.

Hover your mouse over the SSH title under REMOTES(TUNNELS/SSH) and hit the GEAR button.  Choose your local .ssh/config file.  Enter the following in that file(add your home assistant IP or DNS name):

```console
Host homeassistant_IP/DNS_Here
  HostName homeassistant_IP/DNS_Here
  IdentityFile ~/.ssh/id_ed25519
  User root
```

Save the file.  Go back to the REMOTE EXPLORER and hit the REFRESH icon at the top of REMOTES (TUNNEL/SSH).  You can now connect to your Home Assistant instance with your remote VS Code client.

## MQTT Config

In Home Assistant go to SETTINGS > ADD-ONS and click on the ADD-ON STORE.  Find "Mosquitto Broker" and install it.  In Home Assistant, go to SETTINGS > PEOPLE > USERS (tab on the top of the page).  Click ADD USER.  Create a mosquitto broker user with the following properties:

Display Name: MQTT Broker User
Username:  mqttbrokeruser
Password:  Generate a strong password here.

Do not check CAN ONLY LOG IN FROM THE LOCAL NETWORK and ADMINISTRATOR

## RATGDO Config

When you receive your RATGDO, plug it into your laptop, open Chrome/Chromium and navigate to [this webpage](https://paulwieland.github.io/ratgdo/flash.html).  Use the MQTT option and choose the firmware version that corresponds to your device.  Follow the steps to flash the firmware.  In Linux, the serial port with be ttyUSB0.  Once the device is flashed, you will be prompted to enter the 2.4GHz WiFi network that the device should join.  After you enter that info, you will be sent to the config webpage.  Here you will need to create an OTA/Web Config Password.  Add this along with the DEVICE NAME & WEB CONFIG USERNAME to your password database.  Enter the following info:

MQTT SERVER IP: Home Assitant IP Address
MQTT SERVER PORT:  1883 (default for Mosquitto)
MQTT SERVER USERNAME:  mqttbrokeruser (user you created above)
MPTT SERVER PASSWORD:  password you created above
MQTT TOPIC PREFIX:  /home/garage/MainDoor
HOME ASSISTANT DISCOVERY PREFIX:  homeassistant (the default on the page already)
GARAGE DOOR CONTROL PROTOCOL:  Enter the correct protocol for the GDO, not the wall panel

Save the config and reboot.

### Add LOCK to MQTT Version of RATGDO

As of the MQTT 2.57 FW version, the LOCK feature is not enabled.  To add the LOCK feature of the MQTT FW, navigate to:

1. Home Assistant SETTINGS
2. DEVICES & SERVICES
3. INTEGRATIONS
4. Click on MQTT
5. Click on CONFIGURE

In the PUBLISH A PACKET section, put the following config under TOPIC:


```console
homeassistant/lock/ratgdo-46115/config
```
<sub>NOTE: Enter the RATGDO name you wrote down earlier.</sub>

Click on the RETAIN.

Under PAYLOAD, enter the following, properly edited for your RATGDO name:

```json
{
    "~": "/home/garage/MainDoor/ratgdo-46115",
    "name": "Lock",
    "unique_id": "ratgdo-46115_C8:C9:A3:1B:C4:25-lock",
    "availability_topic": "~/status/availability",
    "command_topic": "~/command/lock",
    "payload_lock": "lock",
    "payload_unlock": "unlock",
    "state_locked": "locked",
    "state_unlocked": "unlocked",
    "state_topic": "~/status/lock",
    "device":
    {
        "name": "ratgdo-46115",
        "identifiers": "ratgdo-46115_C8:C9:A3:1B:C4:25",
        "manufacturer": "Paul Wieland",
        "model": "ratgdo",
        "sw_version": "2.57",
        "configuration_url": "http://10.0.2.35/"
    }
}
```
<sub>NOTE: Be sure to include the -lock at the end of the unique_id value.</sub>
<sub>This config was pulled from [here](https://github.com/ratgdo/mqtt-ratgdo/issues/44).</sub>