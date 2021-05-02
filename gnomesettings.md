# Gnome 설치 및 마무리 설정

# 개요
먼저 [Installation](https://archlinux-korea.github.io/Installation.html)에서 설치를 다 하시고 와야합니다.

다 끝나고 다시시작을 하면 **리눅스 초보자이시거나 그래픽 작업이 필요하다고 생각되시는 분들은** GUI를 설치해야 합니다.

하지만 GUI종류는 많습니다. 대표적으로 gnome, KDE, Xfce 등이 있지만 가장 관리하기 쉬운 GUI는 Gnome이므로 여기서는 Gnome 설치법을 알려드리겠습니다.

그리고 그 밑에 필요한 필수 설정을 알려드리겠습니다.

# Gnome 설치
아치 리눅스에서는 편리한 pacman이 있어 명령어 하나로 설치를 할 수 있습니다.
```
sudo pacman -S gnome
```
그후에 무슨 패키지들을 설치할거냐고 물어보는데 추가로 변경사항 없으시면 모두 그냥 엔터를 누르시기 바랍니다.

마지막으로 끝에 `[Y/N]`이 나오시면 Y를 누르세요. 

그러시면 몇분동안 설치가 됩니다. 뭔가 많은 글씨들이 정신없이 나오지만 정상입니다.

설치가 완료되면 아치 리눅스를 시작할때 Gnome이 실행되어야 합니다.
```
sudo systemctl enable gdm
```
* systemctl : 시스템이 시작할때 앱을 시작해주거나 앱을 관리하는 프로그램입니다.
* enable : 시스템이 시작할때 앱을 시작해주는 명령어 입니다.
* gdm : Gnome 로그인 프로그램입니다.

위 명령어를 치고 시스템을 다시시작 합니다.
```
reboot
```
그러면 GUI가 잘 작동될 겁니다.

# 필수 설정
## 한글 깨지는 오류
기본적으로는 한국어 글씨체가 없어 한국어를 표시할 수가 없습니다.

그래서 폰트를 설치해야 합니다. 폰트를 설치하는 명령어입니다.
```
sudo pacman -S noto-fonts-cjk
```
그 후에는 한글이 잘 나오실겁니다.

## 한글 입력 안됨
한글이 잘 나와도 한글을 적을 수가 없습니다. 이 또한 기본적으로 한글 입력이 없기 때문입니다.

그래서 이 명령어를 통해 한글 입력을 설치해야 합니다. **참고로 이 명령어로 설치할시 Gnome이 아닌 다른 GUI일 경우 적용이 불가능합니다.**
```
sudo pacman -S ibus ibus-hangul
```
그리고 시스템을 다시시작 하고 적용을 해야 합니다.

Settings -> Keyboard -> Input Sources 밑에 있는 +를 누릅니다 -> '...'을 누릅니다. -> Other -> 밑에 쭉 내려서 Korean(Hangul)을 누릅니다 -> Add

위 방법을 하시면 그 이후로 한글 입력이 가능해집니다.

## 드라이버 잡기
일단 자신의 컴퓨터 드라이버 제조업체가 어디인지 알아야 합니다.

만약 드라이버 업체가 NVIDIA일 경우 아래 명령어를 입력하세요.
```
sudo pacman -S nvidia nvidia-utils nvidia-settings
```
만약 드라이버 업체가 AMD일 경우 아래 명령어를 입력하세요.
```
sudo pacman -S mesa amdvlk
```
만약 드라이버 업체가 Intel일 경우 아래 명령어를 입력하세요.
```
sudo pacman -S mesa vulkan-intel
```
각각 제조업체에 따라서 설치가 완료되었으면 다시시작합니다.

# 권장 설정
## 브라우저 설치
GUI를 사용하는 사람은 대부분 브라우저를 설치합니다. 밑에 각각 브라우저별로 설치법입니다.

Mozilla Firefox
```
sudo pacman -S firefox
```
Google Chrome
```
git clone https://aur.archlinux.org/google-chrome.git
cd ./google-chrome
makepkg -si
cd ..
rm -rf ./google-chrome
```
Google Chromium(Chrome의 오픈소스 브라우저)
```
sudo pacman -S chromium
```
Opera
```
sudo pacman -S opera
```
Microsoft Edge 개발 버전
```
git clone https://aur.archlinux.org/microsoft-edge-dev-bin.git
cd ./microsoft-edge-dev-bin
makepkg -si
cd ..
rm -rf ./microsoft-edge-dev-bin
```
## Gnome Tweaks
Gnome tweaks는 Gnome 더 커스텀할 수 있도록 나온 프로그램입니다.

커스텀하기를 원하는 사람만 설치해봅시다.

일단 아래와 같이 Gnome Tweaks를 설치합니다.
```
sudo pacman -S gnome-tweaks
```

그리고 모든 프로그램 -> Utilites -> Tweaks를 누르면 여러가지 설정이 보이는데요,

여기서 자기가 원하는대로 설정하시면 됩니다!