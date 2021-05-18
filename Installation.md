# Arch Installation

# 설치 전 주의사항

 간혹가다가 철자 하나로, 또는 가이드 참고 중 프롬프트인 #도 같이 넣는다는 행동은 하지 말아주세요. 오히려 설치가 꼬일 수 있습니다. 철자 하나하나 잘 살펴가면서 해주세요.

이 메뉴얼은 꼭 이렇게 해야할 필요가 없음을 알려드립니다. 오히려 하다가 에러가 날 수 있으면 그땐 적극적으로 구글링을 이용해주세요.

설치화면 진입 자체, 혹은 부팅 매체(BIOS 화면)에서 애초에 usb를 인식하지 못하는 등의 문제는 UEFI세팅 문제일 가능성이 큽니다. secure boot가 ‘사용 안함(Disabled)’으로 되어있는지 확인하시기 바랍니다. 

혹시, 확인 이후에도 문제가 발생할 경우에 우선은 구글 등 검색을 해보시는 것을 추천드립니다. 높은 확률로, fast boot, 혹은 CSM모드(혹은 ‘LEGACY/BIOS MODE’) 에서 발생하는 오류로 생각됩니다. (추천드리는 것은 모두 ‘사용 안함(Disabled)’입니다.) 

팁으로, 아치리눅스 위키를 먼저 보시고 그다음에 이글을 읽으시면 빨리 이해가 되실겁니다.

추가로, 이 글은 [whjeon님의 블로그](https://whjeon.com/arch-install/) 에서 몇 부분 가져왔음을 알려드립니다.


# 설치 준비: 설치용 usb 제작

## 윈도우

[Index of /pub/archlinux/iso/latest](http://ftp.lanet.kr/pub/archlinux/iso/latest/)

라넷 미러에서 archlinux-(현재날짜)-x86_64.iso 를 다운 받아줍니다.

그리고 usb에 구워주시면 됩니다. (Rufus를 사용하시면 편합니다.)

그리고 컴퓨터를 꺼주시고 usb를 꽂아 바이오스로 진입합니다.

이때, 메인보드 제조사에 따라 바이오스 진입키가 다르니 검색해서 숙지해주시기 바랍니다.

## Linux

리눅스 시스템에서 usb 마운트 이후, 관리자 계정으로 변경하거나 혹은 앞에 ‘sudo’를 붙인 후, 본인 계정의 비밀번호를 이용하여 아래의 명령을 실행합니다.

```bash
#lsblk
```

아래 명령어를 통해 usb를 포맷하고 새로운 iso를 작성할 준비를 합니다. 주어진 커맨드의 ‘/dev/sdb’에서 ‘b’부분을 본인 환경에 맞게 입력해야 합니다. 위에서 확인한 본인 USB 장치의 이름을 넣어줍니다.

아래 명령어는 USB 내부의 모든 파일을 삭제합니다.
중요한 파일이나 삭제가 용인되지 않는 파일은 반드시 백업을 하시고 진행하시길 바랍니다.

```bash
#wipefs --all /dev/sdb
```

아래 명령어를 통해 준비된 usb에 iso파일을 구워줍니다. **if 이후에 iso파일을 다운로드 한 경로를 본인 환경에 맞게 입력**합니다. 해당 경로에 아치리눅스 iso파일이 하나밖에 없다고 가정할 경우, ‘archli’와 같이 앞 부분을 입력한 후, (TAB) 키를 이용해 자동 완성이 가능합니다. 

다만, **반드시 자동 완성된 파일이 원한 결과대로 입력되었는지 확인하시기 바랍니다.**

아래 명령어에서 **‘of’부분 이후에는 위에서 준비한 usb 경로를 본인 환경에 맞게 입력**합니다.

```bash
#dd bs=4M if=~/Downloads/archlinux.iso of=/dev/sdb status=progress && sync
```

이제 재부팅해줍니다. 재부팅 도중에 본인 메인보드에 맞는 바이오스/UEFI진입 키를 이용합니다.

# Base System 설치

## uefi 설치 가능 여부 확인

먼저 UEFI 설치가 가능한지 확인해보겠습니다.

UEFI냐 BIOS냐에 따라 설치방법과 설치되는 파티션이 달라지므로 주의해야합니다.

BIOS로 부팅된 경우, 디스크 타입이 MBR, 그리고 필요로 하는 파티션이 다르고, UEFI로 부팅된 경우, 기본적으로 권장하는 설치 설정은 디스크 타입을 GPT로 그리고 반드시 ESP파티션을 가지고 있을 것을 요구합니다.

그러니 잘 살펴보시고 진행하시길 권장드립니다.

```bash
#ls /sys/firmware/efi
```

결과에 ‘efivars’라는 폴더가 보이면 UEFI로 설치하실 수 있습니다.

## 인터넷 연결 확인

인터넷 연결 상태를 확인합니다. 인터넷이 연결되어 있어야 설치가 가능합니다.

```bash
#ping -c 3 www.google.com
```

## 파티션 계획

파티션은 대용량 저장 장치(흔히 하드라 불린다.)를 어떠한 목적으로 구휙으로 나누어 사용할지에 대해 결정하는 순서입니다. 일반적으로 세파트로 나뉘는데, 각각 <부팅을 위한 구획>, <데이터들이 저장될 구획>, 그리고 <리눅스 스왑>으로 나뉘게 됩니다.

가장 먼저, 부팅을 위한 파티션에 UEFI로 부팅했고, 설치하려는 경우 ‘ESP’라고 하여 ‘EFI System Partition’으로 설정해야 합니다.

ESP는 EFI System으로 타입이 설정된, FAT 파일 시스템으로 포맷된 파티션을 뜻합니다. Boot폴더에 마운트 될 파티션입니다.

두번째 구획인 데이터 저장 구획은 단순하게 Linux File System이라는 타입으로 사용할 예정입니다. 이 두번째 파티션은 ‘/(root)’에 마운트 되어 모든 데이터가 저장될 구역이라고 생각하시면 됩니다.

마지막 스왑 파티션도 역시 이름처럼 Linux Swap이라는 타입을 붙이면 되겠습니다.

이 과정은 **드라이브의 구획을 나누고 계획을 잡아두는 것**으로 실제 그 계획에 맞는 환경을 만드는 것은 다음 단계인 포맷에서 진행 됩니다.

여기서 리눅스 스왑이란 램 환경이 모자랄 때 하드디스크의 스왑으로 설정된 부분에 자료를 로드해두고 램처럼 활용하는 것을 의미합니다. 그러니 사용에 따라 설치용량을 달리해주면 됩니다.

아래 명령어를 통해 내 드라이브로 어떤 것들이 있는지 확인해보겠습니다. 용량을 통해서 부팅에 이용한 usb와 하드드라이브를 분명히 구분해서 기억하셔야 합니다.

```bash
#lsblk
```

대용량 장치가 여러개일 경우 sda,sdb,sdc순으로 출력됩니다. 부팅을 위해 마운트한 usb가 아닌지 확실히 확인하셔야 합니다. 확인은 보통 용량으로 하면 큰 문제가 없습니다.

그리고 다음 단계로 넘어가기전, 파티션 용량을 어떻게 할지를 생각해보고 넘어가시는걸 추천드립니다.

아래부터는 cfdisk / gdisk 둘 중에 한가지를 선택하셔서 진행하시면 됩니다.
초보자는 기본적인 표 형태의 인터페이스를 제공하는 cfdisk 를 추천하지만,
커맨드 라인 인터페이스에 익숙하시다면  gdisk를 추천합니다.

아래 명령어들은 하드디스크 내부의 모든 파일을 삭제하거나 사용할 수 없게 만듭니다.
중요한 파일이나 삭제가 용인되지 않는 파일은 반드시 백업을 하시고 진행하시길 바랍니다.

### cfdisk 이용

아래 명령어에 디스크 경로가 포함되는데 본인 환경에 맞는 것을 입력하시면 됩니다.

```bash
#cfdisk /dev/sda
```

UEFI 설치를 위해서는 라벨이 ‘GPT’라고 상단에 표기 있는지 확인합니다. MBR이 아니어야 합니다! 만약 처음에 물어보면 GPT를 선택해주시면 됩니다.

모든 파티션은 ‘Primary’로 진행합니다.(사용자에게 물어보는 경우)

표에 기존 파티션이 기록된다면 보이는 기존 파티션을 한 줄씩 내려가면서 ‘DELETE’해줍니다.(키보드의 화살표 방향키를 이용) 그리고 나서 모든 용량이 FREE SPACE가 되고 나서 계획한 대로 첫번쨰 용량을 잡아주면 됩니다.

예시로 512MB의 파티션을 만들어 보겠습니다.

```bash
'NEW’선택
용량 크기를 입력
'TYPE’으로 커서를 옮긴 후 엔터
'EFI System’을 선택.
```

이부분은 EFI system이라 주로 파일을 저장하는 파티션이 아니니 크게 잡을 필요 없습니다.

512MB여도 적당한 수준입니다.

두번째 만들 파티션은 swap 파티션 입니다. 방향키를 아래로 내려서 free space를 향하게 하면 아래에 NEW버튼이 다시 생긴 것을 볼 수 있습니다.

스왑도 주기억장치(램)처럼 4G, 2G 이렇게 잡는게 좋습니다.

```bash
'NEW’
용량 크기를 입력
'TYPE’으로 ‘Linux Swap’
```

이제 파일을 주로 저장할 /dev/sda3 파티션도 만들어보겠습니다.

```bash
‘NEW’ 선택
용량 크기는 디폴트가 남아있는 최대 크기로 설정 되어있는지 확인해주고 그냥 ‘엔터’
'TYPE’은 ‘Linux FileSystem’
‘WRITE’를 선택
'QUIT’선택
```

### gdisk 사용

바로 상단의 cfdisk를 이용하신 분들은 이 파트는 넘어가시면 됩니다.

아래 명령어에 디스크 경로가 포함되는데 본인 환경에 맞는 것을 입력하시면 됩니다.

```bash
#gdisk /dev/sda
```

모든 파티션은 ‘Primary’로 잡습니다. 이 유틸은 퍼스트 섹터와 라스트 섹터를 물어보는데 퍼스트 섹터에서 라스트 섹터에 해당하는 값을 입력을 하지 않도록 주의하시면서 천천히 진행하시기 바랍니다.

```bash
'o’를 입력 — 모두 지우고
(/dev/sda1 생성을 위해)
'n’ 입력 (New)
첫 번째 파티션이므로 ‘엔터’
(default가 1로 되어있는지 확인/아닐 경우 ‘1’을 입력)
첫 번째 섹터는 알아서 계산해주므로 ‘엔터’
라스트 섹터는 용량으로 입력 가능하므로 ‘+512mb’입력
파일시스템 코드로 ‘EF00’ 입력
'p’를 통해 제대로 나뉘었는지 확인

(표의 형태로 출력됨. /dev/sda1, EF00(혹은 EFI System), 512mb를 확인)

다시 ‘n’입력으로 /dev/sda2 생성 시작
기본 값으로 ‘2’가 되있는지 확인 후 ‘엔터’
첫 번째 섹터는 기본값대로 ‘엔터’
용량으로 입력 ‘+4G’
파일 시스템 코드로 ‘8200’ 입력
'p’를 통해 재확인후 
/dev/sda3 생성을 위해 'n’
기본 값으로 ‘3’이 되있는지 확인 후 ‘엔터’
첫 번째 섹터는 기본값대로 ‘엔터’
이번엔 마지막 섹터까지 사용할 예정이므로 ‘-1’을 입력
파일 시스템 코드로 ‘8300’ 입력
마지막으로 ‘w’를 입력해서 ‘write
```

## 포멧

포맷은 위에서 진행한 파티션 각각의 계획에 맞는 파일시스템으로 진행합니다. UEFI환경에서 부팅 파티션은 FAT32 파일시스템을 원합니다. 

따라서 아까 EFI시스템으로 파티션을 계획했던 sda1은 fat32로 포맷을 할 것이고, 기본 파일 시스템으로 쓰기로 했던 sda3는 최근 가장 범용으로 쓰이는 ext4파일 시스템으로 포맷하겠습니다. 

스왑 파티션은 바로 다음 단계에서 포맷합니다. /dev/sda2를 포맷하지 않도록 주의해주세요.

또한, 다른 하드라면 sdb또는 sdc 이렇게 뜰 수도 있습니다. 설치 하드를 꼭 확인해주세요.

아래 명령어에 디스크 경로가 포함되는데 본인 환경에 맞는 것을 입력하시면 됩니다.

```bash
#mkfs.vfat -F32 /dev/sda1
#mkfs.ext4 -j /dev/sda3
```

## 스왑

스왑드라이브를 만들겠습니다. 스왑드라이브는 위의 포맷과 유사한 명령어를 수행한 이후, 켜주는 과정을 추가로 거칩니다. 

아래 명령어에 디스크 경로가 포함되는데 본인 환경에 맞는 것을 입력하시면 됩니다.

다른 하드라면 sdb또는 sdc 이렇게 뜰 수도 있습니다. 설치 하드를 꼭 확인해주세요.

```bash
#mkswap /dev/sda2
#swapon /dev/sda2
```

## 마운트

마운트는저장 장치에 접근할 수 있는 경로를 디렉터리 구조에 편입시키는 작업을 말합니다.

지금은 루트폴더와 루트아래의 부트 폴더만 마운트하면 됩니다.

우선은 /mnt에 루트폴더로서 쓸 Linux FileSystem 파티션(/dev/sda3)을 연결해보겠습니다. 아래 명령어에 디스크 경로가 포함되는데 본인 환경에 맞는 것을 입력하시면 됩니다.

이때도 다른 하드라면 sdb또는 sdc 이렇게 뜰 수도 있습니다. 설치 하드를 꼭 확인해주세요.

```bash
#mount /dev/sda3 /mnt
```

그리고 나서 루트 폴더 아래에 부트 폴더를 만들어 줍니다.

```bash
#mkdir /mnt/boot
```

부트 폴더를 마운트하려고 만들었던 /dev/sda1을 마운트해줍니다.

```bash
#mount /dev/sda1 /mnt/boot
```

## 미러리스트

미러리스트는 아치에서 패키지 설치를 할 때 기본 데이터 베이스로 활용할 웹 서버를 나열해놓은 리스트입니다. 미러 리스트에 내가 사용할 서버가 가깝거나, 속도가 가장 빠르게 접속되는 것으로 적용되어 있어야 설치 환경에서 유리합니다.

이러한 이유로 미러리스트 서버가 적힌 리스트를 수정해야합니다.

nano 또는 vim을 이용해 하시면 됩니다.

여기선 vim으로 하겠습니다

```bash
#vim -w /etc/pacman.d/mirrorlist
그리고 적기위해 'a' 키 누른다.
```

그리고 맨 위에 대한민국 서버 리스트를 적어줍니다.

```bash
Server = http://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
Server = https://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
```

이렇게 2개만 적었는데도 불안하다고 느끼신다면 아래처럼 적어주세요

```bash
Server = http://ftp.harukasan.org/archlinux/$repo/os/$arch
Server = https://ftp.harukasan.org/archlinux/$repo/os/$arch
Server = http://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
Server = https://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
Server = http://mirror.premi.st/archlinux/$repo/os/$arch
```

각 줄이 (~~~~~)/$repo/os/$arch 로 끝이 나야 합니다. 철자 주의해주세요.

vim 편집기를 사용하여 편집 후 저장하고 나오는 방법은 **esc키를 누르고, :wq를 치고 엔터** 누르면 됩니다

## pacstrap

이번 단계는 본인에게 필요한 것들을 아래 명령어에 추가하여 작성하시면 됩니다.

첫줄은 기본이니 이 줄 뒤에 그 다음에 제시한 파일들을 여러개 나열하시면 됩니다.

```bash
#pacstrap /mnt base linux linux-firmware
```

```
nano(vim을 선택하실 분들은 제외)
vim(nano만 쓰실 분들은 제외, 둘다 선택 가능)
dhcpcd(networkmanager를 선택하실 분들은 제외)
networkmanager(dhcpcd를 선택하신 분들은 제외) -> 초보자들에게 추천드립니다
base-devel(대부분의 사용자는 필요할 것으로 예상)
man-db
man-pages
texinfo
dosfstools
e2fsprogs(여기까지는 대부분의 사용자에게 권장)
mdadm(여기서부터는 뭐하는 패키지인지 모르신다면 설치 안하셔도 무방할 것으로 예상)
lvm2
```

## genfstab

파일 시스템이 어떤 드라이브와 마운트 되어있는지를 알 수 있는 파일 시스템 테이블 파일입니다. 

그 파일 시스템 테이블 파일을 생성하는 작업입니다. 시스템 사용중에도 마운트 된 디바이스, UUID를 확인할 때 사용하는 파일입니다.

```bash
#genfstab -U /mnt >> /mnt/etc/fstab
```

# 아치 리눅스 진입

## 루트로 아치 리눅스 진입

```bash
#arch-chroot /mnt
```

시스템 내부로 들어오는 명령어 입니다. 이로써 추가적인 설정을 해줍시다.

여기서 소프트웨어를 설치하셔도 무방합니다. usb의 아치리눅스 라이브에 들어온것이라, 설치 후에도 패키지가 설치되어 있습니다.

## 인터넷 확인

간혹가다 진입 전엔 잘되다가 진입 후에도 안 될 경우가 있으니 다시 한번 더 체크해줍시다.

```bash
#ping -c 3 www.google.com
```

## 루트 비밀번호 설정

```bash
#passwd
```

root 계정의 비밀번호를 설정합니다. 원래 안보이는거니 걱정마시고 원하는 비번을 치고 엔터를 누르시면 됩니다.

게다가, 확인차 총 두번 입력하는게 맞습니다. 놀라지 마세요.

## locale 설정

```bash
#vim -w /etc/locale.gen
```

파일이 뜨면 화살표 키를 이용해 아래로 내려가 ‘en_US.UTF-8’을 찾습니다. 'a'를 눌러 INSERT모드에 진입하시고 앞의 주석(‘#’)만 제거해주세요. 그리고 esc, :wq 입력 , ‘엔터’ 입니다.

```bash
#locale-gen
```

그리고 언어를 적용해줍시다.

## 언어 설정

```bash
#echo LANG=en_US.UTF-8 > /etc/locale.conf
```

여기서 만약 한글 오류를 좋아하신다면 US.UTF-8 대신 ko_KR.UTF-8’을 입력해주시면 됩니다.

```bash
#export LANG=en_US.UTF-8
```

위의 명령어로 현재 진행하고 있는 쉘 환경(/bin/bash)에서의 언어를 선언합니다.

## PC 이름 설정

```bash
#echo (pcName) > /etc/hostname
```

명령어 예시에서 ‘(pcName)’ 부분에 원하는 PC이름을 설정하시면 됩니다.

## 하드웨어 시간 설정

휴대폰 등의 별도 디바이스를 통해 구글에 ‘current utc time’을 검색해 현재 utc 시간을 확인합니다. 아래 명령어를 utc 시간을 입력합니다.  순서는 (월일시분연도) 입니다. 

예를 들어, 2021년 12월 31일 14시 56분은 ‘#date 123114562021’로 입력됩니다.

```bash
#date (MMDDHHmmYYYY)
#hwclock --systohc --utc
```

## 로컬 타임 설정

하드웨어 시간을 utc로 변경했으니 이제 한국 시간으로 맞춥니다.

```bash
#ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

## 일반 사용자 추가

아래 명령어의 ‘(name)’ 부분에 본인이 사용하실 이름을 입력합니다.

```bash
#useradd -m -g users -G wheel -s /bin/bash (name)
```

그리고 passwd 뒤에 여러분들의 유저명(username)을 적어서 비번을 바꿔줍니다.

```bash
#passwd (username)
```

## sudo 설정

sudo에 대한 논란이 많습니다. 대부분 보안 논쟁으로 이에 대한 대안으로 doas, su -l가 있습니다.
그럼에도 이 글에선 sudo를 사용합니다. (아치 입문자들을 위하여)

sudo가 설치되지 않은 경우, ‘#pacman -S sudo’를 통해 설치합니다.

vi/vim 에디터가 익숙하신 분들은 해당 에디터 설치 후, 그냥 ‘#visudo’ 하시거나 ‘#EDITOR=vim visudo’ 하셔도 됩니다. visudo는 vi를 기본 에디터로 하기 때문에 nano로 설정을 하고 실행합니다.

```bash
#EDITOR=nano visudo
```

sudo 명령어 가능자에 본인을 추가하겠습니다. sudoers 파일은 $nano /etc/sudoers 커맨드로도 수정이 가능하지만, 원칙은 visudo를 통해 수정하는게 맞습니다. visudo가 sudoers파일의 문법적 오류 등을 점검하고 잠금하는 기능을 하기 때문에 임의로 ‘$nano /etc/sudoers’ 를 직접 수정하는 것은 권장하지 않습니다.

이제 내려가서 몇가지를 수정하시면 됩니다.

```bash
##
## User privilege specification
##
root ALL=(ALL) ALL

## Uncomment to allow members of group wheel to execute any command
# %wheel ALL= (이렇게 주석처리 되있던 것을)
%wheel ALL= ('#'을 지워서 이렇게되도록 주석 제거 처리 해주면 됩니다.)

## Same thing without a password
# %wheel ALL=(ALL) NOPASSWD: ALL
(위 설정은, wheel그룹이 '비밀번호도 필요없이' sudo만 붙이면 명령 실행이 가능하도록 합니다. 보안상의 이유로 강하게 비추천합니다. 어떤 변경을 하는 것인지 정확히 알고 계시는 분만 주석 제거하시기 바랍니다.)
```

## 부트로더 설치

2가지 방법이 있습니다

1. gurb사용
2. systemd-boot는 systemd에 내장된 기능을 이용해 부트

### 1. gurb 사용

설치 전에 레파지토리(아치 리눅스 측 패키지 저장소)와 로컬 레파지토리(설치 중인 컴퓨터가 가진 소프트웨어들의 정보)를 업데이트, 동기화 합니다.

```bash
#pacman -Syu
#pacman -S grub efibootmgr
```

이제 부트가 가능하도록 ‘grub’을 설치하겠습니다.

```bash
#grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch --recheck
```

추가적으로 ‘nano -w /etc/default/grub’ 등을 통해, 부트로드의 기본 설정을 변경할 수 있습니다. 예를 들어, 부팅할 OS선택을 기다릴 시간을 0 또는 1초 처럼 짧게 변경하거나 할 수 있습니다. 초보자는 건드리지 않습니다.

설정이 완료된 경우, ‘/etc/default/grub’을 기반으로 부팅을 위한 ‘파일’을 생성/설치합니다.

```bash
#grub-mkconfig -o /boot/grub/grub.cfg
```

### 2. systemd-boot 사용

grub를 사용하지 않는 분들에게만 해당합니다.

```bash
#bootctl --path=/boot install
```

/boot/loader 의 /loader.conf파일을 수정해야 합니다.

```bash
#nano -w /boot/loader/loader.conf
```

그리고 파일을 다음과 같이 작성합니다

```bash
default arch
editor 1
timeout 3
```

디폴트 이후 arch에는 본인이 알아볼 이름을 넣으시면 됩니다. 보통은 arch를 이용합니다. 

다만, 주의하실 점은 이 부분에 넣은 이름을 꼭 기억하셨다가 바로 뒤에 따라오는 과정에서 entries/abcd.conf 파일의 abcd에 동일하게 작성해 주셔야 한다는 점입니다. 지금 arch로 작성하시면 나중에 그냥 entries/arch.conf파일을 만드시면 됩니다.

이제 로더 폴더 아래의 엔트리스 폴더에 파일을 생성하고 수정하겠습니다.

```bash
#nano -w /boot/loader/entries/arch.conf
```

아까 디폴트에 적으셨던 이름으로 파일을 생성해주시면 됩니다. 예를 들어 arch가 아닌 linux로 하셨으면, linux.conf로 바꿔 적으시면 됩니다.

그리고 파일에 다음과 같이 작성합니다.

```bash
title ArchLinux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=/dev/sda3 rw
```

## exit

이제 chroot 환경에서 나가서 재부팅하는 단계입니다. 그러나, 지금 바로 재부팅해선 안됩니다.

재부팅 후 네트워크가 안잡히는 경우가 생기기때문에 마지막으로 네트워크를 설정후 재부팅 해주시면 됩니다.

여기서

(1) dhcpcd만을 사용하실 분,

(2) 데스크탑 사용자 중에서 Gnome, Plasma 등의 데스크탑 환경을 사용하지 않을 예정이신 분들이나,

(3) 유선 연결만 사용하시는 분들,

(4) networkmanager가 무엇인지 정확이 이해하고 있지만 사용하지 않기로 결정하셨던 분들은

```bash
#systemctl enable dhcpcd
```

이 명령어를,

그렇지 않고,

(1) 노트북 사용자이거나,

(2) networkmanager의 편리한 기능을 활용하고 싶으신 분들,

(3) Gnome/Plasma처럼 networkmanager와 가깝게 개발되어 있는 데스크탑 환경을 사용하고자 하시는 분들은

```bash
#systemctl enable NetworkManager.service
```

이 명령어를 해주세요.

그리고 exit명령어를 통해 chroot 환경에서 나옵니다.

```bash
#exit
```

## Umount

```bash
#umount -lR /mnt
```

각 디렉터리에 마운트된 장치를을 모두 제거합니다.

## Reboot

```bash
#reboot
```

재부팅을 합니다.

그리고 다시 켜지는 시점에서 설치usb를 빼주시면 됩니다.

# 이 이후에 설치 할 것들

현재 키고 부팅을 하시면 그래픽이 아무것도 없는 ctl 환경을 접하시게 될겁니다.

만약 지금 이 상태가 좋으시면 그대로 하셔도 되고,

만약 gui를 설치해 즐기고 싶다면,

xorg, gnome을 구글링을 통해 찾아주시면 됩니다.
