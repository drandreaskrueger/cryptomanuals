# mining hints
on a VPS we are supposed to not run with full steam, but to limit the CPU usage per process is easy:
```
ssh root@163.172.162.229

sudo apt-get install -y cpulimit

cpulimit --help
cpulimit -e gsoiltestnet -l 70 -b
```

check the %CPU column in
```
top
```
before and after.



