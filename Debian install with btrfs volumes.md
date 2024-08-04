---
blog: "true"
---
Inspired by: https://gist.github.com/jo-makar/fdaff13ffd830c84b5e130f77a7632b2

The debian installer doesn't have an easy way to setup btrfs volumes. The only way to do that seems to be to pick advanced mode and drop to a terminal right after setting up disk partitioning, where you just setup everything by hand.

That is quite cumbersome and error-prone. 

## Create a netinstall image with sshd enabled

Download and unpack the netinstall image
```sh
export IMAGE=debian-testing-amd64-netinst
wget https://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd64/iso-cd/$IMAGE.iso
```

Unpack the iso into a directory and extract the MBR
```sh
# install bsdtar
sudo apt install libarchive-tools

# unpack the iso
mkdir $IMAGE
bsdtar -C $IMAGE -xf $IMAGE.iso

# extract the MBR
dd if=$IMAGE.iso of=isohdpfx.bin bs=1 count=432
```

We need to modify the mkisofs script:
- remove options that begin with -jigdo and the -checksum_algorithm_iso and -md5-list options
- change the -o option to the path of the to-be-created iso
- change the -isohybrid-mbr to the isohdpfx.bin file created
- change the final directories to be $IMAGE/

```sh
cat $IMAGE/.disk/mkisofs | awk -v img="$IMAGE" '{for (i=1; i<=NF; i++) {if ($i == "-o") {printf "-o %s-1.iso ", img; i++} else if ($i == "-isohybrid-mbr") {printf "-isohybrid-mbr isohdpfx.bin "; i++} else if ($i ~ /^-jigdo/ || $i ~ /^-checksum/ || $i ~ /^-md5/) {i++} else if ($i == "boot1" || $i == "CD1") {printf "%s ", img; i++} else {printf "%s ", $i}}; print ""}' > mkisofs
```

Replace the isolinux boot menu with a single preseed-based installation
```
cd $IMAGE/isolinux
cp isolinux.cfg isolinux.cfg.orig

# modify isolinux.cfg such that (replacing the ip of course):
diff isolinux.cfg.orig isolinux.cfg
4,5c4,7
< include menu.cfg
< default vesamenu.c32
---   
> default installssh 
> label installssh
>     kernel /install.amd/vmlinuz
>     append vga=788 initrd=/install.amd/initrd.gz --- quiet auto=true priority=critical interface=auto hw-detect/load_firmware=false url=http://10.0.0.117:8000/preseed.cfg
# the installssh portion is based on the contents of txt.cfg with the "auto=true ..." added
cd ../..

# run the mkisofs (xorriso) command generated above to build the iso
# and copy the new iso to a usb stick normally with dd
```



