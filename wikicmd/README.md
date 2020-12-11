# wikicmd

## Python

#### how to install right-version pip?
`wget https://bootstrap.pypa.io/get-pip.py && python3 get-pip.py  && rm get-pip.py`

## GPUs
#### how to check cuda version and cudnn version?
`cat /usr/local/cuda/version.txt`.
`cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2`

## Linux

#### how to incrementally sync files?
`rsync -avz –progress -e "ssh -i ~/.ssh/id_rsa" <source> <destination>`

#### how to remotely copy files through proxy/jump servers?
`scp -oProxyCommand="ssh -W %h:%p <proxy-user>@<proxy-ip>" <source> <destination>`

#### how to configure ssh?
at `~/.ssh/config`
```
Host node007
    User node007-username
    ProxyJump proxy-username@proxy-ip
    HostName node007-ip
```

#### how to configure DNS service?
add the following lines into `/etc/resolv.conf`
```
nameserver 114.114.114.114
nameserver 8.8.8.8
```

#### how to remove files/folders in /tmp that belong to you?
```
cd /tmp && ls -l /tmp | grep xysun | awk '{print $9}' | xargs rm -rf && cd -

# delete files that have been over 3 days
cd /tmp && find /tmp -mtime +3 -ls 2>/dev/null | grep xysun | awk '{print $11}' | xargs rm -rf && cd -
```

#### how to solve 'Update errors : FetchFailedException and “Splitting up … failed”' while executing `apt update`
the reason is /tmp disk is full. Remove some files in /tmp.
