# travis-lxd-optimized
This is a garden variety document on issues that are open/closed (and what you can do to potentially speed up LXD builds on ARM). 


## Throwaway env YAML (example of optimal yaml file) 

```yaml
sudo: required
dist: bionic
language: python
env:
 - LINT=1
 - IMAGE="ubuntu:trusty"
 - IMAGE="ubuntu:xenial"
 - IMAGE="ubuntu:bionic"
 - IMAGE="ubuntu:eoan"
 - IMAGE="images:ubuntu/focal/cloud"
 - IMAGE="images:centos/6/cloud"
 - IMAGE="images:centos/7/cloud"
 - IMAGE="images:centos/8/cloud"
script:
 - if [ ! -z ${LINT} ]; then echo "Hello, world!"; fi
# setup dependencies here to save time on simpler test environments
# wait for lxd socket to be ready after snap installation
# change permissions on lxd socket to allow travis user access
# (^^ this is a throw-away CI environment, do not do this at home)
 - if [ ! -z ${IMAGE} ]; then
      sudo apt remove -y --purge lxd lxd-client;
      sudo snap install lxd;
      sudo lxd waitready;
      sudo lxd init --auto;
      sudo chmod 666 /var/snap/lxd/common/lxd/unix.socket;
   fi
 - if [ ! -z ${IMAGE} ]; then
      lxc launch ${IMAGE} mymachinecontainer;
      lxc exec mymachinecontainer -- sh -c "lsb_release -a || cat /etc/redhat-release";
   fi
   ```
