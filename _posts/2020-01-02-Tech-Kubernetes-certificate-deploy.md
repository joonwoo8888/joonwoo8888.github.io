# Kubernetes 인증서 만들기

쿠버네티스 웹UI 대시보드를 외부에서 접근하기위해 인증서를 만들기로 함.

kubectl proxy 사용하기엔 임시적으로 사용하는 용도이고, NodePort는 현재 막힌 상태인거같다.. 남은건 인증서로 접근하는 안전하는 방법만 남은거 같다.

아래 순서대로 따라 하면 됨.

### 1. CRT 파일 생성

    grep 'client-certificate-data' ~/.kube/config | head -n 1 | awk '{print $2}' | base64 -d >> kubecfg.crt

### 2. KEY 파일 생성

    grep 'client-key-data' ~/.kube/config | head -n 1 | awk '{print $2}' | base64 -d >> kubecfg.key

### 3. p12 파일 생성

    openssl pkcs12 -export -clcerts -inkey kubecfg.key -in kubecfg.crt -out kubecfg.p12 -name "kubernetes-admin"

비밀번호를 물어보는데 어렵게 입력하고 기억하고 있어야한다. 나중에 필요함..

### 4. ca.crt, p12 파일 사용할 PC로 복사해둔다.

ca.crt  파일위치 >> /etc/kubernetes/pki/ca.crt`

### 5. Windows 인증서 등록방법

관리자 권한으로 커맨드창을 실행하여 아래 명령어를 입력하면 됨

나는 서버에서 받은 CET, P12 파일을 D:\에 복사해뒀음.

    certutil.exe -addstore "Root" D:\ca.crt
    
    certutil.exe -p <3번에서 입력한 비밀번호> -user -importPFX D:\kubecfg.p12

입력하면 아래처럼 보안경고가 뜨면서 예를 누르면 된다.

![/assets/img/2020-01-02-Tech-Kubernetes-certificate-deploy/Untitled.png](/assets/img/2020-01-02-Tech-Kubernetes-certificate-deploy/Untitled.png)

### 6. 테스트

이제 인증서 등록은 전부 마무리 된거다. 사용할 브라우저를 완전히 종료후 다시 접속해보면 접속이 될거다.

끝.