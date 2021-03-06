# xfce

# 특징

 리눅스의 데스크탑 매니저(DE) 중 하나입니다. KDE, Gnome에 비해 유저 수가 적습니다. (그래서 문서가 거의 없습니다.) 

기반은 GTK 3이며, 그놈과 비교하자면 가벼운 데스크탑 매니저입니다.

추가적으로, Gnome보다 문서량이 적은 탓에 커스텀이 많이 없는 건 사실입니다. 이 점 유의해주세요.

# Installation

먼저 pacman을 업데이트 해줍니다. 

```bash
sudo pacman -Syu
```

그 다음 xorg-server를 깔아줍니다

```bash
sudo pacman -S --needed xorg
```

  

추가적인 설치 명령어입니다.

```bash
sudo pacman -S --needed xfce4 mousepad parole ristretto thunar-archive-plugin thunar-media-tags-plugin xfce4-battery-plugin xfce4-datetime-plugin xfce4-mount-plugin xfce4-netload-plugin xfce4-notifyd xfce4-pulseaudio-plugin xfce4-screensaver xfce4-taskmanager xfce4-wavelan-plugin xfce4-weather-plugin xfce4-whiskermenu-plugin xfce4-xkb-plugin file-roller network-manager-applet leafpad epdfview galculator lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings capitaine-cursors arc-gtk-theme xdg-user-dirs-gtk
```

## systemctl 활성화

Display Manager와 Network Manager를 서비스로서 활성화를 해줘야합니다. 활성화를 해주어야 다음 로그인때부터, 자동으로 systemd에 의해 동작됩니다.

```bash
systemctl enable lightdm
systemctl enable NetworkManager
```

## Reboot

```bash
reboot
```

재부팅 되신 후, 로그인 화면이 나타납니다. 로그인을 해주면 대부분의 설정은 끝나셨습니다.

# 한글 입/출력 설정
[uim](./uim.md)

