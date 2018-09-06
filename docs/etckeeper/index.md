# etckeeper
Linuxの`/etc`ディレクトリをgit管理する事ができる  

## インストール

```
# yum install --enablerepo=epel etckeeper
```

## リポジトリの作成  
初期化  

```
# etckeeper init
```

`/etc`配下に`.git`が作成されてリポジトリ化される  

コミット  

```
# etckeeper commit -m "Initial commit"
```

日時でcronからジョブが呼び出され、コミットされる  

```
# cat /etc/cron.daily/etckeeper
#!/bin/sh
set -e
if [ -e /etc/etckeeper/daily ] && [ -e /etc/etckeeper/etckeeper.conf ]; then
        . /etc/etckeeper/etckeeper.conf
        if [ "$AVOID_DAILY_AUTOCOMMITS" != "1" ]; then
                /etc/etckeeper/daily
        fi
fi
```

## 基本操作  
コミットログの確認  

```
# etckeeper vcs log
```

基本的には上記の用に変更を日時でローカルのリポジトリにコミットするのみの機能となる  
差分状況をメール通知できるようdailyで叩かれるcronジョブに追記してみる  

```
# vim /etc/etckeeper/daily

#!/bin/sh
# Script that can be run daily to autocommit /etc changes.
set -e
if [ -x /usr/bin/etckeeper ] && [ -e /etc/etckeeper/etckeeper.conf ]; then
        # avoid autocommit if an install run is in progress
        lockfile=/var/cache/etckeeper/packagelist.pre-install
        if [ -e "$lockfile" ] && [ -n "$(find "$lockfile" -mtime +1)" ]; then
                rm -f "$lockfile" # stale
        fi
        if [ ! -e "$lockfile" ]; then
                AVOID_SPECIAL_FILE_WARNING=1
                export AVOID_SPECIAL_FILE_WARNING
                if etckeeper unclean; then
                        etckeeper commit "daily autocommit" >/dev/null
                        cd /etc && git show --stat |mail -s "etckeeper $(hostname) git log" root
                fi
        fi
fi
```
