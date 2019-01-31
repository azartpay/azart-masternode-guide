# Setup a Masternode with Ubuntu 16.04 VPS

In this guide you will learn how to prepare a Azart Masternode in Ubuntu version 16.04 VPS. Before you start, you need to meet the initial requirements.

This guide was done with the following environment:
* Local: Windows 8.1 64 bit
* Remote: Ubuntu 16.04 64 bit (fresh Ubuntu server without Azart already installed)
* Azart Version: 0.13.0.1

## Initial requirements

### Virtual Private Server (VPS)

A Masternode must be hosted on a [VPS](https://en.wikipedia.org/wiki/Virtual_private_server). Get a VPS from a provider like [DigitalOcean](https://www.digitalocean.com/), [Vultr](https://www.vultr.com/), [Linode](https://www.linode.com/), etc.

The recommended requirements for the master node would be the following:
* Operating system (OS): Linux.
* Linux Distribution: Ubuntu.
* Version: 16.04 (Xenial) 64 bit.
* Memory RAM: At least 1 GB RAM.
* Disk Size: At least 30 GB.

### Azart Transaction

In order to have a Azart masternode you must have a transaction of 10,000 AZART in your desktop Cold Wallet.

To carry this out you need to open your Azart Core wallet.

Now you will have to go to the "File" tab and select "Receiving Addresses...".

Click on the "New" button and write a label for your new address (for example, "mn01").

Right click on the address you just created and click on "Copy Address" and click on the "Close" button.

Now that you have the address created, you will send 10,000 AZART to your address. With this, you will have a transaction of 10,000 AZART in your wallet.

Click on the "Send" tab and on "Pay To", enter the address you just created. You will see that in "Label" the name that you put before will appear.

Then you must enter the value of "10000" in "Amount" and you must NOT select "Substract fee from amount". Finally, you must click on the "Send" button.

In the "Overview" tab, a "Payment to yourself" will appear. This transaction must be confirmed by the network, so you will have to wait for it to be confirmed (it may take five minutes of waiting depending on the state of the network and the commission chosen).

## Cold Wallet Setup Part 1

### Enabling Azart Core wallet advanced options

Open your Azart Core wallet on your desktop and now click on "Settings" tab, press on "Options..." and select "Wallet" tab.

The options that you have to select are the following:
* "Enable coin control features".
* "Show Masternodes Tab".

In the image you will see the two options that you will have to select.

![Options on Wallet Settings to select](https://github.com/azartpay/azart-masternode-guide/blob/master/azartcore_settings.png "Wallet Settings")

Press "OK" button. In order to enable the new options, you have to restart Azart Core wallet.

### Creating Masternode private key

Open your Azart Core wallet on your desktop and now click on "Tools" tab and press on "Debug console".

Now you have to enter the following command:

```
masternode genkey
```

You should see a long key that looks like:
```
3HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg
```

This is your Masternode `private key`. Keep it safe and do not share with anyone.

## VPS Setup

### VPS Connection

Depending on your local machine, you will need to use different steps:
* Windows users: You have to connect using PuTTY and it is recommended to [follow this guide](https://www.digitalocean.com/community/tutorials/how-to-log-into-your-droplet-with-putty-for-windows-users).
* Linux users: You have to connect using ssh.

### Masternode Installation

Copy and paste these commands into your VPS and hit enter. Remember that one box at a time:

```
apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get -y install \
    software-properties-common \
    fail2ban \
    wget \
    git \
    unzip \
    libevent-dev \
    libboost-dev \
    libboost-chrono-dev \
    libboost-filesystem-dev \
    libboost-program-options-dev \
    libboost-system-dev \
    libboost-test-dev \
    libboost-thread-dev \
    libminiupnpc-dev \
    build-essential \
    libtool \
    autotools-dev \
    automake \
    pkg-config \
    libssl-dev \
    libevent-dev \
    bsdmainutils \
    libzmq3-dev \
    python-virtualenv \
    nano \
    && apt-add-repository -y ppa:bitcoin/bitcoin \
    && apt-get -y update \
    && apt-get -y install libdb4.8-dev libdb4.8++-dev
```

```
cd /opt \
    && wget https://github.com/azartpay/azart/releases/download/0.13.0.1/azart-0.13.0.1-linux.tgz \
    && tar -xvf azart-0.13.0.1-linux.tgz \
    && rm azart-0.13.0.1-linux.tgz \
    && cp azart-0.13.0.1-linux/azart{d,-cli} /usr/local/bin \
    && chmod -R 7777 /usr/local/bin/azart*
```

```
cd /root && mkdir -p .azartcore && nano .azartcore/azart.conf
```

Replace:

```
masternodeprivkey=PRIVATKEY
externalip=VPSIPADDRESS
rpcuser=RPPORT
rpcuser=RPCUSER
rpcpassword=RPCPASSWORD
```

With your info!

```
listen=1
server=1
daemon=1
masternode=1
masternodeprivkey=PRIVATKEY
logtimestamps=1
maxconnections=256
externalip=VPSIPADDRESS
rpcuser=RPPORT
rpcuser=RPCUSER
rpcpassword=RPCPASSWORD
rpcallowip=127.0.0.1
addnode=5.9.6.17:9779
addnode=193.47.33.25:9779
addnode=193.47.33.24:9779
addnode=193.47.33.23:9779
addnode=193.47.33.22:9779
addnode=193.47.33.21:9779
addnode=193.47.33.20:9779
addnode=193.47.33.19:9779
addnode=193.47.33.18:9779
addnode=193.47.33.17:9779
addnode=193.47.33.16:9779
addnode=193.47.33.15:9779
addnode=193.47.33.14:9779
addnode=193.47.33.13:9779
addnode=193.47.33.12:9779
addnode=193.47.33.11:9779
addnode=193.47.33.10:9779
addnode=193.47.33.9:9779
addnode=193.47.33.8:9779
addnode=193.47.33.7:9779
addnode=5.188.205.240:9779
addnode=5.188.205.239:9779
```

CTRL X to save it. Y for yes, then ENTER.

### Create Azart Service

Now you have to create azartd Service:


```
nano /etc/systemd/system/azartd.service
```

With following info:
```
[Unit]
Description=azartd
After=network.target
[Service]
Type=simple
User=root
WorkingDirectory=/root/
ExecStart=/usr/local/bin/azartd -conf=/root/.azartcore/azart.conf -datadir=/root/.azartcore
ExecStop=/usr/local/bin/azart-cli -conf=/root/.azartcore/azart.conf -datadir=/root/.azartcore stop
Restart=on-abort
[Install]
WantedBy=multi-user.target
```

CTRL X to save it. Y for yes, then ENTER.

```
systemctl enable azartd && systemctl start azartd
```

#### Installing Sentinel

```
cd /opt \
    && git clone https://github.com/azartpay/azart-sentinel azart-sentinel \
    && cd azart-sentinel \
    && virtualenv ./venv \
    && ./venv/bin/pip install -r requirements.txt \
    && crontab -e
```

Hit 2. This will bring up an editor. Paste the following in it at the bottom.

```
* * * * * cd /opt/azart-sentinel && ./venv/bin/python bin/sentinel.py >/dev/null 2>&1
```

CTRL X to save it. Y for yes, then ENTER.

Use `watch azart-cli getinfo` to check and wait until it's synced (look for blocks number and compare with a [block explorer](https://chain.azartpay.com)).

## Cold Wallet Setup Part 2

1. On your local machine open your `masternode.conf` file.
   Depending on your operating system you will find it in:
   * Windows: `%APPDATA%\AzartPay\`
   * Mac OS: `~/Library/Application Support/AzartPay/`
   * Unix/Linux: `~/.azartpay/`

   Leave the file open
2. Go to "Tools" -> "Debug console"
3. Run the following command: `masternode outputs`
4. You should see output like the following if you have a transaction with exactly 10000 AZART:
```
{
    "12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx": "0"
}
```
5. The value on the left is your `txid` and the right is the `vout`.
6. Add a line to the bottom of the already opened `masternode.conf` file using the IP of your VPS (with port 24126), `private key`, `txid` and `vout`:
```
mn1 1.2.3.4:24126 3xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 12345678xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 0
```
7. Save the file, exit your wallet and reopen your wallet.
8. Go to the "Masternodes" tab.
9. Click "Start All".
10. You will see "WATCHDOG_EXPIRED". Just wait few minutes.

Congratulations! You have just owned a Azart Master Node. A master node helps improve the network and provide the network with certain anonymous and fast payments functions, also including governance functions.

## How can I check my masternode is working?

Assuming you are using the hot/cold wallet as recommended in our guides, check the following:

1. On Windows/Mac wallet masternode tab
The status field is ENABLED and the Active field is greater than 0:00.

2. In Windows/Mac wallet debug console
Type `masternode list-conf` and ensure everything looks correct, particularly that "status" : "ENABLED".

3. On linux vps (via PuTTY)
Type `azart-cli masternode status` and look for "message" : "Masternode successfully started".

If all these checks are successful, your masternode is working correctly. You may have to wait over 40 hours for your FIRST reward.

### Future improvements

* Create masternode user (move Azart files to user home).
* Masternode user should launch Azart service and cron job (and not root user).
* Create swap to improve OS performance.
* Increase security with UFW (Uncomplicated Firewall).
