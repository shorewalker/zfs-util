# zfs-util
a small bash script with supplemental actions for ZFS on Linux

**For backups you'd probably be better served by using https://github.com/jimsalterjrs/sanoid/.**

Usage
--
    zfs-util upgrade
    zfs-util snapshot <filesystem>
    zfs-util rollback <filesystem>
    zfs-util get-latest <filesystem>
    zfs-util get-latest-remote <filesystem> <remotesystem> [--listkey <listkey>]
    zfs-util get-earliest <filesystem>
    zfs-util get-children <filesystem>
    zfs-util backup <filesystem> <remotesystem> [--listkey <listkey>] [--sendkey <sendkey>] [--opts <opts>] [--init]
    zfs-util backup-children <filesystem> <remotesystem> [--listkey <listkey>] [--sendkey <sendkey>] [--opts <opts>] [--init]
    zfs-util remove-older-than [-r] <filesystem> <date>
    zfs-util snapshot-status <zvol> <unixtimesnap>
    
Example Destination authorized_keys file  
--
    [root@destination.example.com ~]# grep root@source.example.com ~/.ssh/authorized_keys
    command="zfs list -H -t snapshot -r -d 1 -o name ${SSH_ORIGINAL_COMMAND#* } | tail -n 1" ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDhrpZbXaq+r7yqWnv/L0SEk3USe1IPu7EzUYxjkzi3yBNmT3xOJnONZEKBBAp6m5Fj5HUPDu5Op56S7NyCK3nk513hFwuy4tTJsICRLAqGAGJQ7pDr9/TTMWdgI+AF2GRXZ9b/J1bO6oaWSYG6E1SEOVof7WjlGOYIrsPRaIFheY/jp4pufYwNz/THeg/kBpDCcEvhhGiWAg/hm/436ftnFmrtB+t62Zg1MpjYCdIhtMZCJKZq1QgySMDMoyJDg5XUfwmIeFUTjMcoaHe+6Gr4ia6WOtLfs1KrfbfUQH1A7CLHKyTGd8ROfDcmescZAF4b+L6aw5q72l03J4A9Ql4X root@source.example.com
    command="zfs receive -du destination-zpool1" ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQChDLw0pB69oHNT1sqimUFqIYJoEnCsK1CWFW6aFvpEw91VCKdqvOD96hJpf6dqJkCrUmy6LZCs/HuoLSuvbM3LYMEY0D2hta6aNuWE9WyFkMWQp92oj/6bYjjzJL5wgMvl4uFsvGEQSiFIG3JQc8VsV2vN7uxALrlVOk6ewc8GRaBgbCB1T1YjTdiHLXuYUQAj9L/EeXQT5m4hzxJASQ9hjgybOcgAzh7vNybc2iel0C+l3meGh23FOj+/iqnJhcJN7hiq0jVPIarGXbdd4nUCg4+y4Hlqn40Z2B9RZFDMCKt2STUcFXLYjScQ57zvYnBv+WOUEwLzkOFnTLwa/3HR root@source.example.com
    
Example Source Crontab
--
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
    MAILTO=root@example.com
    
    0       0       *       *       *       zfs snapshot -r source-zpool1@$(date +\%F.\%T) && zfs-util backup-children source-zpool1 desination-zpool1/source-zpool1 desination --listkey .ssh/zfs-list-zpool1 --sendkey .ssh/zfs-send-zpool1 && zfs-util remove-older-than -r source-zpool1 "1 month ago"
    
Example Destination Crontab
--
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
    MAILTO=root@example.com
 
    0       10      *       *       *       zfs-util snapshot-status destination-zpool1 $((24*60*60)) | grep -v -f /usr/local/etc/zfs-snap-exclude
