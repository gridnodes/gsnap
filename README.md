# GlusterFS snapshot script

[![](https://img.shields.io/badge/license-GPLv3+-blue.svg?style=flat-square)](LICENSE)


## About

Starting with Debian buster, the cron scheduler of GlusterFS is not available
anymore. However, this tiny little script can be used as a replacement to create
snapshots via cron.

*__NOTE:__ This script can only be used on a single host, so snapshots will not
be continued, if the master node is down.*


## Usage

```
Usage: gsnap volname snapname keep

  volname   name of the volume
  snapname  name of the snapshot (e.g. hourly)
  keep      number of snapshots to keep
```

The script can be used in a simple crontab:

```
@hourly   root  /usr/sbin/gsnap vol0 hourly  24 > /dev/null
```

Special care needs to be taken, if there's more than one concurrent job at the
same time (e.g. creating snapshots of multiple volumes or creating the `hourly`
and `daily` snapshot at midnight), as only one process may manipulate snapshots
at the same time.

However, the tool `sem`, which is part of GNU `parallel`, can be used in this
case, by just prefixing all snapshot jobs by `sem --jobs 1 --id gsnap`:

```
@hourly   root  sem --jobs 1 --id gsnap /usr/sbin/gsnap vol0 hourly 24 > /dev/null
@daily    root  sem --jobs 1 --id gsnap /usr/sbin/gsnap vol0 daily  7  > /dev/null
```


## License

This project is licensed under the [GPLv3+](LICENSE).

&copy; 2019 mksec, Alexander Haase
