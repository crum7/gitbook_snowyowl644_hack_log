
Kali UTM ファイル
- https://mac.getutm.app/gallery/kali-2023
# UTM側の設定
## ネットワーク
UTMの共有ネットワークは、NATなので、普通の場合はNATのままで良い
触らない
## ディスプレイ
- ハードウェア
	- virtio-ramfb
- ディスプレイサイズをウィンドウサイズに自動的に合わせる
	- ポンコツなのでオフ
	- 必ずチェックを外す
		- ディスプレイのサイズの変更はParrot OS側でいじったほうが良い
- 拡大縮小
	- なんのことかよく分かってないけど両方ともリニア
- Retinaモード
	- 上記と同様だが、オン
# Kali OS側の設定
GUIはXFCEね
## windowsキー押した時に出てくるショートカットを消す
- mac使ってると、kali OS使ってるときに英語にしようとして、command(windowsキー）を押して結構ウザいのでショートカットを消す
```sh
xfconf-query -c xfce4-keyboard-shortcuts -r -p "/commands/custom/<Primary>Escape"
```

## ctrlとcommandを入れ替える
- mac、commandしか使わないのに、parrot osではcommandばっか使うんのなんて嫌すぎ！
```sh
setxkbmap -option ctrl:swap_lwin_lctl
```

## 解像度設定
5120❌1440の3分割のモニターで、綺麗に1/3にするための設定
```sh
cvt 1780 1440
xrandr --newmode "1784x1440_alt" 217.00 1784 1912 2104 2424 1440 1443 1453 1493 -hsync +vsync
xrandr --addmode Virtual-1 "1784x1440_alt"
xrandr --output Virtual-1 --mode "1784x1440_alt"
```

CA Tech Loungのモニターで、(3360❌1890)で2分割にするための設定
```sh
cvt 1680 1890
xrandr --newmode "1680x1890_60.00"  270.50  1680 1808 1992 2304  1890 1893 1903 1958 -hsync +vsync
xrandr --addmode Virtual-1 "1680x1890_60.00"
xrandr --output Virtual-1 --mode "1680x1890_60.00"
```

macで2分割
```sh
cvt 900 1169
xrandr --newmode "904x1169_60.00"   87.75  904 960 1056 1208  1169 1172 1182 1212 -hsync +vsync
xrandr --addmode Virtual-1 "904x1169_60.00"
xrandr --output Virtual-1 --mode "904x1169_60.00"
```

## 動画撮る時
```sh
# 1. フォント・UI全体のDPIを192に（フルHD相当の見た目）
xfconf-query -c xsettings -p /Xft/DPI --create -t int -s 192

# 2. パネル高さを48pxに（標準は24px）
xfconf-query -c xfce4-panel -p /panels/panel-1/size -s 48

# 3. パネル内アイコンサイズを32pxに
xfconf-query -c xfce4-panel -p /panels/panel-1/icon-size -s 32

# 4. パネルを再起動して反映
xfce4-panel -r
```

## パッケージアップグレード
```sh
sudo apt upgrade
sudo apt update 
```
## Bloodhound
admin : B7r!kLp29#qW
admin : mimic


ヒントの有効化
```sh
unset ZSH_AUTOSUGGEST_DISABLE
source ~/.zshrc
```


ヒントの無効化
```sh
ZSH_AUTOSUGGEST_DISABLE=1
source ~/.zshrc
```
# 環境構築
新しいツールを入れたら、次のためにパスを通す
ダウンロード・インストールフォルダ : `/home/kali/tools`
エイリアスを書くフォルダ : `/home/kali/.local/bin`

pythonの場合 
- 仮想環境を作成
```sh
python3 -m venv venv
source venv/bin/activate
```

エイリアス作成
```sh
cd /home/kali/.local/bin
nano <ToolName>
```

書き込み
```sh
#!/bin/bash
source ~/tools/<ToolName>/venv/bin/activate
python3 ~/tools/<ToolName>/<Excute File> "$@"
```

```sh
chmod +x ~/.local/bin/<ToolName>
```

# ショートカットの設定
autohotkeyの設定
![](https://i.imgur.com/p5gT7We.png)


## Hexstrike MCP Server自動起動設定 (systemd)
Hexstrike Server を Kali Linux 起動時に自動で立ち上げたい場合は、`systemd` サービスを設定してください。
### 1. サービスファイルを作成
sudo nano /etc/systemd/system/hexstrike.service
### 2. systemd に登録
作成したサービスを有効化して起動
```sh
sudo systemctl daemon-reload
sudo systemctl enable hexstrike.service
sudo systemctl start hexstrike.service
```

## 3.状態確認
サービスが正常に動作しているか確認
```sh
systemctl status hexstrike.service
```

ログを確認する場合
```sh
journalctl -u hexstrike.service -e --no-pager
```

リアルタイムでログを追いかける
```sh
journalctl -u hexstrike.service -f -o cat
```

過去ログも含めて確認しつつ、末尾を追いかける
```sh
journalctl -u hexstrike.service -e -f
```