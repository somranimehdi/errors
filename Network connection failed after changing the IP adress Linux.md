# Network connection failed after changing the IP adress Linux

## Solution N1 

Edit the file `01-netcfg.yaml` loctad in   `/etc/netplan/` by running this command
```bash 
Sudo nano /etc/netplan/01-netcfg.yaml
```
Change the shown IP adress  then close and run 

```bash 
sudo netplan apply
```

If the /etc/netplan/01-netcfg.yaml file is empty, it means that the network configuration is not managed by Netplan which will lead us to the second solution.

## Solution N2 

You can check the IP address and gateway configuration in the `/etc/network/interfaces.d/` directory or the `/etc/network/interfaces` file on the machine. Make sure that the IP address is set to `<New_Ip_Address>` and the gateway is set to the IP address of your router.

You can also check the DNS server configuration in the `/etc/resolv.conf` file. Make sure that it contains the IP address of your DNS server. If the DNS server is not set, you can add it by editing the `/etc/resolv.conf` file and adding the following line:

```bash
nameserver <DNS server IP address>
```

After making any changes to the network configuration files, you will need to restart the network service on the machine by running the following command:

```bash
sudo systemctl restart networking

```

## Solution N3 

If those files did not exists,  then it's likely that network configuration is managed by `systemd-networkd`. In this case, you can check the network configuration file at `/etc/systemd/network/`.


You can check if there is a file named 10-static-enp0s8.network (assuming the network interface name is `enp0s8`) in the directory `/etc/systemd/network/`. If this file exists, you can edit it using a text editor such as nano or vi:

**Note that the interface name can be check with the `nmcli device status` command** 

```bash
sudo nano /etc/systemd/network/10-static-enp0s8.network
```

If the file exists, it should show something like this : 

```bash
[Match]
Name=enp0s8

[Network]
Address=192.168.100.7/24
Gateway=192.168.100.1
DNS=8.8.8.8
DNS=8.8.4.4

```

if not you can create it and add those lines while precising the Ip adress and the Name of the interface

Save the changes and exit the editor. Then, restart the systemd-networkd service:
```bash
sudo systemctl restart systemd-networkd
```

then restart the network manager service with : 
```bash
sudo systemctl restart NetworkManager
```

If the problem proceed after restarting the machine, Check if the configuration on `/etc/systemd/network/10-static-enp0s8.network` and then write this command : 
```bash
sudo systemctl enable systemd-networkd
```

