# Raspberry Pi 원격 접속 실습 정리

## 1. 실습 목표

이번 실습에서는 Raspberry Pi에 별도의 모니터를 연결하지 않고  
노트북과 태블릿에서 원격으로 사용할 수 있도록 설정하였다.

최종 목표는 다음과 같다.

- Raspberry Pi OS 설치
- Wi-Fi 연결
- SSH 원격 접속
- VNC 활성화
- 노트북에서 Raspberry Pi 화면 확인
- 태블릿에서 Raspberry Pi 화면 사용

---

### SSH

모니터 없이 Raspberry Pi를 설정하기 위해 SSH 기능을 활성화하였다.

SSH는 다른 컴퓨터에서 Raspberry Pi의 터미널을 원격으로 사용할 수 있게 해준다.

---

## 2. Raspberry Pi 부팅

OS 설치가 완료된 microSD 카드를 Raspberry Pi에 삽입하고 전원을 연결하였다.

이번 실습에서는 Raspberry Pi와 노트북을 HDMI로 직접 연결하지 않았다.

일반적인 노트북의 HDMI 단자는 출력용이기 때문에 Raspberry Pi의 HDMI 출력을 노트북 화면으로 직접 받을 수 없다.

따라서 Wi-Fi를 이용한 원격 접속 방식을 사용하였다.

### Wi-Fi

노트북과 Raspberry Pi가 같은 네트워크를 사용할 수 있도록 Wi-Fi 정보를 입력하였다.

---

## 3. 첫 번째 SSH 연결 시도

Windows PowerShell에서 다음 명령어를 입력하였다.

```powershell
ssh team5@Team5.local
ssh 사용자이름@호스트이름.local
```

- `사용자이름` : Raspberry Pi에 생성한 계정 이름
- `호스트이름` : Raspberry Pi에 설정한 장치 이름


---

## 4. 휴대폰 핫스팟 사용

기존 Wi-Fi 환경에서 Raspberry Pi와 노트북의 통신이 제대로 이루어지지 않아 휴대폰 핫스팟을 사용하였다.

휴대폰 핫스팟에 다음 두 장치를 연결하였다.

- 노트북
- Raspberry Pi

휴대폰의 연결된 기기 목록에서

```text
Team5
```

가 표시되어 Raspberry Pi가 핫스팟에 정상적으로 연결된 것을 확인하였다.

---

## 5. 네트워크 상태 확인

현재 노트북의 네트워크 정보를 확인하기 위해 다음 명령어를 사용하였다.

```powershell
ipconfig
```

노트북의 Wi-Fi IPv4 주소는 다음과 같았다.

```text
10.174.121.33
```

기본 게이트웨이는 다음과 같았다.

```text
10.174.121.158
```

현재 네트워크에서 확인되는 장치를 보기 위해 다음 명령어도 사용하였다.

```powershell
arp -a
```


---


## 6. Ping 테스트

Raspberry Pi와 노트북 사이의 네트워크 연결을 확인하기 위해 다음 명령어를 사용하였다.

```powershell
ping Team5.local
```

결과:

```text
보냄 = 4
받음 = 4
손실 = 0
```

따라서 Raspberry Pi가 네트워크에서 정상적으로 응답하는 것을 확인하였다.

이때 Raspberry Pi는 IPv6 주소로 응답하였다.

```text
2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

---

## 7. IPv4 접속 확인

IPv4 주소로 Raspberry Pi를 찾기 위해 다음 명령어를 사용하였다.

```powershell
ping -4 Team5.local
```

하지만 다음과 같은 오류가 발생하였다.

```text
Ping 요청에서 Team5.local 호스트를 찾을 수 없습니다.
```

따라서 `.local` 이름을 이용한 IPv4 주소 확인은 제대로 이루어지지 않았다.

---

## 8. IPv6를 이용한 SSH 접속

Ping 테스트에서 확인한 IPv6 주소를 직접 사용하여 SSH 접속을 시도하였다.

```powershell
ssh -6 team5@2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

처음 연결 시 다음 메시지가 나타났다.

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

다음과 같이 입력하였다.

```text
yes
```

그 후 Raspberry Pi Imager에서 설정한 비밀번호를 입력하였다.

SSH 접속에 성공하면 다음과 같이 표시되었다.

```text
team5@Team5:~ $
```

이 화면이 나타나면 Windows PowerShell 창을 사용하고 있지만 이후 입력하는 명령어는 Raspberry Pi에서 실행된다.

---

## 9. Raspberry Pi 파일 확인

SSH 접속 후 다음 명령어를 실행하였다.

```bash
ls
```

결과:

```text
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
```

Raspberry Pi OS의 사용자 디렉터리가 정상적으로 생성되어 있는 것을 확인하였다.

---

## 10. VNC 활성화

Raspberry Pi의 그래픽 화면을 노트북이나 태블릿에서 보기 위해 VNC를 활성화하였다.

다음 명령어를 실행하였다.

```bash
sudo raspi-config
```

설정 화면에서 다음 순서로 이동하였다.

```text
Interface Options
→ VNC
→ Yes
```

VNC를 활성화한 후 다음 메시지가 나타났다.

```text
Created symlink '/etc/systemd/system/multi-user.target.wants/wayvnc.service'
→ '/usr/lib/systemd/system/wayvnc.service'
```

이를 통해 WayVNC 서비스가 활성화된 것을 확인하였다.

---

## 11. Raspberry Pi 재부팅

VNC 설정 적용을 위해 다음 명령어를 사용하였다.

```bash
sudo reboot
```

재부팅 과정에서 SSH 연결이 끊기면서 다음 메시지가 나타났다.

```text
client_loop: send disconnect: Connection reset
```

이는 Raspberry Pi가 재부팅되면서 SSH 연결이 종료된 정상적인 현상이다.

---

## 12. 재부팅 후 SSH 재접속

Raspberry Pi가 다시 부팅된 후 기존 IPv6 주소를 이용하여 다시 접속하였다.

```powershell
ssh -6 team5@2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

비밀번호 입력 후 정상적으로 접속되었다.

```text
team5@Team5:~ $
```

---

## 13. Raspberry Pi IP 주소 확인

Raspberry Pi에서 현재 IP 주소를 확인하기 위해 다음 명령어를 사용하였다.

```bash
hostname -I
```

결과:

```text
10.174.121.18 2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

현재 Raspberry Pi의 IP 주소는 다음과 같다.

### IPv4

```text
10.174.121.18
```

### IPv6

```text
2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

VNC 접속 시에는 IPv4 주소인

```text
10.174.121.18
```

을 사용할 예정이다.

---

## 14. SSH와 VNC 차이

### SSH

SSH는 Raspberry Pi의 터미널을 원격으로 조작하는 방식이다.

예:

```text
team5@Team5:~ $
```

SSH를 통해 다음과 같은 작업을 수행할 수 있다.

- 프로그램 실행
- 파일 관리
- 프로그램 설치
- Raspberry Pi 설정 변경
- 서버 관리

하지만 Raspberry Pi의 바탕화면은 보이지 않는다.

### VNC

VNC는 Raspberry Pi의 GUI 화면을 다른 장치에서 보는 방식이다.

VNC를 사용하면 다음 작업이 가능하다.

- Raspberry Pi 바탕화면 확인
- 마우스 조작
- 키보드 입력
- GUI 프로그램 실행
- 노트북 또는 태블릿을 모니터처럼 사용

---

## 15. 전체 연결 구조

```text
             휴대폰 핫스팟
             /          \
            /            \
       노트북          Raspberry Pi
                          |
                     Wi-Fi 연결
```

노트북과 Raspberry Pi를 같은 네트워크에 연결한 뒤 SSH와 VNC를 이용하여 Raspberry Pi를 원격으로 제어한다.

추후 태블릿도 같은 네트워크에 연결하여 VNC로 Raspberry Pi 화면을 사용할 예정이다.

---

## 16. TigerVNC Viewer 설치

Windows 노트북에서 Raspberry Pi의 GUI 화면을 보기 위해 TigerVNC Viewer를 사용하기로 하였다.

TigerVNC 공식 페이지에서 Windows 64비트용 프로그램을 다운로드하였다.

VNC Viewer에서 사용할 Raspberry Pi 주소는 다음과 같다.

```text
10.174.121.18
```

로그인 정보:

```text
Username : team5
Password : Raspberry Pi Imager에서 설정한 비밀번호
```

정상적으로 연결되면 Windows 노트북에서 Raspberry Pi의 바탕화면을 확인할 수 있다.

---

## 17. 이후 진행 예정

다음 단계에서는 다음 작업을 진행할 예정이다.

1. TigerVNC Viewer 설치 완료
2. 노트북에서 VNC 연결
3. Raspberry Pi GUI 화면 확인
4. 태블릿에 VNC Viewer 설치
5. 태블릿과 Raspberry Pi를 같은 네트워크에 연결
6. 태블릿에서 Raspberry Pi GUI 화면 확인
7. 태블릿을 Raspberry Pi의 원격 모니터처럼 사용

---

## 18. 오늘 사용한 주요 명령어

### 네트워크 연결 확인

```powershell
ping Team5.local
```

### IPv4 연결 확인

```powershell
ping -4 Team5.local
```

### Windows 네트워크 정보 확인

```powershell
ipconfig
```

### 네트워크 장치 확인

```powershell
arp -a
```

### IPv6 SSH 접속

```powershell
ssh -6 team5@2001:2d8:ef40:33d8:2ecf:67ff:fef3:dedf
```

### Raspberry Pi 파일 확인

```bash
ls
```

### Raspberry Pi 설정

```bash
sudo raspi-config
```

### Raspberry Pi 재부팅

```bash
sudo reboot
```

### Raspberry Pi IP 확인

```bash
hostname -I
```

---

## 19. 핵심 정리

이번 실습에서는 Raspberry Pi에 별도의 모니터를 연결하지 않고 네트워크를 이용하여 원격으로 제어하는 방법을 실습하였다.

처음에는 `raspberrypi.local`을 이용하여 접속을 시도하였지만 실제 호스트 이름이 `Team5`였기 때문에 정상적으로 연결되지 않았다.

이후 휴대폰 핫스팟을 이용하여 노트북과 Raspberry Pi를 같은 네트워크에 연결하였다.

`ping Team5.local`을 통해 Raspberry Pi가 정상적으로 네트워크에 연결된 것을 확인하였다.

IPv4 기반 `.local` 주소 확인에는 문제가 있었지만 IPv6 주소를 직접 이용하여 SSH 접속에 성공하였다.

```text
team5@Team5:~ $
```

SSH 접속 후 `raspi-config`를 이용하여 VNC 기능을 활성화하였으며 Raspberry Pi를 재부팅하였다.

재부팅 후 다음 명령어를 통해 Raspberry Pi의 IPv4 주소를 확인하였다.

```bash
hostname -I
```

확인된 IPv4 주소는 다음과 같다.

```text
10.174.121.18
```

현재 SSH 연결 및 VNC 서버 설정까지 완료되었으며, 다음 단계에서는 TigerVNC Viewer를 이용하여 노트북과 태블릿에서 Raspberry Pi의 GUI 화면을 확인할 예정이다.