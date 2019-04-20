# zfs-setup

## Information
Just my step by step method to create my ZFS pools. It will not make any sense except for me.

New HDDs are in a USB enclosure to sync with the old HHDs.
I will remplace the old HDDS with the new one and rename the pool of the next HDDs to match the old pool

zfs info: https://github.com/zfsonlinux/zfs/wiki/FAQ#selecting-dev-names-when-creating-a-pool

## steps / reminder
**Do not forget to use tmux or screen**
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

New HDDs have no partition
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

get attributes
```
┌─[san]─[~]
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
pool01  free                           3.30T                          -
pool01  allocated                      7.57T                          -
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

## Create new ZRAID temporary pool
 I will not use the /dev/sd* but the IDs

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
	  raidz1-0                                    ONLINE       0     0     0
	    usb-ST8000DM_004-2CX188_152D00539000-0:0  ONLINE       0     0     0
	    usb-ST8000DM_004-2CX188_152D00539000-0:1  ONLINE       0     0     0
	    usb-ST8000DM_004-2CX188_152D00539000-0:2  ONLINE       0     0     0

errors: No known data errors
```
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
 ```
 ┌─[san]─[/dev/disk/by-id]
└─⚬ zpool get all pool03
NAME    PROPERTY                       VALUE                          SOURCE
pool03  size                           21.8T                          -
pool03  capacity                       0%                             -
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
pool03  free                           21.7T                          -
pool03  allocated                      888K                           -
pool03  readonly                       off                            -
pool03  ashift                         12                             local
pool03  comment                        -                              default
pool03  expandsize                     -                              -
pool03  freeing                        0                              -
pool03  fragmentation                  0%                             -
pool03  leaked                         0                              -
pool03  multihost                      off                            default
pool03  feature@async_destroy          enabled                        local
pool03  feature@empty_bpobj            enabled                        local
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
```
┌─[san]─[/dev/disk/by-id]
└─⚬ zfs list |grep pool01
pool01                   5.04T  1.97T  15.2G  /pool01
pool01/Plex              5.00T  1.00T  5.00T  /pool01/Plex
pool01/backup_ubuntu     1.18G  1.97T  1.18G  /pool01/backup_ubuntu
pool01/docker            10.8G  1.97T  10.8G  /pool01/docker
pool01/git               2.68M  1.97T  2.68M  /pool01/git
pool01/jupyter-notebook   218K  1.97T   218K  /pool01/jupyter-notebook
pool01/minidlna          21.9G  1.97T  21.9G  /pool01/minidlna
pool01/salt              4.56M  1.97T  4.56M  /pool01/salt
```

Re create the same FS 
```sh
for file in $(zfs list |grep pool01/|cut -d'/' -f2|awk '{print $1}'); do zfs create  pool03/$file; done
```

change ownership and permissions
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

sync FS content

```
 for folder in Plex backup_ubuntu docker drivers git jupyter-notebook minidlna owncloud_backup salt; do rsync -av /pool01/${folder}/ /pool03/${folder}/; done
 ```