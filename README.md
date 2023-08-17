# Home Assistant Generic Configuration

We want to use VS Code on a remote machine to view and edit configs in Home Assistant.  Follow these steps to make that happen.

## Home Assistant Config
After the initial Home Assistant OS install, follow these steps:

1.  Install the 'Terminal & SSH" Add On from SETTINGS > ADD-ONS.
2.  After the install, choose to START ON BOOT, WATCHDOG and AUTO UPDATE.
3.  Open Terminal & SSH and choose CONFIGURATION from the top.
4.  On your local machine, generate an SSH keypair.  Install the PUBLIC key in the AUTHORIZED KEYS section.
5.  In the packages section add the following:

```console
gcompat libstdc++ curl grep
```
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