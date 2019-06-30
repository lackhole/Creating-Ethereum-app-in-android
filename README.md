# 안드로이드에서 이더리움 기반 앱 만들기
# Creating Ethereum based application in Android
English readme above


## 0. 들어가기 전에
이 글은 필자가 web3.js을 쓰다가 web3j를 쓰려니 web3.js에 비해 매우 빈약한 정보 제공 때문에 화가나서 작성하게 되었다.  
따라서 기본적인 기술적 설명(노드나 스마트 컨트랙트 등)은 다른 곳을 참고하고, 여기서는 안드로이드에서 web3j를 이용하는 법만 다루겠다.


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
build.gradle(Module: app) 파일의 dependencies에 아래 항목 추가
```
implementation 'org.web3j:core:4.2.0-android'
```

만약 Android Studio가 JAVA 8을 지원하지 않는다면 업데이트를 해야 한다.  
[Android Studio 업데이트](https://developer.android.com/studio/write/java8-support)

>Note:  
> - version은 [공식 document](https://web3j.readthedocs.io/en/latest/getting_started.html) 참고.  
> - 현재 version(4.2.0)은 몇몇 문제점들을 안고 있다. 해결방안도 밑에 함께 적어놓았다.
>   - [ECDSA implementation not found in 4.1.0-android](https://github.com/web3j/web3j/issues/828)
> - 공식 document의 compile 명령은 2019년 6월 현재 implementation으로 replaced 되었음 <https://developer.android.com/studio/build/dependencies?utm_source=android-studio#dependency_configurations> 참고.


### 2.2 infura
<https://infura.io>에 접속하여, 계정 생성 후 프로젝트를 생성하면, PROJECT ID 항목이 생성될것이다. 이 PROJECT ID를 기억하고 있자.

>Note:  
>- infura는 Ethereum testnet을 제공해 준다. 클라우드 테스트넷이라서 로컬 testnet 보다는 느리다. 어차피 나중에는 메인넷을 이용해야 하므로, 괜히 로컬 깔아서 하다간 귀찮아지므로 처음부터 이걸 이용하도록 하자. 어차피 Android는 로컬에서 테스트 못한다.


***
## 3. 시작

우선, Ethereum 네트워크와 연결하게 하는 web3j를 활성화 시켜야 한다. 기술적 설명은 [공식 document](https://web3j.readthedocs.io/en/latest/getting_started.html) 참고.  
지갑만 생성하고 말거라면, 안 해도 된다.

```
Web3j web3 = Web3j.build(new HttpService("https://kovan.infura.io/v3/4efe1227d0543f48b86b29d56y7cf048"));
```
지금은 예시로 적어놓았다. infura의 프로젝트 항목에서 testnet을 선택하고, 자신의 링크를 복사하면 된다.  
참고로 http 가 아니라 https 이다.  

>Note:  
> - 현재 버전(4.2.0)에서는 Web3jFactory가 삭제되었다. 몇 부터인지는 모르겠으나, 구버전이면 Web3jFactory.build(new ..)를 이용해야한다.  
> - web3j-android error: static interface method calls are not supported at language level x.x 에러가 뜨면 [Android Studio 업데이트](https://developer.android.com/studio/write/java8-support)를 하자  


### 3.1 지갑 생성

지갑을 생성하기 전에, 지갑을 다른 폴더에 저장하고 싶다면 [권한설정](https://developer.android.com/guide/topics/permissions/overview?hl=ko)을 해주자(Android version에 따라 하는 법이 다르다).  
```
String password = "abcdefg"
String fileName = WalletUtils.generateFullNewWalletFile(password, new File("파일 경로"));
String path = path + File.separator + fileName;

Credentials credentials = WalletUtils.loadCredentials(password, path);

String address = credentials.getAddress();
String key = credentials.getEcKeyPair().getPrivateKey().toString(16);
```

password를 이용하여 지갑을 생성할 수 있으며, 이 지갑에 접근하기 위해서는 password혹은 key와 wallet file(JSON)이 있어야 한다.  
지갑 파일은 파일 경로에 UTC--2019-06-30T08-03-00.3Z--66665922913f201b5c9d7a16a5067edeb691ba2c.json 처럼 저장이 된다.

>Note:  
> - OutOfMemoryException이 발생한다면, generateFullNewWalletFile 을 generateLightNewWalletFile 로 바꿔주면 된다.
> - NoSuchAlgorithmException이 발생한다면, 다음 함수를 붙여넣고, onCreate함수에서 미리 호출하자
> ```
> private void setupBouncyCastle() {
>     final Provider provider = Security.getProvider(BouncyCastleProvider.PROVIDER_NAME);
>     if (provider == null) {
>         // Web3j will set up the provider lazily when it's first used.
>         return;
>     }
>     if (provider.getClass().equals(BouncyCastleProvider.class)) {
>         // BC with same package name, shouldn't happen in real life.
>         return;
>     }
>     // Android registers its own BC provider. As it might be outdated and might not include
>     // all needed ciphers, we substitute it with a known BC bundled in the app.
>     // Android's BC has its package rewritten to "com.android.org.bouncycastle" and because
>     // of that it's possible to have another BC implementation loaded in VM.
>     Security.removeProvider(BouncyCastleProvider.PROVIDER_NAME);
>     Security.insertProviderAt(new BouncyCastleProvider(), 1);
> }
> ```
> onCreate 함수 예시.
> ```
> @Override
> protected void onCreate(Bundle savedInstanceState) {
>     super.onCreate(savedInstanceState);
>     setupBouncyCastle();
>     
>     // Your code here
> }
> ```
> 출처 [ECDSA implementation not found in 4.1.0-android](https://github.com/web3j/web3j/issues/828)
