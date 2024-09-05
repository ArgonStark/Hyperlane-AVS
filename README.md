# Hyperlane-AVS

### Install Dependecies 
```
# Update & Install Packages
sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y

# Install Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version

# Install Docker-Compose
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version

# Docker Permission to user
sudo groupadd docker
sudo usermod -aG docker $USER

# Install Go
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```

### Install Eigenlayer CLI 
```
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s
export PATH=$PATH:~/bin
mkdir  hyperlane-avs
cd hyperlane-avs
```

### Create Keys 
```
eigenlayer operator keys create --key-type ecdsa [keyname]
eigenlayer operator keys create --key-type bls [keyname]
```
ou can also create keys by piping your password to this command. This can help in automated key creation and will not prompt for password. 
```
echo "password" | eigenlayer operator keys create --key-type ecdsa [keyname]
```
### Import Keys 
```
echo "password" | eigenlayer operator keys import --key-type ecdsa [keyname] [privatekey]
```

## Fund ECDSA Wallet
Send some ETH ( I sent about $10 ) to the “address” field referenced in your operator.yaml file. This ETH will be used to cover the gas cost for operator registration in the subsequent steps.

### Operator Configuration and Registration
``` 
eigenlayer operator config create
```
- `operator address`: Your Eigenlayer ETH address
- `earnings address`: press `Enter`
- `ETH rpc url`: https://ethereum-rpc.publicnode.com
- `network`: mainnet
- `signer type`: local_keystore
- `ecdsa key path:`: /root/.eigenlayer/operator_keys/opr.ecdsa.key.json

The command will create two files: operator.yaml and metadata.json.

Upload Logo Image, Configure metadata.json, and Upload Both ( Metadata example : https://github.com/ArgonStark/Eigenlayer/blob/main/metadata.json ) 

Upload the logo of the operator to a publicly accessible location and set the url in your metadata.json file. Operator registration only supports .png images for now and must be less than 1MB in size.

### Update your `operator.yaml` : 
``` 
nano operator.yaml
```
Add your metadata.json raw url in github in front of metadata-url

### Registration Command 
```
eigenlayer operator register operator.yaml
```
### Checking Status of Registration
``` 
eigenlayer operator status operator.yaml
```

## Hyperlane AVS Registration 

### Install Nodejs 
``` 
# remove nodejs 
sudo apt-get remove nodejs
sudo apt-get remove npm

# install 
sudo apt-get update
curl -s https://deb.nodesource.com/setup_18.x | sudo bash 
sudo apt install nodejs -y
node -v
```

### Setup
``` 
# Install with NPM
npm install -g @hyperlane-xyz/cli
```

### Register your operator 
```
 hyperlane avs register --operatorKeyPath /root/.eigenlayer/operator_keys/opr.ecdsa.key.json \
    --chain <AVS_NETWORK> \
    --avsSigningKeyAddress <AVS_SIGNING_ADDRESS>
```
The AVS_NETWORK is the network you're registering on, which can be `ethereum` or `holesky`.

#### Confirming registration
```
hyperlane avs check --chain ethereum --operatorAddress <AVS_SIGNING_ADDRESS>
``` 
Replace your operator address with <AVS_SIGNING_ADDRESS>


