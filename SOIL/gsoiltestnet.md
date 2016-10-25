# gsoiltestnet-1.4.1 instructions

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

#### peernodes
create a file for easier starting later:
```
cat > ~/SOIL/testnetpeers.js <<END_OF_MARKER
admin.addPeer("enode://debbd72816d1c6cfbcfae37e842a6906b122b9a2b196419e39b3087383e3ff4500e462dde66d88cc08aac20c777f6e57ecfa2c541e824b7f528a061e97cf378b@192.52.166.129:39340")
admin.addPeer("enode://dea8948426de2527d5d6d7b8a31f8a233813d850db480c1346db1b8fd16499f9f110ada786f844491dc79a99b148cb2a91113a82a6cb70856f87dc32f97b5fdf@163.172.185.74:39340")
admin.addPeer("enode://ed51b7725c81f250e50c56e48a97ef674fac1e34621550aff9c082b70f2d4839f4812ce2f70a8e97f2e1c41b9f7e97d9881a727352cd005a2cb962020867dda4@163.172.162.229:39340")
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
One account for mining:
```
gsoiltestnet account new
```
One account for testing, donations, etc:
```
gsoiltestnet account new
```

### attach JSRE console
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
you have no coins - yet. 

### start mining, DAG - and counting your mined coins:
```
miner.start()   
```
will first generate the DAG, necessary only once. Watch the other terminal, it takes ~15 minutes. 

Then when the mining starts, it will tell you more than 0 for this (in my case 131979):
```
eth.hashrate
```

and then you will find your first mined coins in your coinbase account:
```
web3.fromWei(eth.getBalance(eth.coinbase), "soil")
```

### stop mining, and CPU usage. 

Now leave the JSRE, and look at the CPU usage during the mining:
```
exit
top
```
Maximum usage. 

When you've had enough, check the coin balance, **stop mining**, and look at the CPU usage again.
```
gsoiltestnet attach
web3.fromWei(eth.getBalance(eth.coinbase), "soil")
miner.stop()
exit
top
```

A non-mining node is supporting the network, but does not need much resources.

### and now?

"... does not need much resources". Which allows us to use this one VPS for *two* networks. For this testnet ^

and for the --> mainnet, which we install on the same machine. **Now continue in [gsoilmainnet-1.3.3.md](gsoilmainnet-1.3.3.md) if you want to.**


Keep the testnetnode on for a few days, this is how to let it run in the background:
```
gsoiltestnet js ~/SOIL/testnetpeers.js > ~/SOIL/testnet-log.txt 2>&1 &
exit
```

Thanks for trying this out. Please report your `enode://...@IP:39340` in the [SOIL thread](https://bitcointalk.org/index.php?topic=1176709.new#new).

---

(C) Andreas Krueger 2016, say thankyou: [SOIL] 0x82e6fac28f2c0d89b442f4b7c0d72fa47c54efa7
