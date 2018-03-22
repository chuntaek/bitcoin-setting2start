# Bitcoin-setting2start
Bincoin core 설치부터 기동까지
* [Mac OS X Build Instructions and Notes](https://github.com/bitcoin/bitcoin/blob/master/doc/build-osx.md)

## 사전 준비
OS X command line tools 설치:

    xcode-select --install

팝업이 표시되면, `install` 클릭한다.

그리고 [Homebrew](https://homebrew.sh) 설치한다.

## 의존성
brew 명령어를 통해서 의존성 라이브러리를 설치한다.
    
    
    brew install automake berkeley-db4 libtool boost miniupnpc openssl pkg-config protobuf python qt libevent

전체 개요는 [dependencies.md](https://github.com/bitcoin/bitcoin/blob/master/doc/dependencies.md) 참조

`make deploy` 사용해 디스크 이미지를 만들려면 RSVG가 필요하다. brew 명령어를 통해 라이브러리를 설치한다.

    brew install librsvg
    
## Bitcoin core 설치

1. Bitcoin core 소스를 깃허브에서 내려받고 `bitcoin` 디렉토리로 이동한다.

        git clone https://github.com/bitcoin/bitcoin
        cd bitcoin

2. Bitcoin core 빌드

아래와 같이 Bitcoin 바이너리를 빌드한다.
        
    ./autogen.sh
    ./configure
    make

* GUI를 비활성화하여 빌드하고자 할 경우, configure에 `--without-gui` 옵션을 전달한다.

### 설치

    sudo make install
    
이로써 Bitcoin core 설치가 끝났습니다.

# 테스트넷에서 기동
