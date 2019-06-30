# Creating Ethereum based application in Android
English readme above

## &nbsp;&nbsp;1. 요구사항
### &nbsp;&nbsp;&nbsp;&nbsp;1.1 운영체제
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Windows
### &nbsp;&nbsp;&nbsp;&nbsp;1.2 프로그램
  
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Android Studio : <https://developer.android.com/studio>   
   ```
      스마트 컨트랙트를 이용한다면,  
      solc : https://github.com/ethereum/solidity/releases
      에서 .zip파일 다운받은 후 압축을 푼다.
   ```
### &nbsp;&nbsp;&nbsp;&nbsp;1.3 클라이언트

   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;infura : <https://infura.io/>


***
## 2. 환경 설정
### 2.1 Web3j  
build.gradle(Module: app) > dependencies에 아래 항목 추가
```
implementation 'org.web3j:core:4.2.0-android
```

>Note:  
> - version은 <https://web3j.readthedocs.io/en/latest/getting_started.html> 참고.  
> - compile 명령은 2019년 6월 현재 implementation으로 replaced 되었음 <https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations> 참고.

### 2.2 infura
<https://infura.io>에 접속하여, 계정 생성 후 프로젝트를 생성하면, PROJECT ID 항목이 생성될것이다. 이 PROJECT ID를 기억하고 있자.

>Note:  
>- infura는 Ethereum testnet을 제공해 준다. 클라우드 테스트넷이라서 로컬 testnet 보다는 느리다. 어차피 나중에는 메인넷을 이용해야 하므로, 괜히 로컬 깔아서 하다간 귀찮아지므로 처음부터 이걸 이용하도록 하자. 어차피 Android는 로컬에서 테스트 못한다.
