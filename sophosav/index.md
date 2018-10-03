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
Copyright (c) 1989-2016 Sophos Limited. All rights reserved.

Sophos Anti-Virus インストーラへようこそ。Sophos Anti-Virus には、オンアクセススキャナ、オンデマンドコマンドラインスキャナ、Sophos Anti-Virus デーモン、および Sophos Anti-Virus GUI があります。

オンアクセススキャナ         ファイルがアクセスされると検索し、未感染の場合のみアクセスを許可
オンデマンドスキャナ         コンピュータの全体または一部を直ちに検索
Sophos Anti-Virus デーモン  Sophos Anti-Virus にコントロール、ログ、メール警告機能を提供するバックグラウンドプロセス
Sophos Anti-Virus GUI        Web ブラウザ経由でアクセスするユーザーインターフェース


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
Starting Sophos Anti-Virus daemon:                         [  OK  ]

インストールが完了しました。

```

オンアクセススキャンはいいや  

マニュアル  

* https://www.sophos.com/ja-jp/medialibrary/PDFs/documentation/savl_9_sgeng.pdf
* 設定マニュアル https://www.sophos.com/ja-jp/medialibrary/pdfs/documentation/savl_9_cgeng.pdf

オンデマンドスキャン

```
# savscan /
SAVScan ウイルス検出ユーティリティ
バージョン 5.27.0 [Linux/AMD64]
ウイルスデータバージョン  5.30, 2016年8月
11781705種類のウイルス、トロイの木馬、ワームを検出します。
Copyright (c) 1989-2016 Sophos Limited. All rights reserved.

システム日 2017年07月18日, システム時刻 22時17分42秒

Scan の使用有効期間が経過しています

クイックモード検索

/etc/alternatives/rake を開くことができませんでした

/usr/bin/rake を開くことができませんでした
/lib/modules/4.9.32-15.41.amzn1.x86_64/build を開くことができませんでした
/lib/modules/4.9.32-15.41.amzn1.x86_64/source を開くことができませんでした

ファイル 15647 個を 44秒で検索しました。
4 個のエラーが発生しました。
ウイルスは発見されませんでした。
検索が終了しました。
```

アップデートは定期的にデーモンで実行しているみたい。

```
# chkconfig --list
sav-protect     0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

messagesにログも出てる

```
Jul 19 13:17:50 pweb-01 savd: update.check: Successfully updated Sophos Anti-Virus from sdds:SOPHOS
```

検知した場合はファイル権限をなくすよう等オプションを付けて実行  

```
/usr/local/bin/savscan / -all --quarantine
```
