# CosmWasmResourceBook

Blockchain technology has obviously captured the attention of a substantial number of people, some of whom are aspiring developers, a group that I belong to. But, at least for me, the learning curve is equivalent to the Mongells trying to overcome great wall of China. 
 
A little background first. I fit in the category of coders who are self-taught and as a self-taught coder there is a large void in my understanding. As an example, I have always known that development required the use of an array of interconnected frameworks that make a system function. However, what I did not know is that this was called a tech stack... 
 
So, this is a resource dedicated to these people, my people, the people like me. The large swaths of aimless souls crawling on the surface of true development. The reason for building this resource is to anchor myself. To act as a starting point for my development career, which to this point has been non-existent. 
 
Enough story telling lets get into it. 
 
Obviously the first thing is to setup the environment, lets start there. 


## Environment Setup

I will assume that because it's 2022 your setup is Windows 10/11 runing `WSL2`.
If you don't know what I'm talking about or simply wish to following along, check out the resource below before moving on.

Cooming Soon. A few resources I need to compile to make this as straight forward as possible.
* WSL2 







### WSL2 Installation
As of now the `WSL2 Installation` steps aren't tested. They are built from resources and memory. I will take some time to walk through these steps myself on a fresh machine to test and make revisions where nessecary.

https://pureinfotech.com/install-windows-subsystem-linux-2-windows-10/







#### Basic Dependencies

Run the following commands to finish he basic dependencies.
```
# update the local package list and install any available upgrades
sudo apt-get update && sudo apt upgrade -y

# install toolchain and ensure accurate time synchronization
sudo apt-get install make build-essential gcc git jq chrony -y
```





#### SSH Setup



### Install Docker




### Install Rust

First, install rustup. 

 `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

Once installed, make sure you have the wasm32 target:

```rustup default stable
cargo version
# If this is lower than 1.50.0+, update
rustup update stable

rustup target list --installed
rustup target add wasm32-unknown-unknown
```




### Install Go
Remove any previous Go installation (You may need to run the command as root or through sudo).
`rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.linux-amd64.tar.gz`

Next Install Go
```
wget https://golang.org/dl/go1.17.1.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.17.1.linux-amd64.tar.gz
```

After that, open your users .profile file `nano ~/.profile` and add the following lines at the bottom of the file.
```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
```

After saving those changes run `source ~/.profile`.


### Install Git
#### Configure git to use ssh.
Always remember when cloning a repo to reformat the url to git@github.com:user/the-repository.git.


### Install Juno

This is taken directly from the Juno Docs with some steps ommitted and added.
https://docs.junonetwork.io/smart-contracts-and-junod-development/getting-started

#### Build from source.
Clone the Juno repository. Fetch. Checkout the current `testnet` version.

```
git clone https://github.com/CosmosContracts/juno
cd juno
git fetch
git checkout <version-tag>
```

#### Determining what version tag to use.

As of writing the `<version-tag>` we will use is `v2.1.0`. 
Make sure to look at the `Current Github version tag` in the Juno docs.
https://docs.junonetwork.io/validators/joining-the-testnets#current-testnets

The resulting command then is. 
```
git checkout v2.1.0
```


You should get a response that looks like, or includes the following.
```
HEAD is now at e6b8c21 Add moneta-patch base upgrade handler (#135)
```

If your output includes the above but is preceeded by a big body of text that looks like this.
```
Note: switching to 'v2.1.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false
```

you can run `git config --global advice.detachedHead False` to minimize output.

You can run `git checkout v2.1.0` again to see this change if you want.

#### Building
Using this version of the repo we can now build. Make sure you are still in the juno directory.
'''
make install
'''

To confirm that the installation has succeeded, you can run:

```
junod version
```
You can see that the github `version-tag` we used for checkout is the version that has been installed.

You may have also noticed the cli for juno is appended by a `d` that is because we have installed the juno daemon.

#### Configuring the environment.

The following section in the offical docs sets these system variables only for the current shell session. They do not persist when the shell is closed. I will assume that you will forget that you even set system variables when you come back later on. So, we will add these system variables in the `~/.profile` file so that they persist. Afterwards, you won't need to worry about these variables again.

So open the `.profile` file by running `nano ~/.profile` and we will add the following system variables at the bottom of the file.

First, we choose a testnet by specifying the `chain-id`. This is taken from the docs. After that we set the nodes moniker name.


```
CHAIN_ID=uni-2
MONIKER_NAME="Validatron 9000"
```

Save the file and run `source ~/.profile` for these changes to take place or restart your shell.

Check that these changes did take place by checking the variables.

```
echo $CHAIN_ID
echo $MONIKER_NAME
```

#### Set persistent peers
In the following section of the offical docs we are told to persistent peers. This is to allow our node to join the netwrok and communicate with other test nodes. As normal for me, I could not get this to work. Good news though, we are simply running a local test node and have no need to communicate with other test nodes so we can skip this part.

But for completions sake here are the commands from the offical doc.
```
#Set the base repo URL for the testnet & retrieve peers
CHAIN_REPO="https://raw.githubusercontent.com/CosmosContracts/testnets/main/$CHAIN_ID" && \
export PEERS="$(curl -s "$CHAIN_REPO/persistent_peers.txt")"

# check it worked
echo $PEERS
```
As of now the commands are trying to pull persistent peers from this file that does not seem to exist/host content.
https://raw.githubusercontent.com/CosmosContracts/testnets/main/uni-2/persistent_peers.txt


#### Setting the minimum gas price.
You should have no problems not running this but since it's covered in the docs we will do it anyways.
```
# note testnet denom
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ujunox\"/" ~/.juno/config/app.toml
```


### Setting up the Node
Were almost done with the setup, just a few more steps.
Note: We are only setting up a node not a validator. See https://docs.junonetwork.io/validators/joining-the-testnets#setting-up-the-node for how to upgrade your node to a validator.

#### Initialize the chain
`junod init "$MONIKER_NAME --chain-id $CHAIN_ID`

This will generate the following files in `~/.juno/config/`
* genesis.json
* node_key.json
* priv_validator_key.json


#### Download the genesis file
```
curl https://raw.githubusercontent.com/CosmosContracts/testnets/main/$CHAIN_ID/genesis.json > ~/.juno/config/genesis.json
```

This will replace the genesis file created using the `junod init` command with the genesis file for the testnet


#### Persistent peers again
Skiping persistent peers again. Here is the link https://docs.junonetwork.io/validators/joining-the-testnets#set-persistent-peers-1


#### Create a local key pair
Now we must create a local key pair for our test node.

```
junod keys add <key-name>

# Query the keystore for your public address
junod keys show <key-name> -a
```
Replacing `<key-name>` with a key name of your choosing.
I choose `resourcebook` as my key name which results in the following:

```
junod keys add resourcebook
```

##### IMPORTANT
After creating a new key, the key information and seed phrase will be shown. It is essential to write this seed phrase down and keep it in a safe place. The seed phrase is the only way to restore your keys. I like to store my seed pharse in a file close to my key. Copy your seed phrase to past in the following command.

```
mkdir ~/.juno/seeds
echo "<seed phrase>" >> ~/.juno/seeds/<key-name>_seed.txt

# Example
echo "word1 word2 word3 word4 word5 word6 word7 word8 word9 ... word24" >> ~/.juno/seeds/resourcebook_seed.txt
```

You can check you seed phrase by running: `cat ~/.juno/seeds/resourcebook_seed.txt`


# Query the keystore for your public address
junod keys show resourcebook -a
```

If you already have a key from a previous testnet, you can recover it using the mnemonic:
```
junod keys add <key-name> --recover
```

 One final step.
 
 #### Get some testnet tokens
 Testnet tokens can only be requested from the #faucet channel on Discord https://discord.gg/HnHKpzd3Db
 To request tokens type `$request <your-public-address>` in the message field and press enter.
 
 The offical documentation continues from here for the purpose of upgrading your node to become a validator, something we have no need for yet.
 
 Thats it! Congratulations on installing juno and setting up your very own local testnet node. Although we are isolated, not connected to the rest of the testnet, we can begin following the various tutorials on how to get started with Juno development. 
 
 Some resources that you should immediatley check out is:
 * Callum's Zero to Hero Course https://github.com/Callum-A/cosmwasm-zero-to-hero
 * Envoy Labs erc20 Tutorial https://docs.junonetwork.io/smart-contracts-and-junod-development/tutorial-erc-20
 
 That's all I have for now. I will be constantly updating this page with more resources as I make my way through the 

 






 
