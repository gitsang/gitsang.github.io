
```
# create swap file
dd if=/dev/zero of=/.swap bs=1048576 count=4096

# format swap
mkswap /.swap

# start swap
swapon /.swap

# check
free -h

# onboot
echo "/.swap swap swap defaults 0 0" >> /etc/fstab
```