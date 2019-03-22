# Fix Docker NFS Permissions for MacOS

Give sufficient permissions for mounting `/Users` and `/Application/MAMP/htdocs`

## Step 1 - Setup 
Setup Docker for Mac with native NFS: 

- https://medium.com/@sean.handley/how-to-set-up-docker-for-mac-with-native-nfs-145151458adc

## Step 2 - Give Permissions 
Adjust `/etc/exports` and `/etc/nfs.conf` 

```
LINE1="/Users -alldirs -mapall=$(id -u):$(id -g) localhost"
LINE2="/Users -alldirs -mapall=$(id -u):$(id -g) localhost"

FILE=/etc/exports
grep -qF -- "$LINE1" "$FILE" || sudo echo "$LINE1" | sudo tee -a $FILE
grep -qF -- "$LINE2" "$FILE" || sudo echo "$LINE2" | sudo tee -a $FILE

LINE="nfs.server.mount.require_resv_port = 0"
FILE=/etc/nfs.conf
grep -qF -- "$LINE" "$FILE" || sudo echo "$LINE" | sudo tee -a $FILE > /dev/null

sudo nfsd restart
```

## Step 3 - Confirm 
You should end up with the files:

### `/etc/exports`
```
/Users -alldirs -mapall=501:20 localhost
/Applications/MAMP/htdocs -alldirs -mapall=501:20 localhost
```

### `/etc/nfs.conf`
```
#
# nfs.conf: the NFS configuration file
#
nfs.server.mount.require_resv_port = 0
```

## References

- https://github.com/openeuropa/openeuropa/blob/master/docs/starting/tooling.md#using-docker-on-macos
- https://forums.docker.com/t/nfs-native-support/48531/3
- https://medium.com/@sean.handley/how-to-set-up-docker-for-mac-with-native-nfs-145151458adc
