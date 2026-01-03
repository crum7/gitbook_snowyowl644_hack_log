
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
# Parrot OS側の設定
GUIはMateね
## 追加パッケージインストール
```sh
sudo apt update
sudo apt install xserver-xorg-video-qxl spice-vdagent
sudo systemctl restart spice-vdagent
sudo systemctl enable spice-vdagent
sudo reboot
```


## windowsキー押した時に出てくるショートカットを消す
- mac使ってると、parrot OS使ってるときに英語にしようとして、command(windowsキー）を押して結構ウザいのでショートカットを消す
```sh
gsettings set com.solus-project.brisk-menu hot-key ''
```

## ctrlとcommandを入れ替える
- mac、commandしか使わないのに、parrot osではcommandばっか使うんのなんて嫌すぎ！
```sh
echo 'setxkbmap -option ctrl:swap_lwin_lctl' >> ~/.xprofile
```

## 解像度設定
5120❌1440の3分割のモニターで、綺麗に1/3にするための設定
```sh
cvt 1780 1440
xrandr --newmode "1784x1440_alt" 217.00 1784 1912 2104 2424 1440 1443 1453 1493 -hsync +vsync
xrandr --addmode Virtual-1 "1784x1440_alt"
xrandr --output Virtual-1 --mode "1784x1440_alt"
```


## キーボードショートカット
![](https://i.imgur.com/7XV4zeI.png)

## Bloodhound
neo4j : mimic


## ログインしたらやるコマンド
```sh
setxkbmap -option ctrl:swap_lwin_lctl
cvt 1780 1440
xrandr --newmode "1784x1440_alt" 217.00 1784 1912 2104 2424 1440 1443 1453 1493 -hsync +vsync
xrandr --addmode Virtual-1 "1784x1440_alt"
xrandr --output Virtual-1 --mode "1784x1440_alt"
```