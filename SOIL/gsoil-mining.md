# CPU mining 
The syntax is the same for testnet, and mainnet; however this will only really work out of the box on testnet, with its low difficulty. 
For mainnet, you need to connect GPUs (graphics cards), which is beyond this manual. Please you write one, thanks.

Back to the testnet. Situation: You have no testnet coins - yet.

Suppose you have started the [testnet node](gsoiltestnet.md), e.g. with 
```
gsoiltestnet js ~/SOIL/testnetpeers.js > ~/SOIL/testnet-log.txt 2>&1 &
tail -f ~/SOIL/testnet-log.txt
```
### Start mining, DAG - and counting your mined coins:
in the JSRE console
```
gsoiltestnet attach
```

```
miner.start()   
```
To first generate the DAG takes ~15 minutes, but only once. Watch it the other terminal (or in ~/SOIL/testnet-log.txt). 

When the DAG is ready, and the mining starts, this will tell you the hashrate of your CPU (in my case 51501 at cpulimit 70%):
```
eth.hashrate
```

which (if it is calculated like in bitcoin which I do not know) gives you 
the average seconds between two of your mined blocks:
```
eth.getBlock(eth.blockNumber).difficulty / eth.hashrate
```
I see a 20.194 there - which does make sense.

(Funfact: The same CPU-hashrate 51501 at current mainnet difficulty would need
141947 seconds = 39.4297 hours - for 1 block, i.e. 1 SOIL. 
Even at [scaleways](VPS-scaleway.md)'s supercheap €0.006/hour, CPUmining would only become profitable 
(with unchanged difficulty) at a SOIL price of 39017 Satoshi; 
or at 13656 Satoshi when I don't cpulimit, see below.)

### Balance

After a while you will find your first mined coins in your coinbase account:
```
web3.fromWei(eth.getBalance(eth.coinbase), "soil")
```

### Stop mining, and CPU usage. 

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

Very little. A non-mining node is supporting the network, but does not need much resources.
 

### Limit CPU usage when mining 
On a VPS (virtual private server) we are sharing a real server. 
So we are supposed to not run with full steam (otherwise we might get kicked).  
 
To limit the CPU usage per process is easy:
```
ssh root@163.172.162.229

sudo apt-get install -y cpulimit

cpulimit --help
cpulimit -e gsoiltestnet -l 70 -b
```
(70% is 0.35 of the 200% we have in 2 cores.)

Look at the %CPU column in
```
top
```
before and after you tell cpulimit to target the process `gsoiltestnet`.  

Just one twitch: make sure `gsoiltestnet attach` is exited *before* you start cpulimit, 
because otherwise cpulimit might throttle the attached JSRE console - which needs only very little CPU anyways.   

Good?

Please report any [feedback](https://bitcointalk.org/index.php?action=pm;sa=send;u=860710). Thanks.

### What now?
Please run a mainnet node. See [gsoilmainnet-1.3.3.md](gsoilmainnet-1.3.3.md). Thanks.

---

(C) Andreas Krueger 2016, yes very nice: [SOIL] 0x82e6fac28f2c0d89b442f4b7c0d72fa47c54efa7
