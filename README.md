Totem: Ubuntu
=========================

This repos is used to build a base Ubuntu image for use in Docker.

## Image Name

This image can be found in the repository at:

```
totem/ubuntu:raring
```

## Building

To build this image, simply run `docker build -t totem/ubuntu:raring .` from the root of this repository.

## Details

The base Ubuntu system used by this repository is contained in the `raring.tar.gz` file. This file was created by running the following on an existing raring system:

```bash
# Install base ubuntu into folder
debootstrap --variant=minbase --verbose raring ./raring

# Clean up caches
rm -fr ./raring/var/cache/apt/archives/*.deb

# Setup sane sources list
cat > ./raring/etc/apt/sources.list << EOF
deb http://archive.ubuntu.com/ubuntu raring main universe multiverse restricted
deb-src http://archive.ubuntu.com/ubuntu raring main universe multiverse restricted
deb http://archive.ubuntu.com/ubuntu raring-updates main universe multiverse restricted
deb-src http://archive.ubuntu.com/ubuntu raring-updates main universe multiverse restricted
deb http://security.ubuntu.com/ubuntu raring-security main universe multiverse restricted
deb-src http://security.ubuntu.com/ubuntu raring-security main universe multiverse restricted
EOF

# Prevent daemons from being started on installation or upgrades
cat  > ./raring/usr/sbin/policy-rc.d << EOF
#!/bin/sh

exit 101
EOF
chmod +x ./raring/usr/sbin/policy-rc.d

# Prevent APT from installing unwanted packages
cat > ./raring/etc/apt/apt.conf.d/25norecommends << EOF
APT
{
    Install-Recommends  "false";
    Install-Suggests    "false";
};
EOF

cd ./raring
tar -zcvf ../raring.tar.gz .
```

See: [http://sandbox.dotcloud.com/user/advanced.html#creating-base-images](http://sandbox.dotcloud.com/user/advanced.html#creating-base-images) for more details on how this image was setup.
