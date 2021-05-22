# uim 설치법
## uim 설치
먼저 AUR에 있는 uim을 설치합니다.
```
git clone https://aur.archlinux.org/uim.git
cd ./uim
makepkg -si
cd ..
rm -rf uim
```
설치 도중 컴파일을 진행하기 때문에 시간이 1~2분 정도 걸릴 수 있습니다.

(만약 yay로 설치하시려면 `yay uim`을 하시면 됩니다.)
## uim 실행
uim이 시작할때 실행이 되어야 하므로 xprofile 수정을 해야 합니다.
```
# nano
sudo nano ~/.xprofile
# vim
sudo vim ~/.xprofile

(그 외에도 vi, emacs, ee등으로도 각자 방법으로 열 수 있습니다.)
```
그리고, 밑에 있는 내용을 적어주세요. vi 또는 vim은 적기전에 i를 눌러야 합니다.
```
export GTK_IM_MODULE=uim
export QT_IM_MODULE=uim
uim-xim &
export XMODIFIERS=@im=uim
```
그후, 그 문서를 저장하고 나갑니다.
```
# nano
ctrl + z, y, 엔터
# vim 또는 vi
Esc, :wq
```
## uim 한글 설정
밑의 명령어를 입력합니다.
```
uim-pref=gtk
```
그러면 설정 프로그램이 나오실건데 위쪽에 있는 specity default IM을 체크합니다.

그후, Default input method에서 많은것들이 나오실건데 거기서 위로 쭉 가서 Byeoru를 선택합니다.

다음으로, 옆에 목록에서 밑으로 내려 Byeoru를 잧습니다.

거기 안에서 ESC turns off Hangul mode (for vi users) 체크 표시를 끕니다.

그리고, 옆에 Byeoru key bindings 1을 찾습니다.

거기서 [Byeoru] on에서 오른쪽에 있는 Edit 버튼을 누릅니다.

그리고 Grab 버튼을 누른 후, 한/영 키를 누릅니다.

Add 버튼을 누른후, <Shift>space를 누르고 Remove를 누릅니다.

마찬가지로 [Byeoru] off에서 오른쪽에 있는 Edit 버튼을 누릅니다.

그리고 Grab 버튼을 누른 후, 한/영 키를 누릅니다.

Add 버튼을 누른후, <Shift>space를 누르고 Remove를 누릅니다.

다음으로,  [Byeoru] convert Hangul to Chinese characters에서 오른쪽에 있는 Edit 버튼을 누릅니다.

그리고 Grab 버튼을 누른 후, 한자 키를 누릅니다.

Add 버튼을 누른후, hangul-hanja를 누르고 Remove를 누릅니다.

드디어! 마지막으로 밑에 있는 OK버튼을 누릅니다.

그리고 재부팅하면 다음부터 한글로 사용이 가능해집니다!
