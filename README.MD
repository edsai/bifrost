# bifrost

bifrost is a collection of scripts and tools for running a Raspberry Pi with a LTE modem hat. The purpose for the device is to bridge multiple networks and act as a failsafe when primary internet connectivity goes down.

### network.sh

network.sh is a simple script to check conditions of your primary network interface and its ability to reach the internet. Currently, this is completed by pinging a well known DNS server, Google's 8.8.8.8, over a specified interface (ping -I). Based on the response, the script will either turn on the LTE network coverage or turn it off. 

* If ping exists with code >=1 (unsuccessful or error) and PPPD is not active, turn it on.
* If ping exists with code >=1 (unsuccessful or error) and PPD is active, do nothing & exit.
* If ping exists with code 0 (successful) and PPPD is active, shut it down.
* If ping exits with code 0 (successful) and PPPD is not activate, do nothing & exit.

You can tweak the interface and the destination by editng the variables ```iface``` and ```pingip``` in the script.

Add this to your crontab (root) to run every 5 minutes: ```*/5 * * * * /path/to/network.sh 2>&1```

Depends on pon and poff ppp scripts.


### Tunnel.sh

Depending on your LTE service provider, you may or may not be able to actually talk to the device over the LTE Network. In my case, the network I am using blocks all incoming traffic. 

tunnel.sh uses autossh to create and sustain a reverse tunnel that will automatically reconnect when the network switches between primary network connectivity and LTE. The script is meant to be executed by crontab and contains fall back code in the event that autossh fails or exits permanetly for weirdness. 

To tune to your specific environment edit the following:

* keyFilePath - provide the private key identity file
* SSHremotePort - the port that SSH runs on the remote host (normally 22, change if not)
* SSHTunnelPort - the port number you want to use for the SSH tunnel on the *remote* host. Think very high. Examples: 2222,5555, etc.
* SSHlocalPort - the port SSH is running on from the machine your are initiating the connection from (normally 22, change if not)
* identity - the username and host/IP address you wish to connect to

Add this to your crontab (not root) to run every 1 minutes: ```*/5 * * * * /path/to/tunnel.sh 2>&1```

Depends on autossh being installed. 


## Versioning

For the versions available, see the [tags on this repository](https://github.com/Privacywonk/MMM-Surf/tags). 

## Authors

* **me** - *Initial work* - [PrivacyWonk](https://github.com/PrivacyWonk)

## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0) license  - see the [LICENSE.md](LICENSE.md) file for details

