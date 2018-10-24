# Tlog
ターミナルログの取得ツール  
https://github.com/Scribery/tlog  

## インストール  
GithubにてRPMが提供されているため、ダウンロードしてインストールする  
https://github.com/Scribery/tlog/releases  

```
# wget https://github.com/Scribery/tlog/releases/download/v4/tlog-4-1.el7.centos.x86_64.rpm
# yum localinstall tlog-4-1.el7.centos.x86_64.rpm
```

## 使い方

記録の開始  

```
# /usr/bin/tlog-rec --writer=file --file-path="$LOGGINGFILE"
```

記録の再生  

```
# /usr/bin/tlog-play --reader=file --file-path="$LOGGINGFILE"
```

ログインした際に自動記録させる場合  

* ログインシェルを変更する  

```
# usermod -s /usr/bin/tlog-rec-session <user>
```

* profileに追記してログイン時に実行させる  

ユーザー毎にログをわけて取りたいときに設定する。

```
# Logging operation
GRPS=(`/usr/bin/id -Gn`)
CHK_LOGIN="not a tty"
CHK_TTY=`LANG=C /usr/bin/tty|sed -e "s/$CHK_LOGIN/1/g"`
echo $CHK_TTY
if [ $CHK_TTY != 1 ]; then
  for grpnum in ${GRPS[@]};do
    case $grpnum in
      wheel)
        NUM=1
        ENTER_LIMIT=3
        echo ""
        echo -n "Please enter your name in *ALPHABET*. : "
        read REALNAME
        echo ""
        while [ ! -n "$REALNAME" ]
        do
          if [ $NUM -eq $ENTER_LIMIT ]; then
            echo "$REALNAME"
            break
          fi
          echo -n "Enter your name again : "
          read REALNAME
          NUM=`expr $NUM + 1`
        done
        if [ ! -n "$REALNAME" ]; then
          exit
        fi
        LOGGINGFILE="/var/log/terminallog/tlog_$(date +%Y%m%d_%H%M%S)_${REALNAME}.log"
        /usr/bin/tlog-rec --writer=file --file-path="$LOGGINGFILE"
        exit
      ;;
    esac
  done
fi
```

出力先ディレクトリには各ユーザーが出力できるよう書き込み権限のみ許可する

```
# mkdir /var/log/terminallog
# chmod +t /var/log/terminallog
# chmod o+w /var/log/terminallog
# chmod o-r /var/log/terminallog
```
