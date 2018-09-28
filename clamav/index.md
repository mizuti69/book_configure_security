# ClamAV
OSSのウイルススキャンツール  

## インストール  
コマンド実行するための最低限のパッケージ群のみインストール  

```
yum install --enablerepo=epel clamav

================================================================================================================
 Package                       アーキテクチャー   バージョン                       リポジトリー            容量
================================================================================================================
インストール中:
 clamav                        x86_64             0.100.1-3.el7                    epel                   642 k
依存性関連でのインストールをします:
 clamav-filesystem             noarch             0.100.1-3.el7                    epel                    26 k
 clamav-lib                    x86_64             0.100.1-3.el7                    epel                   758 k
 clamav-update                 x86_64             0.100.1-3.el7                    epel                   100 k
 libtool-ltdl                  x86_64             2.4.2-22.2.amzn2.0.2             amzn2-core              49 k

```

インストールするとOS上にユーザーが作成される  

```
clamupdate:x:995:992:Clamav database update user:/var/lib/clamav:/sbin/nologin
clamscan:x:994:990:Clamav scanner user:/:/sbin/nologin
```

## ウイルスデータベースの更新  

```
# freshclam
ClamAV update process started at Fri Sep 28 12:35:31 2018
Downloading main.cvd [100%]
main.cvd updated (version: 58, sigs: 4566249, f-level: 60, builder: sigmgr)
Downloading daily.cvd [100%]
daily.cvd updated (version: 24988, sigs: 2102260, f-level: 63, builder: neo)
Downloading bytecode.cvd [100%]
bytecode.cvd updated (version: 327, sigs: 91, f-level: 63, builder: neo)
Database updated (6668600 signatures) from database.clamav.net (IP: 104.16.185.138)
```

cronに自動更新のジョブは登録されている  

```
# cat /etc/cron.d/clamav-update
## Adjust this line...
MAILTO=root

## It is ok to execute it as root; freshclam drops privileges and becomes
## user 'clamupdate' as soon as possible
0  */3 * * * root /usr/share/clamav/freshclam-sleep
```

## スキャンの実施  
テストファイルを配置して  
http://www.eicar.org/download/eicar.com  
手動によるスキャン  

```
# clamscan /root
/root/.bash_logout: OK
/root/.gitconfig: OK
/root/.bash_profile: OK
/root/.bashrc: OK
/root/.cshrc: OK
/root/.tcshrc: OK
/root/.viminfo: OK
/root/eicar.com: Eicar-Test-Signature FOUND
/root/.bash_history: OK

----------- SCAN SUMMARY -----------
Known viruses: 6662136
Engine version: 0.100.1
Scanned directories: 1
Scanned files: 9
Infected files: 1
Data scanned: 0.05 MB
Data read: 0.02 MB (ratio 2.00:1)
Time: 13.813 sec (0 m 13 s)
```

システムログには検知等の文言は出ていない  
デフォルトでは検知してもレポートするのみで何もしない  
詳細なスキャン  

```
# clamscan -r --log=/var/log/clamav/scan.log --move=/var/log/clamav --max-ziptypercg=1024M --exclude-dir=^/backup --cross-fs=no /root
```

「--remove」オプションもあるが、削除してしまうと誤検知時やシステムファイルのときに困るため利用は注意する  
「--log」オプションで指定したログにはサマリのみ出力される  
その他詳細は「--help」で確認できる  

clamdをインストールしていると、スキャン用のサンプルコンフィグがある  
コマンドベースで利用する場合はほぼ不要だが、でフォルト設定について確認することはできる  

```
# /etc/clamd.d/scan.conf
```

定期的に実行する場合ばバッチを作成しcron等に登録する  

## デーモンでの利用
clamavを上記のように利用した場合、ウイルス定義DBを都度読み込みに行くためスキャン速度が遅くなる  
定期的なスキャンやメールのスキャン用にデーモン化してメモリ上に情報を持つことで高速にスキャンする事ができる  

```
# yum install --enablerepo=epel clamd
```

初期設定  
コンフィグは「Example」の記述があるとエラーとなる  

```
# vim /etc/clamd.d/scan.conf
#Example
LogFile /var/log/clamd.scan
LogSyslog yes
LocalSocket /var/run/clamd.scan/clamd.sock
FixStaleSocket yes
TCPSocket 3310
TCPAddr 127.0.0.1
CrossFilesystems no
User root
```

サービス起動  

```
# systemctl start clamd@service
# systemctl enable clamd@service
```

スキャンの実施  

```
# clamdscan --log=/var/log/clamav/scan.log --move=/var/log/clamav --config-file=/etc/clamd.d/scan.conf  /root
```

clamdscanはデフォルトで`/etc/clamd.conf`を見に行こうとするため明記する  
