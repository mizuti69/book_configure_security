# SophosAV for Linux
無償のウイルススキャンツール  
OSSでは無く無償なので商用利用はやめましょう  

## インストール
ダウンロード  
利用者情報、企業情報の入力を求められます  
https://www.sophos.com/ja-jp/products/free-tools/sophos-antivirus-for-linux.aspx  

```
# ./install.sh

Sophos Anti-Virus
=================
Copyright 1989-2018 Sophos Limited. All rights reserved.

Sophos Anti-Virus インストーラへようこそ。Sophos Anti-Virus には、オンアクセススキャナ、オンデマンドコマンドラインスキャナ、Sophos Anti-Virus デーモンがあります。

オンアクセススキャナ         ファイルがアクセスされると検索し、未感染の場合のみアクセスを許可
オンデマンドスキャナ         コンピュータの全体または一部を直ちに検索
Sophos Anti-Virus デーモン   Sophos Anti-Virus にコントロール、ログ、メール警告機能を提供するバックグラウンドプ ロセス


「Enter」キーを押して、使用許諾契約書を表示してください。そして、<spc> を押してスクロールダウンしてください。


ライセンス内容に同意しますか？ はい(Y)/いいえ(N) [N]
> Y

Sophos Anti-Virus のインストール先を指定してください。 [/opt/sophos-av]
>

オンアクセス検索を有効にしますか？ はい(Y)/いいえ(N) [Y]
> N

オンアクセス検索が無効になっています。オンデマンド検索には、savscan を使ってください。
ソフォスは、Sophos Anti-Virus での自動アップデートの設定をお勧めします。

ソフォスから直接アップデートしたり（要アカウント情報）、自社サーバー（ディレクトリや Web サイト（アカウント情報 が必要な場合もあります））からアップデートすることができます。

オートアップデートの種類を選択してください: ソフォス(s)/自社サーバー(o)/なし(n) [s]
>

ソフォスから直接アップデートしています。
SAV for Linux の無償バージョン (f) と サポート対応付きバージョン (s) のどちらをインストールしますか？ [s]
> f

Sophos Anti-Virus for Linux の無償バージョンに対して、サポート対応は提供されていません。
無償ツールのフォーラムは次のサイトを参照してください。http://openforum.sophos.com/
ソフォスからアップデートを行うためにプロキシが必要ですか？ はい(Y)/いいえ(N) [N]
> N

無償のアップデート用アカウント情報を取得しています。
Sophos Anti-Virus をインストールしています....

インストールが完了しました。

```

マニュアル  

* https://www.sophos.com/ja-jp/medialibrary/PDFs/documentation/savl_9_sgeng.pdf
* 設定マニュアル https://www.sophos.com/ja-jp/medialibrary/pdfs/documentation/savl_9_cgeng.pdf

オンアクセススキャンはここでは有効にしない  
インストールするとユーザーが作成されている  

```
sophosav:x:993:10001:Sophos Anti-Virus:/opt/sophos-av:/bin/bash
```

## ウイルスデータベースの更新  
アップデート等はサービスが起動して行っている  

```
# systemctl status sav-protect
● sav-protect.service - "Sophos Anti-Virus daemon"
   Loaded: loaded (/usr/lib/systemd/system/sav-protect.service; enabled; vendor preset: disabled)
   Active: active (running) since 水 2018-10-03 19:23:44 JST; 2min 52s ago
     Docs: man:sav-protect
 Main PID: 29943 (savd)
   CGroup: /system.slice/sav-protect.service
           tq29943 savd etc/savd.cfg
           mq29968 savscand --incident=unix://tmp/incident --namedscan=unix://root@tmp/namedscansprocessor.0 ...

10月 03 19:23:45 study-mng savd[29943]: savd.daemon: Sophos Anti-Virus daemon started.
```

手動で更新する場合は下記コマンドで実行  

```
# /opt/sophos-av/bin/savupdate
sdds:SOPHOS からの Sophos Anti-Virus のアップデートに成功しました
```

## 設定

**メールで使用される言語を指定**  
デフォルトはEnglish  

```
# /opt/sophos-av/bin/savconfig set EmailLanguage Japanese
```

**メール通知先を設定**  
デフォルトは`root@localhost.localdomain`

```
# /opt/sophos-av/bin/savconfig set Email test@maildomain.com   (上書き)
# /opt/sophos-av/bin/savconfig add Email test@maildomain.com   (追加)
# /opt/sophos-av/bin/savconfig remove Email test@maildomain.com   (削除)
```

**通知Fromアドレス**  
デフォルトは`root@localhost.localdomain`

```
# /opt/sophos-av/bin/savconfig set EmailSender admin@localhost
```

## スキャン
テストファイルを配置して  
http://www.eicar.org/download/eicar.com  
オンデマンドスキャンを実施する  

```
# savscan /root -nc -all --quarantine -rename --stop-scan
SAVScan ウイルス検出ユーティリティ
バージョン 5.47.0 [Linux/AMD64]
ウイルスデータバージョン  5.55, 2018年9月
25680574種類のウイルス、トロイの木馬、ワームを検出します。
Copyright (c) 1989-2018 Sophos Limited. All rights reserved.

システム日 2018年10月03日, システム時刻 19時46分21秒
以下のオプションスイッチがあります： -all --quarantine

IDE ディレクトリ： /opt/sophos-av/lib/sav



クイックモード検索

>>> ウイルス‘EICAR-AV-Test’がファイル /root/eicar.com に発見されました
/root/eicar.com の隔離に成功しました
/root/eicar.com の名前を /root/eicar.com.infected に変更しました

ファイル 124 個を 6秒で検索しました。
1 個のウイルスが発見されました。
1 個のファイル（124 個中）が感染しています。
検出に関する詳細は、ソフォス Web サイトの次のリンクを参照してください。
脅威解析センター: http://www.sophos.com/ja-jp/threat-center.aspx
検索が終了しました。
```

* 「-all」全てのファイルを検索する  
* 「--quarantine」ウイルス検知時に権限を剥奪する(0400)  
* 「--stop-scan」検索に過度な時刻やリソースを消費する検索は中断する  
* 「-rename」検知時infectedという拡張子を付加する  
* 「-nc」検知時に確認メッセージを表示しない  

syslogに以下のようなメッセージが出力する  

```
Oct  3 10:55:42 study-mng savd: savscan.log: On-demand scan started.
Oct  3 10:55:48 study-mng savd: savscan.log: On-demand scan details: master boot records scanned: 0, boot records scanned: 0, files scanned: 124, scan errors: 0, threats detected: 1, infected files detected: 1
Oct  3 10:55:48 study-mng savd: Threat detected in /root/eicar.com: EICAR-AV-Test during on-demand scan. (The file has been quarantined.)
Oct  3 10:55:48 study-mng savd: savscan.log: On-demand scan finished.
```

メールは下記のようなものが送信される  

```
From: root@localhost.localdomain
To: root@localhost.localdomain
Subject: [SAV-LINUX] オンデマンド検索中、脅威が検出されました: localhost
Status: RO

オンデマンド検索中、脅威が検出されました。詳細情報：
124個のファイルを検索しました。
検出した脅威数： 1
検出された感染ファイル数： 1
/root/eicar.com.infected は EICAR-AV-Test に感染していますが、隔離されました。
```
