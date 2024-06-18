# 인증서 비밀번호가 계속 틀리다고 나오는 이유

```
CertUtil: -importPFX 명령이 실패되었습니다.0x80070056 (WIN32: 86 ERROR_INVALID_PASSWORD) 
CertUtil: 지정된 네트워크 암호가 맞지 않습니다.
```

<figure><img src="../../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

특정 컴퓨터만 가면 위와 같은 오류가 발생 하는 경우가 있습니다.&#x20;

문제점은 openssl 최신 버전에서 구버전 윈도우를 지원하지 않기 때문입니다.  (윈 7,8  server 2016)

이 경우에 구버전 openssl로 (1버전대)로 다시 작성후 배포하면 됩니다.
