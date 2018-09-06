# Audit
OS監査ログの取得  

## インストール
多くのOSにてデフォルトでインストールされている場合が多い  
インストールして起動すれば基本動く  

```
# yum install audit
# systemctl start auditd
```

## 基本設定

監査ログは`/var/log/auditd`配下に出力される  
ログファイルの保持期間やサイズはコンフィグで調整可能  

```
# vim /etc/auditd/auditd.conf

# １世代あたりのログファイルサイズ（MB）
max_log_file = 8

# ログファイルサイズ最大時の挙動
max_log_file_action = ROTATE

# ログファイルの保持世代数
num_logs = 5
```

## レポート出力

* 全てのレポートを表示

```
# aureport -x
# aureport -x --summary

Executable Summary Report
=================================
total  file
=================================
117876  /usr/sbin/crond
13662  /usr/lib/systemd/systemd
1092  /usr/sbin/sshd
136  /usr/bin/sudo
136  /usr/bin/su
16  /usr/sbin/useradd
8  /usr/sbin/groupadd
4  /usr/sbin/usermod
2  /usr/lib/systemd/systemd-update-utmp
2  /usr/bin/passwd
1  /usr/bin/kmod
```

* OSログインレポートの表示

```
# aureport --login -i

Login Report
============================================
# date time auid host term exe success event
============================================
1. 2018年09月04日 12:50:25 unset XX.XX.XX.XX /dev/pts/0 /usr/sbin/sshd yes 152612
```

* 認証ログ  

```
# aureport -au

Authentication Report
============================================
# date time acct host term exe success event
============================================
1. 2018年09月04日 12:50:25 user_name XX.XX.XX.XX ssh /usr/sbin/sshd yes 152601
2. 2018年09月04日 12:50:25 user_name XX.XX.XX.XX ssh /usr/sbin/sshd yes 152604
3. 2018年09月04日 12:50:30 user_name ? /dev/pts/1 /usr/bin/sudo yes 152615
4. 2018年09月04日 12:50:30 root study-mng pts/1 /usr/bin/su yes 152620
```

* 日時、期間指定

過去一週間のログイン情報を確認  

```
# aureport --login -i -ts week-ago
```

* 日時指定

```
# aureport --start 04/08/2013 00:00:00 --end 04/11/2013 00:00:00
```

* ログファイル指定

```
# /sbin/aureport -au -if /var/log/audit
# /sbin/aureport -au -if /var/log/audit/audit.log
```