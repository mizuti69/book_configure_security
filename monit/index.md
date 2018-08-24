# Monit
サーバエージェント型で動く死活監視サービス  
主な機能として以下のような事ができる  

* プロセス監視  
* テキスト監視  
* ファイル監視  
* リソース監視  
* メール通知  
* アラートと連動したコマンド実行  

[マニュアル]  
https://mmonit.com/monit/documentation/monit.html  

## インストール  

EPELリポジトリからインストールする  

```
# yum install --enablerepo=epel monit
```

基本設定  

```
# vim /etc/monitrc

## 監視間隔(s)
set daemon  60 

## SMTPサーバの指定
set mailserver localhost

## メールフォーマット
set mail-format {
  from: monit@localhost.localdomain
  subject: アラート検知 $EVENT $SERVICE
  message: 関係者各位
 
    監視: $SERVICE $EVENT
    ステータス: $ACTION
    監視サーバ: $HOST
    監視時刻: $DATE
 
    内容:
    $DESCRIPTION
}

## システムアラート通知先
## ここに定義したメールアドレスには全てのアラートとシステム通知が飛ぶ
## 個別に定義したい場合ば監視設定毎に定義する必要がある
set alert shm_ope_ml@ml01.trans-cosmos.co.jp but not on { action }
```

## 監視設定
コンフィグ`monitrc`に定義された`include /etc/monit.d/*`に定義されたディレクトリに監視項目単位で定義する  

**テキスト監視**  

```
# vim /etc/monit.d/check_messages
check file syslog with path /var/log/messages
 ignore match "/opt/conf/check_messages-exclude"
 if match "/opt/conf/check_messages-include"  then alert
```

検知文言一覧と除外文言一覧を外出ファイルで管理する  

```
# vim check_messages-include
ERROR
warn
Warn
WARN
fatal
Fatal
FATAL
infect
Infect
INFECT
alert
Alert
ALERT
panic
Panic
PANIC
oom-killer

# vim check_messages-exclude
test
```

**プロセス監視**  

プロセス監視では指定されたPIDファイルを監視し、無い場合起動あるいは停止を行う  
復旧しない場合の無限ループにならないよう、リトライ回数を3回と設定している  

```
# vim check_crond
check process crond
  with pidfile "/var/run/crond.pid"
  start program = "/bin/systemctl start crond"
  stop program = "/bin/systemctl stop crond"
  if 3 restarts within 3 cycles then alert
```

**リソース監視**  

監視項目に対して閾値を設定し超えた場合アラート  
発生し続ける限りアラート通知は発生し続けます  

```
check system $HOST
    if loadavg (1min) > 4 then alert
    if loadavg (5min) > 2 then alert
    if cpu usage > 95% for 10 cycles then alert
    if memory usage > 75% then alert
    if swap usage > 25% then alert
```

**ファイル存在監視**  

指定されたファイルかディレクトリにアクセス出来なくなった場合アラート  

```
check directory data02 
  path /data02/stgimg.shamaison.com/public/sys/housing_imgs/.directory_monitor
  if does not exist then alert
```

**通知の個別設定**  
監視対象毎に通知先を分けたい場合監視定義ファイルの末にメール定義を追加する  

```
alert toto_system@ml01.trans-cosmos.co.jp
```
