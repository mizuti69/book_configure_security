# OpenVAS
ログインするとダッシュボードが表示される  
<img src="images/openvas_002.png" width="80%" />  

## スキャンの実施
OpnVASサーバ --> 対象サーバに対してスキャンを実施する  

### Immediate Scan  
メニューバー「Scans」から「Tasks」を選択  
<img src="images/openvas_003.png" width="80%" />  

左上のマークから「Task Wizard」で追加する  
<img src="images/openvas_004.png" width="80%" />  

<img src="images/openvas_005.png" width="80%" />  

スキャンが完了するとタスクステータス、レポート状況が追加される  
<img src="images/openvas_006.png" width="80%" />  

### Advanced Scan
メニューバー「Configration」から「Credentials」を選択  
<img src="images/openvas_007.png" width="80%" />  

SSHの接続情報を設定する  
<img src="images/openvas_008.png" width="80%" />  

「Scans」->「Tasks」の左上のマークから「Task Wizard」->「Advanced Task Wizard」  
<img src="images/openvas_004.png" width="80%" />  

先程作成したSSH接続情報を選択しタスクを作成しスキャン実施  
<img src="images/openvas_009.png" width="80%" />  

Advanced Scanは30～40分かかる場合もある  

## テスト内容
「Configuration」->「Scan Configs」から確認できる  
例えばQuickScanで利用される「Full and fast」では下記のようになっている  
<img src="images/openvas_010.png" width="80%" />  

* Network Vulnerability Test Families
* Scanner Preferences
* Network Vulnerability Test Preferences

スキャナーの設定を除くと内容はネットワークスキャンに関するポリシーが定義されている  
SSH設定はあるが、OS内部の状況についてはスキャンしない  

## スキャンレポートの確認  
「Scans」->「Reports」から確認できる  
<img src="images/openvas_011.png" width="80%" />  

適当に立てたサーバに対してスキャンを実行した場合（FWあり）SSHに対する脆弱性が検知された   
SSHで利用されている暗号化強度が弱いものらしい  
