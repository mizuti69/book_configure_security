# AFICK
ファイル改ざん検知ツール  

## インストール
パッケージはSourceForgeにて公開されている  
https://sourceforge.net/projects/afick/files/afick/  

最新版をインストール  

```
# yum install https://sourceforge.net/projects/afick/files/afick/3.6.0/afick-3.6.0-1.noarch.rpm
```

## 起動
データベースの初期化  

```
# afick -i
WARNING: skip bad config neg rule = /dev/scsi p+n (line 184) /dev/scsi directory does not exist
# Afick (3.6.0) init at 2018/09/06 17:09:21 with options (/etc/afick.conf):
# archive:=/var/lib/afick/archive
# database:=/var/lib/afick/afick
# exclude_suffix:=log LOG html htm HTM txt TXT xml hlp pod chm tmp old bak fon ttf TTF bmp BMP jpg JPG gif png ico wav WAV mp3 avi
# history:=/var/lib/afick/history
# max_checksum_size:=10000000
# running_files:=1
# timing:=1
# dbm:=Storable
# #################################################################
# MD5 hash of /var/lib/afick/afick => a+cXNY4U9ckjVkEzTSbF7Q

# Hash database created successfully. 48806 files entered.
# user time : 12.9; system time : 1.17; real time : 15
```

ちょっと定義ファイル上の対象ディレクトリが無いとWARNINGは出ている  

コマンド自体はperlで書かれているため、必要なパッケージが足りない場合は追加する  

```
# yum install perl5 perl-Digest-MD5
```

## 基本的操作
スキャンの実施  

何も変更がない場合  

```
# afick -k

WARNING: skip bad config neg rule = /dev/scsi p+n (line 184) /dev/scsi directory does not exist
# Afick (3.6.0) compare at 2018/09/06 17:13:02 with options (/etc/afick.conf):
# archive:=/var/lib/afick/archive
# database:=/var/lib/afick/afick
# exclude_suffix:=log LOG html htm HTM txt TXT xml hlp pod chm tmp old bak fon ttf TTF bmp BMP jpg JPG gif png ico wav WAV mp3 avi
# history:=/var/lib/afick/history
# max_checksum_size:=10000000
# running_files:=1
# timing:=1
# dbm:=Storable
# last run on 2018/09/06 17:09:21 with afick version 3.6.0
# summary changes

# detailed changes
# #################################################################
# MD5 hash of /var/lib/afick/afick => a+cXNY4U9ckjVkEzTSbF7Q

# Hash database : 48806 files scanned, 0 changed (new : 0; delete : 0; changed : 0; dangling : 12; exclude_suffix : 774; exclude_prefix : 0; exclude_re : 0; degraded : 22)
# user time : 18.53; system time : 1.08; real time : 19
```

変更が合った場合、  
例えばafick.confからWARNINGが出ていた設定を除外する変更を加えてみる  

```
# afick -k

# Afick (3.6.0) compare at 2018/09/06 17:14:44 with options (/etc/afick.conf):
# archive:=/var/lib/afick/archive
# database:=/var/lib/afick/afick
# exclude_suffix:=log LOG html htm HTM txt TXT xml hlp pod chm tmp old bak fon ttf TTF bmp BMP jpg JPG gif png ico wav WAV mp3 avi
# history:=/var/lib/afick/history
# max_checksum_size:=10000000
# running_files:=1
# timing:=1
# dbm:=Storable
# last run on 2018/09/06 17:09:21 with afick version 3.6.0
WARNING: (control) afick internal change : /etc/afick.conf (see below)
# summary changes
changed file : /etc/afick.conf

# detailed changes
changed file : /etc/afick.conf
        filesize                 : 7051 7053
        md5              : 56e9c4f257e67df1e59907118584e8d0     59008e6515127767810fa1fb3b4603c2
# #################################################################
# MD5 hash of /var/lib/afick/afick => a+cXNY4U9ckjVkEzTSbF7Q

# Hash database : 48806 files scanned, 1 changed (new : 0; delete : 0; changed : 1; dangling : 12; exclude_suffix : 774; exclude_prefix : 0; exclude_re : 0; degraded : 22)
# user time : 18.49; system time : 1.12; real time : 20
```

何処のファイルが変更されたか、変更ファイルサイズ、ハッシュ値がレポートされる  
ファイルを更新した場合、DBをアップデートする  

```
# afick -u

 Afick (3.6.0) update at 2018/09/06 20:05:24 with options (/etc/afick.conf):
# archive:=/var/lib/afick/archive
# database:=/var/lib/afick/afick
# exclude_suffix:=log LOG html htm HTM txt TXT xml hlp pod chm tmp old bak fon ttf TTF bmp BMP jpg JPG gif png ico wav WAV mp3 avi
# history:=/var/lib/afick/history
# max_checksum_size:=10000000
# running_files:=1
# timing:=1
# dbm:=Storable
# last run on 2018/09/06 17:09:21 with afick version 3.6.0
WARNING: (control) afick internal change : /etc/afick.conf (see below)
# summary changes
changed file : /etc/afick.conf

# detailed changes
changed file : /etc/afick.conf
        filesize                 : 7051 7053
        md5              : 56e9c4f257e67df1e59907118584e8d0     59008e6515127767810fa1fb3b4603c2
# #################################################################
# MD5 hash of /var/lib/afick/afick => vEnOm8YCAgsspcWPRIjhVg

# Hash database updated successfully : 48806 files scanned, 1 changed (new : 0; delete : 0; changed : 1; dangling : 12; exclude_suffix : 775; exclude_prefix : 0; exclude_re : 0; degraded : 22)
# user time : 19.25; system time : 1.2; real time : 21
```

最後に`Hash database updated successfully : 48806 files scanned, 1 changed`となっている  
次にもう一度スキャンすると検知されない  

## 設定

デフォルトで管理から除外されている拡張子  

```
# vim /etc/afick.conf

# only_suffix : list of suffix to scan (and just this ones) : is empty (disabled) by default
# not very usefull on unix, but is ok on windows
# this will speed up the scan, but with a lesser security
# only_suffix :=

# the 3 next directives : exclude_suffix exclude_prefix exclude_re
# can be written on several lines
# exclude_suffix : list of suffixes to ignore
# text files
exclude_suffix := log LOG html htm HTM txt TXT xml
# help files
exclude_suffix := hlp pod chm
# old files
exclude_suffix := tmp old bak
# fonts
exclude_suffix := fon ttf TTF
# images
exclude_suffix := bmp BMP jpg JPG gif png ico
# audio
exclude_suffix := wav WAV mp3 avi
```

WEBサイトのコンテンツを監視したい場合は除外りすとをコメントアウトする必要がある  

スキャン対象  

```
= /  DIR

/bin    MyRule

/boot   MyRule
! /boot/map
! /boot/System.map

/dev p+n
! /dev/.udev/db
! /dev/.udev/failed
! /dev/.udev/names
! /dev/.udev/watch
! /dev/bsg
! /dev/bus
! /dev/pts
! /dev/shm
# to avoid problems with pending usb
! = /dev/scsi p+n

/etc    ETC
/etc/mtab ETC - md5 - s
/etc/adjtime ETC - md5
/etc/aliases.db ETC - md5
/etc/mail/statistics ETC - md5
/etc/motd ETC
/etc/ntp/drift ETC - md5
/etc/urpmi/urpmi.cfg Logs
/etc/urpmi/proxy.cfg Logs
/etc/prelink.cache ETC - md5 - s
! /etc/cups
! /etc/map
! /etc/postfix/prng_exch
! /etc/samba/secrets.tdb
! /etc/webmin/sysstats/modules/
! /etc/webmin/package-updates/
! /etc/webmin/system-status/

/lib    MyRule
/lib64  MyRule
/lib/modules MyRule
/lib/dev-state MyRule -u

/root MyRule
! /root/.viminfo
! /root/.bash_history
! /root/.mc
! /root/tmp

/sbin   MyRule

/usr/bin        MyRule
/usr/sbin       MyRule
/usr/lib        MyRule
/usr/lib64      MyRule
/usr/local/bin  MyRule
/usr/local/sbin MyRule
/usr/local/lib  MyRule

/var/ftp MyRule
/var/log Logs
! /var/log/journal
= /var/log/afick Logs
! /var/log/ksymoops
/var/www MyRule
! /var/www/html/snortsnarf
```

スキャンアクション  

```
# action : a list of item to check :
# md5 : md5 checksum
# sha1 : sha-1 checksum
# sha256 : sha-256 checksum
# sha512 : sha-512 checksum
# d : device
# i : inode
# p : permissions
# n : number of links
# u : user
# g : group
# s : size
# b : number of blocks
# m : mtime
# c : ctime
# a : atime

#all:    p+d+i+n+u+g+s+b+m+c+md5
#R:      p+d+i+n+u+g+s+m+c+md5
#L:      p+d+i+n+u+g
#P:      p+n+u+g+s+md5
#E:      ''

# action alias may be configured with
# your_alias = another_alias|item[+item][-item]
# all is a pre-defined alias for all items except "a"
DIR = p+i+n+u+g
ETC = p+d+u+g+s+md5
Logs = p+n+u+g
MyRule = p+d+n+u+g+s+b+md5
```

デフォルトでは最小限の対象のみ定義されているため、個別で定義は追加する必要がある  

## 定期的な改ざん検知
デフォルトでcronにジョブが作成される  

```
# /etc/cron.daily/afick_cron
```

実行内容はメールにてレポートされる  
通知先はコンフィグに定義  

```
# vim /etc/afick.conf
@@define MAILTO root@localhost
```
