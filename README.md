# Home Ethereum

My checklist and scripts for running [go ethereum client](https://github.com/ethereum/go-ethereum), aka "geth" on a laptop @ the comfort of your home.

We are setting up a "full node" here.  I recommend skipping "fast" and "light" sync.  I personally never got the "fast" sync working (maybe related to [this](https://github.com/ethereum/go-ethereum/issues/16796)) and anyway, for the sake of the ethereum network health, everyone should go full node.

As per today (13.8.2020), it took more than a month to sync and reach the last block, while the blockchain takes around 850 GB of space.


## The Hardware

You don't want to recycle your old "ultrabook" laptop for this one, as they typically have an expensive NVME circuit working as the SSD drive, and no physical space / slot for inserting "commodity" SSD drives.

Prefer a larger gaming laptop with extendable SSD/HDD docks where you can install some cheap commodity SSDs and HHDs.

Keep in mind that **SSD is a must** for geth to work.

I cleaned the dust from an older Asus ROG (Republic of Gamers) laptop that has two docs for internal SSD/HHDs and recycled it for this purpose.

The SSD I'm using is a "Crucial BX500" 2TB drive, with an off-the-shelf price of around 200 EUR.

I also got a "Seagate Backup Plus Portable" 4TB external HDD drive for backing up the blockchain (~ 130 EUR).  This way I never have to download the god'damn thing again if something goes wrong.

Amount of RAM in the laptop is ~ 12 GB.


## Install Kubuntu

Set up Ubuntu 18.04 LTS (or similar).

Remember to choose the option to encrypt your drive!  You don't want all that ethereum account information lying around in an unencrypted SSD.

Install the KDE desktop environment with:
```
sudo apt-get update && sudo apt-get install kubuntu-desktop
sudo apt-get upgrade
```

Disable lid-close auto-suspend etc. from power management.  This way you can just close the lid and leave the laptop lying around, while it's syncing with the ethereum network.


## Bookmarks

Bookmarks checklist for firefox:

- Your favorite exchanges
- https://etherscan.io/
- https://ethgasstation.info/


## Networking

Set a constant IP address for the laptop in your home LAN.

Configure your LAN router with port forwarding from port 30303 to the constant IP address of the laptop.  This is essential, in order to allow enough ethereum clients to connect to your laptop.  Otherwise your syncing process will slow down considerably.


## Tune your system

Some of my favorite packages:
```
sudo apt-get install encfs gnumeric net-tools tree emacs
```

Disable auto-updates:
```
sudo dpkg-reconfigure -plow unattended-upgrades
```

Linux leaves 5% of space unused in all installed disk partitions.  In the case of my 2 TB drive that's just too much, so, diminish number of reserved space with:
```
sudo tune2fs -m0.5 /dev/sdb1
```

Just in case, we add 10 GB of swap:
```
sudo fallocate -l 10G /swapfile
sudo chmod 600 /swapfile 
sudo mkswap /swapfile 
sudo swapon /swapfile
sudo gedit /etc/fstab
## insert this line to /etc/fstab:
# /swapfile swap swap defaults 0 0
sudo swapon --show
```

## Install go

Install go from here: https://golang.org/doc/install?download=go1.14.6.linux-amd64.tar.gz

Adjust your environment to find the go executable:
```
echo "export PATH=\$PATH:/usr/local/go/bin" >> $HOME/.profile
```

## Install geth

Download from:
```
git clone https://github.com/ethereum/go-ethereum.git
```

## Use the scripts

Your SDD is encrypted, but it is still a good idea to have an extra layer of security with encfs.  This way you will also have a directory with encrypted files that you can copy around.  This script will mount you secret directory to "crypt/":
```
./mount_secret.bash
```

You can use this script to daemonize geth as a systemctl background process.  Run this script **only once**:
```
./install_daemon.bash
```

For stopping / starting the daemon manually, use:
```
systemctl --user stop geth.service
systemctl --user start geth.service
```

Check out the logs to see what geth is doing with this:
```
./logs.bash
```

To stop geth, create a backup copy of the blockchain to an external HDD and to autostart geth automatically, use this script:
```
./save.bash
```
however, remember to edit that script first!


Start the JS console:
```
./console.bash
```

## JS console quickstart

List your accounts with:
```
personal.listAccounts
```

Unlock account with:
```
personal.unlockAccount(personal.listAccounts[0],"PASSPHRASE")
```

Balances, etc.
```
eth.getBalance(personal.listAccounts[0])
```

Check sync state:
```
eth.syncing
```

Load some routines:
```
loadScript('loader.js')
```

After that, run:
```
checkAllBalances()
```

Send ethers with
```
eth.sendTransaction({from: "...",to: "...",value: web3.toWei(0.01, "ether"), gas: 120000, gasPrice: web3.toWei(184, "gwei")});
```

- one wei = E-18 Ether
- gas sets the permitted max amount of gas to be burned for this transaction
- gasPrice sets the unit for gas

In that example case, the max transaction fee is:
```
120 000 * 184 Gwei => 120E+3 * 184E+9 wei * 1E-18 wei / eth  = 22080 E-6 eth ~ 0.02 eth
```

## Copyright

Sampsa Riikonen, 2020

## License

WTFPL








