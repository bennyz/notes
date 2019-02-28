# ovirt-engine
## important tables
command_entities


# Vdsm
## vdsm-tool/vdsm-client

when installing from custom built source
```
  vdsm-tool configure --force
```
get volume info using vdsm-client
```
  vdsm-client Volume getInfo storagepoolID=sp_id storagedomainID=64f87b0f-88d6-49e9-b797-60d36c9df497 imageID=fa4feaa2-e699-46ca-8594-24b1484a9b40 volumeID=ec3317c9-dfd7-4802-8743-f168c26d0d85
```

update volume metadata using vdsm-client:
```
  $ cat update.json
  {
      "job_id":"<job_uuid>",
      "vol_info": {
          "sd_id": "<sd_id>",
          "img_id": "<img_id>",
          "vol_id": "<vol_id>",
          "generation": "<vol_gen>"
      },
      "legality": "ILLEGAL" # Updating metadata to illegal
      }
  }

  $ vdsm-client SDM update_volume -f update.json
```
reading block volume metadata:
1. Get lvs with tags
```
  lvs -o+tags
```
Sample output:
```
    LV                                   VG                                   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert LV Tags
    7b6463c1-b0f2-45c7-a3bc-e0d6fae2bdd3 64f87b0f-88d6-49e9-b797-60d36c9df497 -wi-a-----   1.00g                                                     IU_fa4feaa2-e699-46ca-8594-24b1484a9b40,MD_7,PU_ec3317c9-dfd7-4802-8743-f168c26d0d85
```

MD_7 means we have to skip 7 slots to read the metadata for the above volume

2. Read metadata using skip=7 according to above MD_7
```
  dd if=/dev/64f87b0f-88d6-49e9-b797-60d36c9df497/metadata skip=7 bs=512 iflag=direct count=1
```
Sample output:
```
  DOMAIN=64f87b0f-88d6-49e9-b797-60d36c9df497
  CTIME=1551198153
  FORMAT=COW
  DISKTYPE=DATA
  LEGALITY=ILLEGAL
  SIZE=2097152
  VOLTYPE=LEAF
  DESCRIPTION=
  IMAGE=fa4feaa2-e699-46ca-8594-24b1484a9b40
  PUUID=ec3317c9-dfd7-4802-8743-f168c26d0d85
  MTIME=0
  POOL_UUID=
  TYPE=SPARSE
  GEN=1
  EOF
  1+0 records in
  1+0 records out
  512 bytes (512 B) copied, 0.000549317 s, 932 kB/s
```

Update volume metadata directly using `dd` (unsafe):
1. Output the metadata to a file
```
  dd if=/dev/64f87b0f-88d6-49e9-b797-60d36c9df497/metadata skip=7 bs=512 iflag=direct count=1 of=md7.tmp
```

2. Edit the required field in the metadata (using `sed -i` or some text editor)

3. Write back the edited metadata to the logical volume with seek=7 instead of skip=7 to write it directly to
the correct slot
```
  dd if=md7.tmp seek=7 of=/dev/64f87b0f-88d6-49e9-b797-60d36c9df497/metadata count=1 iflag=direct
```
