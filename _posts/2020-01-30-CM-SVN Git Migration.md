---
layout: post
title:  "[Tech] SVN → Git Migration"
subtitle:   "[Tech] SVN → Git Migration"
categories: Tech
tags: CM
---

# SVN → Git Migration

도커로 svn 서버 실행

    docker run -d --name svn-server -p 80:80 -p 3690:3690 elleflorio/svn-server

신규 레파지토리 생성

    docker exec -it svn-server svnadmin create sample

또는 컨테이너에 접근하여 생성할 수 있습니다. 파일 타입은 fsfs와 bdb 두가지를 제공하지만 bdb는 deprecate되었습니다. 

    docekr exec -it svn-server sh 
    
    
    svnadmin create --fs-type fsfs /home/svn/<레파지토리명>

레파지토리 권한 설정

vi <레파지토리명>/conf/svnserve.conf

    # 인증받지않은 사용자 권한
    anon-access=none
    # 인증받은 사용자 권한
    auth-access=write
    # passwd 파일기반으로 사용자 관리
    password-db=passwd

신규 사용자 생성

    docker exec -t svn-server htpasswd -b /etc/subversion/passwd <username> <password>

또는 <레파지토리명>/conf/passwd 경로 직접 사용자명과 비밀번호를 파일에 작성해야합니다.

    <사용자 아이디>=<사용자 비밀번호>

trunk, branches, tags 디렉토리 구성하기

    export SVN_EDITOR=vi
    svn mkdir svn://localhost/<레파지토리명>/trunk --username <사용자 아이디> --password <사용자 비밀번호>
    svn mkdir svn://localhost/<레파지토리명>/branches --username <사용자 아이디> --password <사용자 비밀번호>
    svn mkdir svn://localhost/<레파지토리명>/tags --username <사용자 아이디> --password <사용자 비밀번호>

클라이언트에서 http으로 접근이 필요하여 권한을 부여합니다.

    chown -R svn:www-data <레파지토리명>

### SVN 사용자정보

svn 사용자정보를 생성해야 합니다. 이번 블로그에서는 TortoiseSVN을 사용하겠습니다. svn의 레파지토리를 로컬에 체크아웃의 자세한 설명은 생략하도록 하겠습니다. 체크아웃 받은 폴더에서 오른쪽 클릭하여 Show log를 선택합니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled.png)

선택하면 아래 이미지처럼 화면에서 Statistics를 선택합니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%201.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%201.png)

Graph Type을 Commits by author를 선택합니다. 그러면 오른쪽에 컴밋했던 사용자명이 보입니다. 

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%202.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%202.png)

SVN에서 컴밋했던 사용자명을 users.txt 파일을 규칙에 맞게 작성합니다.

작성규칙: SVN 사용자명= Github 사용자명 <Github 사용자이메일>

    jwk = joonwoo8888 <dev.joonwoo@gmail.com>
    

SVN 레파지토리에서 로컬 Git으로 복사합니다. 옵션정보는 대소문자를 구분합니다. 정확하게 입력해야합니다.

git svn clone <SVN URL> --no-metadata -A <users.txt 경로> -T <trunk명> -b <branches명> -t <tags명> <로컬git경로>

    git svn clone <SVN URL> --no-metadata -A users.txt -T trunk -b branches -t tags ./sample-git

정상적으로 복사되었으면 source tree를 이용하여 확인합니다. 원격 하위에 Subversion이 생성되었고 Branches, Tags가 보입니다. 그리고 히스토리도 정상적으로 보입니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%203.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%203.png)

원격에 표시되는 branches, tags는 svn에 연결이 되어 있습니다. 앞으로 git으로 사용하기 위해서는 svn의 branches, tags의 history 정보와 새로 만든 git branches, tags의 history가 연결되어야 합니다.

git branches 생성 하겠습니다.

    for branch in `git branch -r | grep -v tags`; do
    git branch -a -m"Converting SVN Branches" $branch $branch
    done

git tags 생성하겠습니다.

    for tag in git branch -r | grep "tags/" | sed 's/ tags\\///'| cut -d'/' -f 3; do
    git tag -a -m"Converting SVN tags" $tag origin/tags/$tag
    done

source tree를 이용하여 확인해보겠습니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%204.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%204.png)

현재 상태에서 github, gitlab등으로 푸시하여 저장할 수 있습니다.

테스트로 gitlab에 푸시하여 브렌치정보를 확인합니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%205.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%205.png)

태그도 정상적으로 저장되었는지 확인합니다.

![/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%206.png](/assets/img/2020-01-30-CM-SVN%20Git%20Migration/Untitled%206.png)

정리

SVN은 사용하기 쉽고 비공개적으로 사용하는 장점으로 많이 사용되어 왔습니다. 하지만 다양한 기술을 사용할 수 없고 디스크용량의 압박을 느끼게 되어 git으로 변경하게 되었습니다. 사용방법이 편리하고 git에서 제공하는 기능으로 편리하게 마이그레이션을 완료할 수 있었습니다.