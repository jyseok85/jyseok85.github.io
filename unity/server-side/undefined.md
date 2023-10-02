# 자체 서명 인증서 만들기

2023.10.01 밤 11시 추석연휴 갑작스럽게 인쇄서버가 안된다는 연락이 왔습니다.&#x20;

서버모듈은 몇달내에 업데이트 된 적이 없었기에 다른 환경 문제라고 생각했는데 증상을 들어보니 그런 단순한 문제가 아니였습니다.&#x20;

* 모든 PC에서 발생.
* 윈도우 업데이트가 모든 PC동시에 발생할 가능성 없음.
* 브라우저 업데이트가 모든 PC 동시에 발생할 가능성 없음.

브라우저에서 확인해보니 ERR\_CERT\_DATE\_INVALID 오류가 출력되고 있었습니다.&#x20;

위 문제는 인증서의 유효기간이 만료되었기에 나오는 문제였습니다. 분명히 10년짜리로 만들었는데 왜 발생하나 했더니, 자체인증기관은 10년짜리로 되어 있는데 실제서버 인증서는 5년짜리로 되어 있었습니다.



얼마나 급하면 연휴 일요일 밤에 연락이 오는지..

다음날 출근해서 작업겸 문서 정리를 위해 작성했습니다.&#x20;



### **인증서란?**

인증서는 공개적으로 신뢰할 수 있는 인증기관(CA)에서 서명한 것을 사용합니다.  또한 그 인증서의 유효기간은 2015년 이후 1년으로 축소되어 매년 갱신해야 한다고 합니다. 그래야 서버측에서 접속하는 대상이 안전하다고 판단하는 거겠죠?&#x20;

그러나 굳이 인증이 필요없다면? 모든 서버가 외부와 단절되었더단가, 로컬PC와 통신할때만 사용한다던가, 할때는 굳이 비싼 돈을 주면서 서명된 인증서를 사용할 필요가 없습니다.&#x20;



### 자체서명 인증서란?

실제 서버에 적용할 인증서를 자체적으로 만든 인증기관(CA)로 서명한 것을 말합니다. 당연히 인증된 기관이 아니기 때문에 외부(다른 업체)와 통신은 불가능합니다.\
(물론 이것도 허용해주면 가능합니다만 보안에 구멍이 뚤리는걸 해주지 않겠죠. ) \
(정확하게는 오류는 발생하는데 서버측에서 오류 무시하고 통신되도록 예외처리 하는 것 입니다)

보통 같은 업체내의 서비스를 위해서 사용합니다. 브라우저에서는 안전하지 않은 것으로 간주하고 표시됩니다.&#x20;



기본 배경은 이렇게 되지만, 브라우저에서 엄격하게 관리하기 시작한 것은 얼마 되지 않았습니다.&#x20;

(구)익스플로러의 경우는 이런 보안 싸그리 무시하고, \
크로미움 기반의 (엣지, 크롬 등) 은 80버전이후부터 제대로 체크하기 시작했습니다. \
(이전까지는 콘솔창에 무언가오류가 표시되더라도 동작되는케이스가 많았습니다)



#### 작업내용

* 스스로 서명한 인증기관 인증서(ROOT CA = Self Signed Certificate(SSC) 만들기
* 서버에 적용할 SSL 인증서 만들기

#### 준비물

윈도우용 최신OpenSSL 다운로드\
(원래는 [https://github.com/openssl/openssl](https://github.com/openssl/openssl) 소스를 빌드해서 사용합니다.)

{% embed url="https://slproweb.com/products/Win32OpenSSL.html" %}



<details>

<summary>ROOT CA 생성</summary>

1. 개인 키 생성(key)

```
openssl genrsa -des3 -out root_ca.key 2048
```

2. 메모장으로  인증요청서 환경파일 생성(cnf)\
   이 파일이 없으면 인증요청서 생성시 전부 타이핑해서 입력하게 됩니다. \
   편의성을 위해서 만들어 주는게 좋습니다.&#x20;

{% code title="root_ca_openssl.cnf" %}
```
[ req ]
default_bits            = 2048
default_md              = sha1
default_keyfile         = root_ca.key
distinguished_name      = req_distinguished_name
extensions             = v3_ca
req_extensions = v3_ca

[ v3_ca ]
basicConstraints       = critical, CA:TRUE, pathlen:0
subjectKeyIdentifier   = hash
##authorityKeyIdentifier = keyid:always, issuer:always
keyUsage               = keyCertSign, cRLSign
nsCertType             = sslCA, emailCA, objCA

[req_distinguished_name ]
countryName                     = KR
countryName_default             = KR
countryName_min                 = 2
countryName_max                 = 2

# 회사명 입력
organizationName              = Company
organizationName_default      = Company

# 부서 입력
#organizationalUnitName          = 부서명
#organizationalUnitName_default  = 부서명  

# SSL 서비스할 domain 명 입력
commonName                     = 도메인
commonName_default             = 도메인
commonName_max                  = 64
```
{% endcode %}

3. 인증요청서 (CSR) 파일생성

```
openssl req -new -key root_ca.key -out root_ca.csr -config root_ca_openssl.cnf
```

4. 자체 서명 인증서 생성(CRT)

```
openssl x509 -req -days 3650 -extensions v3_ca -set_serial 1 -in root_ca.csr
 -signkey root_ca.key 
 -out root_ca.crt 
 -extfile root_ca_openssl.cnf -sha256
```

</details>



<details>

<summary>서버 인증서 생성</summary>

1. 개인키 생성(key)

```
openssl genrsa -des3 -out server.key 2048
```

2. 인증요청서 환경파일 생성(cnf)\
   이 파일이 없으면 인증요청서 생성시 전부 타이핑해서 입력하게 됩니다. \
   편의성을 위해서 만들어 주는게 좋습니다.&#x20;

{% code title="server_openssl.cnf" %}
```
[ req ]
default_bits            = 2048
default_md              = sha1
default_keyfile         = root_ca.key
distinguished_name      = req_distinguished_name
extensions             = v3_user
## 인증서 요청시에도 extension 이 들어가면 authorityKeyIdentifier 를 찾지 못해 에러가 나므로 막아둔다.
## req_extensions = v3_user
  
[ v3_user ]
# Extensions to add to a certificate request
basicConstraints = CA:FALSE
authorityKeyIdentifier = keyid,issuer
subjectKeyIdentifier = hash
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
## SSL 용 확장키 필드
extendedKeyUsage = serverAuth,clientAuth
subjectAltName          = @alt_names

[ alt_names]
## Subject AltName의 DNSName field에 SSL Host 의 도메인 이름을 적어준다.
## 멀티 도메인일 경우 *.lesstif.com 처럼 쓸 수 있다.
IP.1   = 127.0.0.1
#DNS.2   = lesstif.com
#DNS.3   = *.lesstif.com
  
[req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = KR
countryName_min                 = 2
countryName_max                 = 2
  
# 회사명 입력
organizationName              = Organization Name (eg, company)
organizationName_default      = 회사명
   
# 부서 입력
organizationalUnitName          = Organizational Unit Name (eg, section)
organizationalUnitName_default  = 부서명
   
# SSL 서비스할 domain 명 입력
commonName                      = Common Name (eg, your name or your server's hostname)
commonName_default             = 127.0.0.1
commonName_max                  = 64
```
{% endcode %}

3. 인증요청서 (CSR) 파일생성

<pre><code><strong>openssl req -new -key server.key -out server.csr -config server_openssl.cnf
</strong></code></pre>

4. 인증요청서파일(CSR)을 자체서명된인증기관(ROOT CA) 캐인키로 서명

```
openssl x509 -req -days 3650 -extensions v3_user -in server.csr 
-CA root_ca.crt -CAcreateserial 
-CAkey root_ca.key 
-out server.crt -extfile server_openssl.cnf -sha256 
```

</details>



### 윈도우에서 인증서를 사용하기 위한 설정

1. 윈도우에서 사용하기 위한 PFX 파일 변환

```
openssl pkcs12 -export -in server.crt -inkey server.key -out server.pfx
```

2. 컴퓨터 인증서에 지금 생성한 인증서 등록

```
certutil -f -p 암호 -importpfx server.pfx NoRoot
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>(인증서-로컬컴퓨터-개인용-인증서에 생성됩니다.)</p></figcaption></figure>

3. Root CA 인증서 등록

```
certutil -addstore -f ROOT root_ca.crt
```

4. 웹서버 프로그램 SSL 인증서 등록

```
netsh http add sslcert ipport=127.0.0.1:8888 certhash=인증서지문 appid={"guid"}
```

* appid의 경우 어플리케이션을 식별하는 GUID 입니다. 임의의 값을 입력 가능합니다.&#x20;
* 인증서 지문의 경우 server.crt 파일을실행 후 자세히 탭 맨 아래에 있습니다.&#x20;
