# gsoil mainnet 1.3.3 instructions
Until `martymcfly2015`'s new code is fully tested, let us please use the proven [soilcurrency/go-ethereum](https://github.com/soilcurrency/go-ethereum) code. 

This is a short manual how to install that onto the same VPS that was created in [VPS-scaleway.md](VPS-scaleway.md).
To be prepared for later already, we call the binary `gsoil-1.3.3`.

### connect your VPS as user `soil`
```
ssh soil@163.172.162.229
```

### clone sources and build mainnet client
```
mkdir -p ~/SOIL; cd ~/SOIL
git clone https://github.com/soilcurrency/go-ethereum

cd ~/SOIL/go-ethereum
git checkout master
make clean
make
```
Takes a while, ends with 'Done building'. Then rename, and link binary into PATHed folder: 
```
mv build/bin/gsoil build/bin/gsoil-1.3.3
ln -sf ~/SOIL/go-ethereum/build/bin/gsoil-1.3.3 ~/go/bin/
cd ~/SOIL
```
### create peers file 
Get the newest enodes from http://178.62.133.174:9001/#/util/peers , or use these for now. This creates a file `~/SOIL/mainnetpeers.js` to seed your node with peers.
```
cat > ~/SOIL/mainnetpeers.js <<END_OF_MARKER
admin.addPeer("enode://0a43ccb2526dd5ad743969b9fefb7a93a84bd214d7119b4a0bc2b589d320698ddfdbebba448702daefc8e9fb3fa6687bfe90431952cc8bd4f363725e4b3702df@2.105.196.29:39420")
admin.addPeer("enode://0b5050bafb79ca7be0f1141ce1ccc2d6c5b080d7174dc72726e8edca01339610f15af13622169239ddeccee4548250fb936c2b0f79abd29cfd6cec9774f407ff@77.85.55.175:39420")
admin.addPeer("enode://1366a1b1f2a4057892afd8066ce999cf622adb9d6ad08017ff6b34d3d651a01161ccaf8bfe355e27a7b94248982329091067149639af3bd65dfef5aac044aba0@40.112.185.197:39420")
admin.addPeer("enode://77b71660f5cb025e337bfb9bebf0c71001849fab8786d7b13fe3e081841f35cbf463cfc46fd570ef09829553524806be76b051e4f16afa7daf77265eb9733335@192.241.129.165:39420")
admin.addPeer("enode://aa7b0925c125fa7b8ff4f175f7118d60b7a261e630b4af6113b52a05c2571ea47fd13e3591de840a9e140686fb1cf786744fb223fdacbd79cf061bd401234ca1@103.50.151.215:39420")
admin.addPeer("enode://df5fe1c441b280a2e11dc6948aa6e341bd5dffdedeecc2f2562b7c4039a3292d3c984c365a12194de71e37766b207a50678f560fbbc10dc1785eccc34fc95950@109.172.69.68:39420")
admin.addPeer("enode://f0b07d04dda71718f2692d93cf758ebb232e4bcddd8eab3dd84011fbb5e92d01ed142ae08a008805e61b8bdb1eaab0555cc0962612ba8be0c6fcc5729a0ff315@90.153.96.55:39420")
admin.addPeer("enode://46fce6fdc328596df97c8c2e4d1744b9bf5bfd540ff8cf95e4880d797bd92e2cb52a81f38a9a49e51079f875c1adbdc2d4d6f503253e2d7188a494d421bd968b@178.62.133.174:39420")
END_OF_MARKER
```

The discovery port is `39420` for the mainnet.

## Start mainnet node:
```
gsoil-1.3.3 js ~/SOIL/mainnetpeers.js 
```

after a while you should see:
> ... Block synchronisation started ...

To sync it can take much time. If that is your only choice, 
then CTRL-C the above, and restart it in the background (with "&"), 
then exit the connection, and do something else for the next 24 hours;
when you log back in it is probably synced:

```
# Ctrl-C
gsoil-1.3.3 js ~/SOIL/mainnetpeers.js &
exit
```

### use existing blockchaindata
You do not have to do this. But you can also copy the whole blockchain 
from an OLD Vps to this NEW Vps, there are many ways (zip unzip ...); this is direct copying:
```
ssh soil@my.old.VPS.machine
du ~/.soil/chaindata/
scp -r /home/soil/.soil/chaindata soil@163.172.162.229:/home/soil/.soil/
exit
```

You can watch it on the new machine while it happens
```
ssh soil@163.172.162.229
du ~/.soil/chaindata/
du ~/.soil/chaindata/
du ~/.soil/chaindata/
...
```

## start node, and attach JSRE console
in one terminal
```
gsoil-1.3.3 js ~/SOIL/mainnetpeers.js
```
in new terminal

``` 
ssh soil@163.172.162.229
gsoil-1.3.3 attach
```

and try out a few commands:
```
eth.blockNumber
web3.version
net.peerCount
admin.peers
admin.nodeInfo
exit
```

## start node permanently
Please support the SOIL network by leaving your node on 24/7. With such a VPS that is really easy - and cheap.

Connect, and make sure there is no existing process `gsoil-1.3.3` running:
```
ssh soil@163.172.162.229
ps aux | grep gsoil-1.3.3
```

if there is such a process, kill it by using its PID = the second column (in my case 21934)

```
kill 21934
ps aux | grep gsoil-1.3.3
```

now you start it, in the background ("&"), with all output (stdout and stderr = "2>&1") 
redirected (">") into the log file `~/SOIL/mainnet-log.txt` 

```
gsoil-1.3.3 js ~/SOIL/mainnetpeers.js > ~/SOIL/mainnet-log.txt 2>&1 &
```

#### check status

You can always come back, check the process, and its output:

```
ps aux | grep gsoil-1.3.3
tail -n 50 ~/SOIL/mainnet-log.txt
```

but you can also logout now:

```
exit
```

and it will continue to be running.

### Done
Thank you for supporting the SOIL network. 
Please report back to the [SOIL thread](https://bitcointalk.org/index.php?topic=1176709.new#new) - and spread the word.

---

(C) Andreas Krueger 2016, show appreciation: [SOIL] 0x82e6fac28f2c0d89b442f4b7c0d72fa47c54efa7
