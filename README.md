# zfs-setup

# Information
* Just my step by step method to create my ZFS pools. It will not make any sense except for me.
* New HDDs are in a USB enclosure to sync with the old HHDs.
* I will remplace the old HDDS with the new one and rename the pool of the next HDDs to match the old pool
* zfs info: https://github.com/zfsonlinux/zfs/wiki/FAQ#selecting-dev-names-when-creating-a-pool

**Do not forget to use tmux or screen**

# Before
```
┌─[san]─[~]
└─⚬ zpool get ashift
NAME    PROPERTY  VALUE   SOURCE
pool01  ashift    12      local
pool02  ashift    0       default
```

```
┌─[san]─[~]
└─⚬ zpool status pool01
  pool: pool01
 state: ONLINE
  scan: 
config:

	NAME                                 STATE     READ WRITE CKSUM
	pool01                               ONLINE       0     0     0
	  raidz1-0                           ONLINE       0     0     0
	    ata-ST4000DM000-2AE166_WDH038Q8  ONLINE       0     0     0
	    ata-ST4000DM000-2AE166_WDH04JXT  ONLINE       0     0     0
	    ata-ST4000DM000-1F2168_Z301P41Y  ONLINE       0     0     0

errors: No known data errors
```

```
┌─[san]─[~]
└─⚬ zpool status pool02
  pool: pool02
 state: ONLINE
  scan: 
config:

	NAME                                         STATE     READ WRITE CKSUM
	pool02                                       ONLINE       0     0     0
	  ata-WDC_WD2002FAEX-007BA0_WD-WMAWP0195993  ONLINE       0     0     0
	  ata-WDC_WD2002FAEX-007BA0_WD-WMAY02609781  ONLINE       0     0     0

```

* Get attributes (pool01 only)
```
┌─[san]─[~]
└─⚬ zfs get all pool01
NAME    PROPERTY              VALUE                  SOURCE
pool01  type                  filesystem             -
pool01  creation              Sat Jul 12 10:47 2014  -
pool01  used                  5.03T                  -
pool01  available             1.98T                  -
pool01  referenced            15.2G                  -
pool01  compressratio         1.00x                  -
pool01  mounted               yes                    -
pool01  quota                 none                   default
pool01  reservation           none                   default
pool01  recordsize            128K                   default
pool01  mountpoint            /pool01                default
pool01  sharenfs              off                    default
pool01  checksum              on                     default
pool01  compression           off                    default
pool01  atime                 on                     default
pool01  devices               on                     default
pool01  exec                  on                     default
pool01  setuid                on                     default
pool01  readonly              off                    default
pool01  zoned                 off                    default
pool01  snapdir               hidden                 default
pool01  aclinherit            restricted             default
pool01  createtxg             1                      -
pool01  canmount              on                     default
pool01  xattr                 on                     default
pool01  copies                1                      default
pool01  version               5                      -
pool01  utf8only              off                    -
pool01  normalization         none                   -
pool01  casesensitivity       sensitive              -
pool01  vscan                 off                    default
pool01  nbmand                off                    default
pool01  sharesmb              off                    default
pool01  refquota              none                   default
pool01  refreservation        none                   default
pool01  guid                  8270128686657247824    -
pool01  primarycache          all                    default
pool01  secondarycache        all                    default
pool01  usedbysnapshots       0B                     -
pool01  usedbydataset         15.2G                  -
pool01  usedbychildren        5.02T                  -
pool01  usedbyrefreservation  0B                     -
pool01  logbias               latency                default
pool01  dedup                 off                    default
pool01  mlslabel              none                   default
pool01  sync                  standard               default
pool01  dnodesize             legacy                 default
pool01  refcompressratio      1.00x                  -
pool01  written               15.2G                  -
pool01  logicalused           5.04T                  -
pool01  logicalreferenced     15.2G                  -
pool01  volmode               default                default
pool01  filesystem_limit      none                   default
pool01  snapshot_limit        none                   default
pool01  filesystem_count      none                   default
pool01  snapshot_count        none                   default
pool01  snapdev               hidden                 default
pool01  acltype               off                    default
pool01  context               none                   default
pool01  fscontext             none                   default
pool01  defcontext            none                   default
pool01  rootcontext           none                   default
pool01  relatime              off                    default
pool01  redundant_metadata    all                    default
pool01  overlay               off                    default
```

* Strange, I never disabled atime. No idea why
```
┌─[san]─[~]
└─⚬ zfs get atime                                                                                                       
NAME                     PROPERTY  VALUE  SOURCE
pool01                   atime     on     default
pool01/Plex              atime     on     default
pool01/backup_ubuntu     atime     on     default
pool01/docker            atime     on     default
pool01/git               atime     on     default
pool01/jupyter-notebook  atime     on     default
pool01/minidlna          atime     on     default
pool01/salt              atime     on     default
pool02                   atime     on     default
pool02/timemachine       atime     on     default
pool03                   atime     on     default
pool03/Plex              atime     on     default
pool03/backup_ubuntu     atime     on     default
pool03/docker            atime     on     default
pool03/drivers           atime     on     default
pool03/git               atime     on     default
pool03/jupyter-notebook  atime     on     default
pool03/minidlna          atime     on     default
pool03/owncloud_backup   atime     on     default
pool03/salt              atime     on     default
```

# Create new ZRAID temporary pool
* New HDDs have no partition
```
┌─[san]─[~]
└─⚬ fdisk -l /dev/sd{g,h,i}
Disk /dev/sdg: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes


Disk /dev/sdh: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes


Disk /dev/sdi: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

*  I will not use the /dev/sd* but the IDs
* force ashift=12 for better perfs
 ```sh
┌─[san]─[/dev/disk/by-id]
└─⚬ zpool create -o ashift=12 pool03 raidz $(ls /dev/disk/by-id/usb*)
```

```
┌─[san]─[/dev/disk/by-id]
└─⚬ zpool status pool03
pool: pool03
state: ONLINE
scan: none requested
config:

NAME                                          STATE     READ WRITE CKSUM
pool03                                        ONLINE       0     0     0
	raidz1-0                                  ONLINE       0     0     0
	usb-ST8000DM_004-2CX188_152D00539000-0:0  ONLINE       0     0     0
	usb-ST8000DM_004-2CX188_152D00539000-0:1  ONLINE       0     0     0
	usb-ST8000DM_004-2CX188_152D00539000-0:2  ONLINE       0     0     0

errors: No known data errors
```

```
┌─[san]─[/pool02/backups/plex]
└─⚬ zpool get ashift                                                                                                     
NAME    PROPERTY  VALUE   SOURCE
pool01  ashift    12      local
pool02  ashift    0       default
pool03  ashift    12      local
```

* Check partitions
```
┌─[san]─[/dev/disk/by-id]
└─⚬ fdisk -l /dev/sd{g,h,i}
Disk /dev/sdg: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 3B1A48B7-46C7-4B4D-9369-CB18CADEC03D

Device           Start         End     Sectors  Size Type
/dev/sdg1         2048 15628036095 15628034048  7.3T Solaris /usr & Apple ZFS
/dev/sdg9  15628036096 15628052479       16384    8M Solaris reserved 1


Disk /dev/sdh: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: E0809A01-ED2C-3349-B6C9-AE4D379FF19F

Device           Start         End     Sectors  Size Type
/dev/sdh1         2048 15628036095 15628034048  7.3T Solaris /usr & Apple ZFS
/dev/sdh9  15628036096 15628052479       16384    8M Solaris reserved 1


Disk /dev/sdi: 7.3 TiB, 8001563222016 bytes, 15628053168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: E3EC7410-E933-EB45-ACAC-2478E4B410CE

Device           Start         End     Sectors  Size Type
/dev/sdi1         2048 15628036095 15628034048  7.3T Solaris /usr & Apple ZFS
/dev/sdi9  15628036096 15628052479       16384    8M Solaris reserved 1
 ```
 
 * check properties
 ```
┌─[san]─[/pool02/backups/plex]
└─⚬ zfs get all pool03
NAME    PROPERTY              VALUE                  SOURCE
pool03  type                  filesystem             -
pool03  creation              Fri Apr 19 19:56 2019  -
pool03  used                  4.54T                  -
pool03  available             9.49T                  -
pool03  referenced            160K                   -
pool03  compressratio         1.00x                  -
pool03  mounted               yes                    -
pool03  quota                 none                   default
pool03  reservation           none                   default
pool03  recordsize            128K                   default
pool03  mountpoint            /pool03                default
pool03  sharenfs              off                    default
pool03  checksum              on                     default
pool03  compression           off                    default
pool03  atime                 on                     default
pool03  devices               on                     default
pool03  exec                  on                     default
pool03  setuid                on                     default
pool03  readonly              off                    default
pool03  zoned                 off                    default
pool03  snapdir               hidden                 default
pool03  aclinherit            restricted             default
pool03  createtxg             1                      -
pool03  canmount              on                     default
pool03  xattr                 on                     default
pool03  copies                1                      default
pool03  version               5                      -
pool03  utf8only              off                    -
pool03  normalization         none                   -
pool03  casesensitivity       sensitive              -
pool03  vscan                 off                    default
pool03  nbmand                off                    default
pool03  sharesmb              off                    default
pool03  refquota              none                   default
pool03  refreservation        none                   default
pool03  guid                  2811666465543548798    -
pool03  primarycache          all                    default
pool03  secondarycache        all                    default
pool03  usedbysnapshots       0B                     -
pool03  usedbydataset         160K                   -
pool03  usedbychildren        4.54T                  -
pool03  usedbyrefreservation  0B                     -
pool03  logbias               latency                default
pool03  dedup                 off                    default
pool03  mlslabel              none                   default
pool03  sync                  standard               default
pool03  dnodesize             legacy                 default
pool03  refcompressratio      1.00x                  -
pool03  written               160K                   -
pool03  logicalused           4.55T                  -
pool03  logicalreferenced     49K                    -
pool03  volmode               default                default
pool03  filesystem_limit      none                   default
pool03  snapshot_limit        none                   default
pool03  filesystem_count      none                   default
pool03  snapshot_count        none                   default
pool03  snapdev               hidden                 default
pool03  acltype               off                    default
pool03  context               none                   default
pool03  fscontext             none                   default
pool03  defcontext            none                   default
pool03  rootcontext           none                   default
pool03  relatime              off                    default
pool03  redundant_metadata    all                    default
pool03  overlay               off                    default
```

# Re create pool01 FS on pool03 (no quota for now)
```sh
for folder in $(ls /pool01/); do echo zfs create pool03/${folder}; done
```

# Change ownership and permissions if needed (compare to pool01)
```
┌─[san]─[/dev/disk/by-id]
└─⚬ chown root:docker /pool03/docker

┌─[san]─[~sys/disk/by-id]
└─⚬ chown frederic /pool03/{git,salt,minidlna}

┌─[san]─[~sys/disk/by-id]
└─⚬ chown plex:plex /pool03/Plex

┌─[san]─[~sys/disk/by-id]
└─⚬ chmod 777 /pool03/Plex
```

# Disable atime
* Disabling atime on the pool will also disable it on all volumes
```
┌─[san]─[/pool02/backups/plex]
└─⚬ zfs set atime=off pool0{1,2,3}
```

```
┌─[san]─[/pool02/backups/plex]
└─⚬ zfs get atime -d2 pool0{1,2,3}
NAME                     PROPERTY  VALUE  SOURCE
pool01                   atime     off    local
pool01/Plex              atime     off    inherited from pool01
pool01/backup_ubuntu     atime     off    inherited from pool01
pool01/docker            atime     off    inherited from pool01
pool01/git               atime     off    inherited from pool01
pool01/jupyter-notebook  atime     off    inherited from pool01
pool01/minidlna          atime     off    inherited from pool01
pool01/salt              atime     off    inherited from pool01
pool02                   atime     off    local
pool02/timemachine       atime     off    inherited from pool02
pool03                   atime     off    local
pool03/Plex              atime     off    inherited from pool03
pool03/backup_ubuntu     atime     off    inherited from pool03
pool03/docker            atime     off    inherited from pool03
pool03/drivers           atime     off    inherited from pool03
pool03/git               atime     off    inherited from pool03
pool03/jupyter-notebook  atime     off    inherited from pool03
pool03/minidlna          atime     off    inherited from pool03
pool03/owncloud_backup   atime     off    inherited from pool03
pool03/salt              atime     off    inherited from pool03
```

# sync FS content
```
for folder in $(ls /pool01/); do rsync -av /pool01/${folder}/ /pool03/${folder}/; done
```


