# alephium-rpi

[Alephium](https://alephium.org/) is the first live Layer 1 sharded blockchain scaling and improving on Bitcoin core technologies, Proof of Work & UTXO. It delivers a highly performant, secure DeFi & Dapps platform with enhanced energy efficiency.

This repo is primarily intended for developers running headless Raspberry Pi's (no desktop environment) who want to use the various blockchain services for Alephium. With that said, individuals on the desktop version can still follow this guide by executing the same commands in their Pi terminal window.

## [Alephium Node](https://github.com/alephium/alephium)

The full node is a critical piece of infrastructure to interact, host, and synchronize a copy of the entire Alephium blockchain. There is no financial incentive to run a node at this point in time but doing so increases the security of the network.

> Note: since July 26th, 2022, it takes approximately 22 hours for a full node sync on a RPi-4 with 4gb RAM using a 256gb MicroSD card.

### Minimum requirements

* Raspberry Pi 4 with 4GB RAM 
* Installed Raspberry Pi OS (64-bit) with the [official imager](https://www.raspberrypi.com/software/)

### Prepare installation
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install default-jdk -y
```

### Download JAR

Find the latest release in the [Alephium GitHub](https://github.com/alephium/alephium/releases).

```bash
wget https://github.com/alephium/alephium/releases/download/v<VERSION>/alephium-<VERSION>.jar
````

### Launch node

```bash
java -jar alephium-<VERSION>.jar
```

### Generate secret API key

The following creates a random alphanumeric string that is 64 characters long.

```bash
`cat /dev/urandom | LC_ALL=C tr -dc 'a-zA-Z0-9' | fold -w 64 | sed 1q`
```

### Edit config file 

The following command opens up the text editor.

```bash
sudo nano ./alephium/user.conf 
```

Then add your secret API key from the previous step into the `alephium.api.api-key` field.

```bash
alephium.api.api-key = "<API-SECRET-KEY>"
alephium.api.network-interface = "0.0.0.0"
```

If you're running a headless Pi, you'll also want to include the last line above which allows you to access the node from another computer on the same subnet.

Save the file with `CTRL + X` then `Y` and `ENTER` to confirm.

### Restart node

This step is required for the settings to take effect.

## Swagger 

OpenAPI is used to interact with the full node and the UI can be accessed via `http://<RPI-IP-ADDRESS>:12973/docs` (default is `127.0.0.1`). However, below are a few commands that may be helpful to get you up and running.

### Node sync status

```bash
 curl -X 'GET' \
  'http://<RPI-IP-ADDRESS>:12973/infos/self-clique' \
  -H 'accept: application/json' \
  -H 'X-API-KEY: <API-SECRET-KEY>'
 ```

### Create a new wallet

A new wallet will be created and respond with a mnemonic. Make sure to keep that mnemonic safely as it allows you to recover your wallet!

```bash
curl -X 'POST' \
  'http://<RPI-IP-ADDRESS>:12973/wallets' \
  -H 'accept: application/json' \
  -H 'X-API-KEY: <API-SECRET-KEY>' \
  -H 'Content-Type: application/json' \
  -d '{
  "password": "<SECRET-PASSWORD>",
  "walletName": "<AWESOME-WALLET-NAME>"
}'
```

### Restore wallet

```bash
curl -X 'PUT' \
  'http://<RPI-IP-ADDRESS>:12973/wallets' \
  -H 'accept: application/json' \
  -H 'X-API-KEY: <API-SECRET-KEY>' \
  -H 'Content-Type: application/json' \
  -d '{
  "password": "<SECRET-PASSWORD>",
  "mnemonic": "<ENTER YOUR SEED PHRASES HERE>",
  "walletName": "<AWESOME-WALLET-NAME>"
}'
```

### Unlock wallet

```bash
curl -X 'POST' \
  'http://<RPI-IP-ADDRESS>:12973/wallets/wallet-super-name/unlock' \
  -H 'accept: */*' \
  -H 'X-API-KEY: <API-SECRET-KEY>' \
  -H 'Content-Type: application/json' \
  -d '{
  "password": "<SECRET-PASSWORD>"
}'
```

### Get wallet balance

```bash
curl -X 'GET' \
  'http://<RPI-IP-ADDRESS>:12973/wallets/alpheiumnode/balances' \
  -H 'accept: application/json' \
  -H 'X-API-KEY: <API-SECRET-KEY>'
```

