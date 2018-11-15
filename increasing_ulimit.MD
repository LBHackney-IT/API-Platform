# Increasing ulimit

## What

We use the 'AWS optimised ec2' image. This is a distro is a spinoff of an older Redhat version. 

There are a number of application limits that have been set very low in this distribution one of these is the number of open files that a process can have (referred in *NIX as 'nofiles' ). This number defaults to 1024 and this default is passed into all containers by the docker host. This can be checked by calling  `ulimit -n`

## Why
Rails applications often have thousands of files open at runtime so this limit was being met (open sockets to DB's and logging services count to this limit). 

## How
Inside the VM access the docker config file located at `/etc/sysconfig/docker` change the line:
```
OPTIONS="--default-ulimit nofile=1024:1024"
```
to
```
OPTIONS="--default-ulimit nofile=104857:104857"
```

Then restart the docker daemon (Warning this will restart all containers on the node!)
`sudo service docker reload`
