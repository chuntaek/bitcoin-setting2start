# Bitcoin Setting & Start
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
bitcoin-daemon(이하 bitcoind)를 이용하여 Bitcoin core를 실제 기동해 본다.

비트코인 테스트 모드
* Testnet: 인터넷상에서 동작하는 테스트 네트워크. 테스트용 BTC를 사용하지만 처음 시작할 때 Testnet에 있는 모든 블럭을 동기화해야 한다.
* Regtest: 로컬PC 내에서 동작하는 테스트 네트워크. 개인PC 내에서만 계정을 만들거나 채굴할 수 있고 블럭체인 초기화도 쉽게 때문에 테스트로 사용하기에 적합하다.

bitcoind 기동

    ./bitcoind -regtest -daemon
    
처음 bitcoind를 기동하면 채굴자용 계정이 default로 생성된다.    

결과:
    
    Bitcoin server starting

bitcoind를 실행한 터미널은 실행 상태가 되므로 비트코인 조작을 위해서는 다른 터미널에서 bitcoin-cli를 사용한다.

# Bitcoin Core 조작
### 블록 생성
송금을 하기 위해 BTC를 생성한다. 비트코인을 블럭을 생성한 보상으로 BTC를 받는다.

비트코인은 100블록이 경과하지 않으면 송금 등에 이용할 수 없기 때문에 101개의 블록을 만든다.

    ./bitcoin-cli -regtest generate 101

결과:

    [
      "39c2a6f947f6af27ea085eb18963f4975903e4e1ea3e1deef6c492b7e935a6a9",
      "7078d9e567d2e07a98da39202fc99bb3f4af305b45871bc63619eb9005c4c2be",
      "2d3028dc124499de8ecec1690e21fefcaa53cf6cefa3253493beb48e57477ce8",
        ..................
    ]

위와 같이 101행의 영숫자(해시값)이 표시된다. 1행은 1블럭을 나타낸다.
비트코인의 블럭은 32바이트의 16진수로 표현된다.

### 블록 수 확인
현재 블록 수를 확인한다.

    ./bitcoin-cli -regtest getblockcount
    
결과:
        
    101

위에서 101개의 블럭을 만들었으므로 결과는 101이다.

정상적으로 블럭이 만들어진 것을 확인할 수 있다.

### 계좌 생성
계좌는 은행 계좌와 동일한 역할을 한다. 계좌 생성 후 BTC 거래가 가능해진다. 계좌는 n개 이상 생성이 가능하다.

    ./bitcoin-cli -regtest getnewaddress testuser1

결과

    2NAKG7Uiq3EYxcHBMYWdXvBGak1FVGGNQJe

송금을 위해서 생성된 계좌는 잘 기억하자.

### 잔고 확인
현재 잔고를 확인한다.

    ./bitcoin-cli -regtest getbalance

결과

    50.00000000

처음에 블록을 생성한 보상으로 50 BTC를 받았기 때문에 계좌에 50 BTC가 존재한다. 인수 없이 이 명령을 실행하면 채굴자가 소유한 BTC가 표시된다.

이번엔 특정 계좌의 잔고를 확인해 본다.

    ./bitcoin-cli -regtest getbalance testuser1
    
결과

    0.00000000

방금 생성한 계정이기 때문에 BTC는 0이다.

# 송금
송금하기 위해서는 송금할 계좌, 금액을 지정해 트랜잭션을 발행한다. 채굴자가 보우하고 있는 BTC 중 10BTC를 위에서 생성한 계좌에 송금해 보자.

    ./bitcoin-cli -regtest sendtoaddress 2NAKG7Uiq3EYxcHBMYWdXvBGak1FVGGNQJe 10

결과

    78b5da77bc09cba86f6be2a711f722755301a82440f2f684b653f88cee2b2e85

트랜잭션을 확인하기 위한 식별번호(txid)가 표시된다.

##### 트랜잭션 확인

    ./bitcoin-cli -regtest listunspent

결과

    [
    ]

빈 값이 표시된다. listunspent는 확정된 트랜잭션을 확인하는 명령이다.

파라미터로 0을 추가해 실행하면 미확정 트랜잭션이 표시된다.

##### 미확정 트랜잭션 확인

    ./bitcoin-cli -regtest listunspent 0

결과

    [
      {
        "txid": "78b5da77bc09cba86f6be2a711f722755301a82440f2f684b653f88cee2b2e85",
        "vout": 0,
        "address": "2NAKG7Uiq3EYxcHBMYWdXvBGak1FVGGNQJe",
        "account": "testuser1",
        "redeemScript": "00142e72390322db36656beb85f02ce9efe72c958ec0",
        "scriptPubKey": "a914bb3d9342c905e8bf31197806902909f61621648787",
        "amount": 10.00000000,
        "confirmations": 0,
        "spendable": true,
        "solvable": true,
        "safe": true
      },
      {
        "txid": "78b5da77bc09cba86f6be2a711f722755301a82440f2f684b653f88cee2b2e85",
        "vout": 1,
        "address": "2N3z39X96P4BnWN1mQ1aty559bA8KVUdsWi",
        "redeemScript": "00146b7c56f85be312d806661786e566f336b12f2e7c",
        "scriptPubKey": "a91475ca18445250be72975c5b85d054203c60c870f787",
        "amount": 39.99996240,
        "confirmations": 0,
        "spendable": true,
        "solvable": true,
        "safe": true
      }
    ]

앞에서 확인한 트랜잭션 식별번호(txid)와 동일한 값을 확인할 수 있다.
10BTC를 송금해 40BTC가 돼야 하지만 실제로는 채굴자에게 송금 수수료가 지불됐기 때문에 그만큼 차감돼 40BTC가 되지 않는다.

송금자의 잔고를 확인해 보자

    ./bitcoin-cli -regtest getbalance

결과

    49.99996240

결과를 보면 수수료만 차감돼 있다. 이처럼 블록체인에서는 트랜잭션을 발행한 것만으로는 송금이 확정되지 않는다. 이후 실행하는 채굴에 의해 송금이 확정된다.

# 채굴
미확정 트랜잭션을 확정하기 위해 채굴을 실행한다.

채굴에 의해 트랜잭션이 블록에 저장되고, 송금이 확정된다.

### 블록 생성(채굴)

    ./bitcoin-cli -regtest generate 1
    
결과

    [
      "1e30cc6e447919db0a3d0a263cbf0f4f6422a5eabf5ae3b4781d92f6b3588c05"
    ]

# 송금 확인
블록이 새로 생성됐으니(채굴 완료) 송금이 확정됐는지 확인해본다.

    ./bitcoin-cli -regtest listunspent
    
결과

    [
      {
        "txid": "d00005e3a15ff9729d02c8ccc60077a4a5f8e12874c94508999f8b6e26daf606",
        "vout": 0,
        "address": "mvDNBHYr89vYLVQk9tkYTYLaajfWQSjSX3",
        "scriptPubKey": "21034a81585ab8e5c9979d11e3467d37b59046636e29ad5b35ea2d2432a4584de720ac",
        "amount": 50.00000000,
        "confirmations": 101,
        "spendable": true,
        "solvable": true,
        "safe": true
      },
      {
        "txid": "78b5da77bc09cba86f6be2a711f722755301a82440f2f684b653f88cee2b2e85",
        "vout": 0,
        "address": "2NAKG7Uiq3EYxcHBMYWdXvBGak1FVGGNQJe",
        "account": "testuser1",
        "redeemScript": "00142e72390322db36656beb85f02ce9efe72c958ec0",
        "scriptPubKey": "a914bb3d9342c905e8bf31197806902909f61621648787",
        "amount": 10.00000000,
        "confirmations": 1,
        "spendable": true,
        "solvable": true,
        "safe": true
      },
      {
        "txid": "78b5da77bc09cba86f6be2a711f722755301a82440f2f684b653f88cee2b2e85",
        "vout": 1,
        "address": "2N3z39X96P4BnWN1mQ1aty559bA8KVUdsWi",
        "redeemScript": "00146b7c56f85be312d806661786e566f336b12f2e7c",
        "scriptPubKey": "a91475ca18445250be72975c5b85d054203c60c870f787",
        "amount": 39.99996240,
        "confirmations": 1,
        "spendable": true,
        "solvable": true,
        "safe": true
      }
    ]

확정된 것이 없을 때는 결과가 공백으로 나왔지만, 미확정 트랜잭션을 확정했기 때문에 결과가 표시된다.

앞에서 생성한 계좌(testuser1)의 잔고를 확인해 본다.

    ./bitcoin-cli -regtest getbalance testuser1

결과

    10.00000000
        
