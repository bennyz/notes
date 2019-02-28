# General commands
## make
preview make build process
  make -n

## sanlock
Every 20 seconds sanlock reads 1MB and writes 512 bytes to monitor and renew its leases in the lockspace.
  sanlock client gets -h 1

  sanlock direct dump /rhev/data-center/<sp_id>/<sd_id>/images/<image_group>/<volume_id>.lease
Sample output:
    offset                            lockspace                                         resource  timestamp  own  gen lver
  00000000 77847bfc-b39c-4406-8377-a9515267318a             b6956d4e-d0e0-4cb1-84f1-373dd661b1b5 0000187302 0001 0018 2
