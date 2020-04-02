# zfs new setup

# Information
* Just my step by step method to create my ZFS pools. It will not make any sense except for me.
* New HDDs are in an USB enclosure to sync with the old HHDs.
* I will remplace the old HDDS with the new one and rename the new pool to match the old pool.
* ZFS info: https://github.com/zfsonlinux/zfs/wiki/FAQ#selecting-dev-names-when-creating-a-pool
* I will use root user because I am lazy.
* /dev/disk/by-id/wwn* are the best candidates: https://en.wikipedia.org/wiki/World_Wide_Name

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

* Get pool attributes (pool01 only)

```
┌─[san]─[/pool02/backups/plex]
└─⚬ zpool get all pool01
NAME    PROPERTY                       VALUE                          SOURCE
pool01  size                           10.9T                          -
pool01  capacity                       69%                            -
pool01  altroot                        -                              default
pool01  health                         ONLINE                         -
pool01  guid                           10964498760137193668           -
pool01  version                        -                              default
pool01  bootfs                         -                              default
pool01  delegation                     on                             default
pool01  autoreplace                    off                            default
pool01  cachefile                      -                              default
pool01  failmode                       wait                           default
pool01  listsnapshots                  off                            default
pool01  autoexpand                     off                            default
pool01  dedupditto                     0                              default
pool01  dedupratio                     1.00x                          -
pool01  free                           3.32T                          -
pool01  allocated                      7.56T                          -
pool01  readonly                       off                            -
pool01  ashift                         12                             local
pool01  comment                        -                              default
pool01  expandsize                     -                              -
pool01  freeing                        0                              -
pool01  fragmentation                  14%                            -
pool01  leaked                         0                              -
pool01  multihost                      off                            default
pool01  feature@async_destroy          enabled                        local
pool01  feature@empty_bpobj            active                         local
pool01  feature@lz4_compress           active                         local
pool01  feature@multi_vdev_crash_dump  enabled                        local
pool01  feature@spacemap_histogram     active                         local
pool01  feature@enabled_txg            active                         local
pool01  feature@hole_birth             active                         local
pool01  feature@extensible_dataset     active                         local
pool01  feature@embedded_data          active                         local
pool01  feature@bookmarks              enabled                        local
pool01  feature@filesystem_limits      enabled                        local
pool01  feature@large_blocks           enabled                        local
pool01  feature@large_dnode            enabled                        local
pool01  feature@sha512                 enabled                        local
pool01  feature@skein                  enabled                        local
pool01  feature@edonr                  enabled                        local
pool01  feature@userobj_accounting     active                         local
```

* Get volumes attributes (pool01 only)
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

* Get quota
```
┌─[san]─[/pool02/backups/plex]
└─⚬ zfs get quota
NAME                     PROPERTY  VALUE  SOURCE
pool01                   quota     none   default
pool01/Plex              quota     6T     local
pool01/backup_ubuntu     quota     none   default
pool01/docker            quota     none   default
pool01/git               quota     none   default
pool01/jupyter-notebook  quota     none   default
pool01/minidlna          quota     none   default
pool01/salt              quota     none   default
pool02                   quota     none   default
pool02/timemachine       quota     none   default
pool03                   quota     none   default
pool03/Plex              quota     none   default
pool03/backup_ubuntu     quota     none   default
pool03/docker            quota     none   default
pool03/drivers           quota     none   default
pool03/git               quota     none   default
pool03/jupyter-notebook  quota     none   default
pool03/minidlna          quota     none   default
pool03/owncloud_backup   quota     none   default
pool03/salt              quota     none   default
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

* I will not use the /dev/sd* but the IDs
* Force ashift=12 for better perfs
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

## Check ashift
```
┌─[san]─[/pool02/backups/plex]
└─⚬ zpool get ashift                                                                                                     
NAME    PROPERTY  VALUE   SOURCE
pool01  ashift    12      local
pool02  ashift    0       default
pool03  ashift    12      local
```

## Check partitions
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
 
 ## Check properties
 * Pool properties
 ```
 ┌─[san]─[/pool02/backups/plex]
└─⚬ zpool get all pool03
NAME    PROPERTY                       VALUE                          SOURCE
pool03  size                           21.8T                          -
pool03  capacity                       32%                            -
pool03  altroot                        -                              default
pool03  health                         ONLINE                         -
pool03  guid                           6740060430251469199            -
pool03  version                        -                              default
pool03  bootfs                         -                              default
pool03  delegation                     on                             default
pool03  autoreplace                    off                            default
pool03  cachefile                      -                              default
pool03  failmode                       wait                           default
pool03  listsnapshots                  off                            default
pool03  autoexpand                     off                            default
pool03  dedupditto                     0                              default
pool03  dedupratio                     1.00x                          -
pool03  free                           14.7T                          -
pool03  allocated                      7.06T                          -
pool03  readonly                       off                            -
pool03  ashift                         12                             local
pool03  comment                        -                              default
pool03  expandsize                     -                              -
pool03  freeing                        0                              -
pool03  fragmentation                  0%                             -
pool03  leaked                         0                              -
pool03  multihost                      off                            default
pool03  feature@async_destroy          enabled                        local
pool03  feature@empty_bpobj            active                         local
pool03  feature@lz4_compress           active                         local
pool03  feature@multi_vdev_crash_dump  enabled                        local
pool03  feature@spacemap_histogram     active                         local
pool03  feature@enabled_txg            active                         local
pool03  feature@hole_birth             active                         local
pool03  feature@extensible_dataset     active                         local
pool03  feature@embedded_data          active                         local
pool03  feature@bookmarks              enabled                        local
pool03  feature@filesystem_limits      enabled                        local
pool03  feature@large_blocks           enabled                        local
pool03  feature@large_dnode            enabled                        local
pool03  feature@sha512                 enabled                        local
pool03  feature@skein                  enabled                        local
pool03  feature@edonr                  enabled                        local
pool03  feature@userobj_accounting     active                         local
```
 
 * Volumes properties
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

# Disable atime on all pools
* Disabling atime on the pool will also disable it on all child volumes.
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

# Sync FS content
```
for folder in $(ls /pool01/); do rsync --delete -av /pool01/${folder}/ /pool03/${folder}/; done
```

# Replace old pool with new pool
## Stop all containers and disable dockerd at start
```sh
for service in smbd netatalk docker; do
	sudo systemctl stop ${service}
	sudo systemctl disable ${service}
done
```

```
┌─[san]─[~]
└─⚬ lsof|grep pool0

┌─[san]─[~]
```
## Rename pool01 to pool01_old
```sh
zpool export pool01
zpool import pool01 pool01_old  
```

## Replace HDDs
* Just swapping pool01_old HDDs inside my NAS by pool03 HDDs

## Reconnect HDDs to pool03
* IDs have changed, now we need to reconnect the HDDs
```
zpool import -d /dev/disk/by-id pool03
```

* test if pool is present
```
┌─[san]─[~]
└─⚬ zpool list
NAME     SIZE  ALLOC   FREE  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
pool03  21.8T  7.57T  14.2T         -     0%    34%  1.00x  ONLINE  -
```

## Rename pool03 to pool01
```
zfs export pool03
zfs import pool03 pool01
```

## Re enable services
```sh
for service in smbd netatalk docker; do
	sudo systemctl start ${service}
	sudo systemctl enable ${service}
done
```

## Set quota on Plex partition

```sh
sudo zfs set quota=15TB pool01/Plex
```

## cleanup
```
rmdir /pool01_old
```

# replace zfs disk

https://dlford.io/linux-zfs-raid-disk-replacement-procedure/
