# gsoiltestnet-1.4.1 instructions
Installation procedure with explanations, good for learning. If you are experienced, use the [quickstart](gsoiltestnet-quickstart.txt). Or for a more basic, quicker way: [this one (scroll down to 'Install')](https://community.c9.io/t/cryptocurrency-soil-start-node-how-to-doesnt-connect/10707#install). 

### Install into your new VPS 
log into your new [VPS-scaleway.md](VPS-scaleway.md) server, with user soil:
```
ssh root@163.172.162.229
login soil
```

### GOPATH, PATH, and folder for scripts & shortcuts
make golang known:
```
mkdir -p ~/go/bin
echo "export GOPATH=\$HOME/go" >> ~/.bashrc
echo "export PATH=\$PATH:\$HOME/go/bin:/usr/local/go/bin" >> ~/.bashrc
source ~/.bashrc
```


### SOIL v1.4.1 testnet
clone sources and build testnet client
```
mkdir -p ~/SOIL
cd ~/SOIL
git clone https://github.com/martymcfly2015/go-soil.git
cd ~/SOIL/go-soil/

git checkout Testnet
make
```
Takes a while, ends with 'Done building'. Then rename, and link into PATHed folder:
```
mv build/bin/gsoil build/bin/gsoiltestnet
ln -s ~/SOIL/go-soil/build/bin/gsoiltestnet ~/go/bin/
cd ~
```

#### Peernodes to connect to
create a file `~/SOIL/testnetpeers.js` for easier starting later:
```
cat > ~/SOIL/testnetpeers.js <<END_OF_MARKER
admin.addPeer("enode://debbd72816d1c6cfbcfae37e842a6906b122b9a2b196419e39b3087383e3ff4500e462dde66d88cc08aac20c777f6e57ecfa2c541e824b7f528a061e97cf378b@192.52.166.129:39340")
admin.addPeer("enode://ed51b7725c81f250e50c56e48a97ef674fac1e34621550aff9c082b70f2d4839f4812ce2f70a8e97f2e1c41b9f7e97d9881a727352cd005a2cb962020867dda4@163.172.162.229:39340")
admin.addPeer("enode://e310e882ea6dc0e57472b21f9029d4633bc24682b032bcf19da7b9d70e1fde5dd3715b08c124b03e89c9069d980e77dbd6bbe8782668031d5c2a28c6569afb76@163.172.187.101:39340")
END_OF_MARKER
```

The discovery port is `39340` for the testnet. 

## Start testnet node:
```
gsoiltestnet js ~/SOIL/testnetpeers.js 
```
After a few seconds you should see "... Block synchronisation started ...". And if anyone is mining, blocks are imported continuously. 

### coinbase & second account
Leave the above terminal open, so you can watch it. Start a new terminal:
 
```
ssh soil@163.172.162.229
```

Create 2 accounts, perhaps with 1 passphrase - but better do not use the same passphrase as on mainnet.  
One account for mining (will become your `eth.coinbase` account):
```
gsoiltestnet account new
```
One account for testing, donations, etc (will become your second account `eth.accounts[1]`):
```
gsoiltestnet account new
```

### Attach JSRE console
```
gsoiltestnet attach
```
you should see something like
> instance: Gsoil_MMF_TN/v1.4.1-430cc246/linux/go1.6.3  
> datadir: /home/soil/.soiltnet  
> coinbase: 0xf385fd6addfd1fa039c1bc6e698998bb60a8fb5f  
> at block: 2365 (Mon, 24 Oct 2016 21:48:44 UTC)  
> modules: admin:1.0 db:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 shh:1.0 txpool:1.0 web3:1.0  

and a prompt >

try out a few commands:
```
eth.blockNumber
net.peerCount
admin.peers
admin.nodeInfo
web3.version
eth.accounts
eth.getBalance(eth.coinbase)
```

You have no coins - yet. For how to mine coins, see the manual [gsoil-mining.md](gsoil-mining.md) later.

Or [tell us](https://bitcointalk.org/index.php?topic=1176709.new#new) one of your addresses, then we send testnet coins.


### Send money

Check balances. Send coins. Check balances. 
```
eth.accounts

web3.fromWei(eth.getBalance(eth.accounts[0]), "soil")
web3.fromWei(eth.getBalance(eth.accounts[1]), "soil")

eth.sendTransaction({value:web3.toWei(42, "soil"), from:eth.coinbase, to:eth.accounts[1]})
```
input your passphrase, the answer should be a transaction hash.  

Then wait, until the transaction has been mined. You see that in the node output window/logfile. 

After a while, 42 soil should have moved from one account to the other:
```
web3.fromWei(eth.getBalance(eth.accounts[0]), "soil")
web3.fromWei(eth.getBalance(eth.accounts[1]), "soil")
```

### Longrunner - start node in background, and exit

Please keep the testnetnode on for a few days; this is how to let it run in the background:

```
gsoiltestnet js ~/SOIL/testnetpeers.js > ~/SOIL/testnet-log.txt 2>&1 &
```

A non-mining node is supporting the network, and does not need much resources. So you can exit now:

```
exit
```

and when you log back in, it will still be there. Examine the situation with

```
ps aux | grep gsoiltestnet
tail -n 50 ~/SOIL/testnet-log.txt
```

Thanks for trying this out. Please report your `enode://...@IP:39340` in the [SOIL thread](https://bitcointalk.org/index.php?topic=1176709.new#new).


### and now?

^ "... and does not need much resources". Which allows us to use this one VPS for *two* networks. For this testnet ^

and for the --> mainnet, which we install on the same machine. **Now continue in [gsoilmainnet-1.3.3.md](gsoilmainnet-1.3.3.md) if you want to.**


---

(C) Andreas Krueger 2016, say thankyou: [SOIL] 0x82e6fac28f2c0d89b442f4b7c0d72fa47c54efa7
