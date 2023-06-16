# Tendermint exporter
## Description
When using the standard Tendermint Prometheus exporter, our team found that sometimes the validator miss counter breaks down and starts to increase continuously without being able to fix it without recreating the validator.
We've developed a simple and easy-to-use tool that allows you to retrieve validator missed blocks data via a request to the blockchain API.

## Getting Started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

## Running the application

### Prerequisites
- [Node.js](https://nodejs.org)

#### Step 1: Clone this repo to your server and change directory.
```shell
git clone https://github.com/SergeyMitk/tendermint-exporter
cd tendermint-exporter
```
#### All further actions are carried out in the tendermint-exporter folder. 

#### Step 2: Install npm dependencies 
```shell
npm install
```

### Step 3: Exporter config
Copy ".env.example" file to ".env" and make changes in ".env" according to your needs.
```shell
cp .env.example .env
```

#### Step 4: Create systemd service file
```shell
tee $HOME/exporterd.service > /dev/null <<EOF
[Unit]
  Description=Validator missed blocks counter
  After=network-online.target
[Service]
  User=$USER
  WorkingDirectory=$(pwd)
  ExecStart=$(which node) $(readlink -f index.js)
  Restart=on-failure
  RestartSec=10
  LimitNOFILE=65535
[Install]
  WantedBy=multi-user.target
EOF
```

### Step 5: Copy systemd file to the working directory and start exporter
```shell
sudo mv $HOME/exporterd.service /etc/systemd/system/
sudo systemctl enable exporterd
sudo systemctl daemon-reload
sudo systemctl start exporterd
sudo journalctl -u exporterd -f -o cat
```
