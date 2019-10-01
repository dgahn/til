# Netlify로 웹페이지 배포하기

**Netlify**는 무료로 정적 웹페이지를 배포해주는 사이트이다. 

오늘 할 것은 **Netlify**을 이용해서 **Github**에 올린 **VueJs** 프로젝트를 자동으로 배포하는 과정을 설명할 것이다.

## 주의할 점

다 작성하고 알았는데 **Netlify**를 **Nelify**로 작성한 부분이 있다. 오타.. 치명적..

## 1. VueJS 프로젝트 생성하기

먼저, **Github**에 **VueJS** 프로젝트를 올리자.

![](https://imgur.com/La8pjWH.png)

<br>

## 2. Netlify에 프로젝트 배포하기

**Netlify**에 가입을 하고 로그인을 하면 아래와 같은 화면이 나온다. **New site from Git**을 선택한다.

![](https://imgur.com/3LGuqjp.png)

나는 이미 배포한 사이트가 있어서 나오는데 신경 쓰지 않아도 된다.

<br>

코드 저장소를 선택한다. **Github**을 사용했으므로 **Github**를 선택한다.

![](https://imgur.com/1FWVl40.png)

<br>

**Github**에 **Netlify**에서 코드를 가져갈 수 있도록 인증을 해줘야한다. 로그인을 진행하면 아래의 화면이 나오는데 모든 저장소를 허가할 수 있고 특정 저장소에 대해서만 허가할 수도 있다. 원하는 저장소에 대해서 권한을 허가해주고 **Save** 버튼을 선택한다.

![](https://imgur.com/ucv50rG.png)

<br>

다시 **Netlify** 사이트로 돌아오면 배포할 사이트의 저장소를 선택할 수 있다. 자신이 배포할 사이트의 저장소를 선택한다.

![](https://imgur.com/jqqVSYT.png)

<br>

기본적인 설정 몇가지가 필요하다. 아래의 설명에 따라서 설정을 진행한다.

![](https://imgur.com/PgJi13T.png)

- **Build command** : 프로젝트의 빌드 커맨드를 작성한다. 예를 들어, 내가 올린 프로젝트 같은 경우 **npm run build**로 빌드 커맨드를 작성해놨다. 없는 경우 작성 안해도 된다.
- **Public directory** : 빌드된 정적파일이 존재하는 디렉터리를 작성해준다. 내가 올린 프로젝트 같은 경우 **dist** 디렉터리에 정적파일이 생기기 때문에 **dist/** 라고 작성했다. 만약, **build** 디렉터리이면 **build/** 라고 작성하면 된다. 참고로, 아무 것도 작성하지 않는 경우 **/** 를 기준으로 정적파일을 찾아준다.

위 두가지 옵션을 작성하면 **Deploy site** 버튼을 선택한다.

<br>

배포가 시작하면 아래와 같이 화면으로 진입한다. 현재 배포하고 있는 상황의 로그를 보고 싶다면 **Proudction deploys** 목록 중 최신 항목을 선택하면 된다.

![](https://imgur.com/YAgmyb4.png)

<br>

그러면 아래와 같이 **Deploy log**를 확인할 수 있다.

![](https://imgur.com/xGCqwPq.png)

<br>

배포가 완료되면 아래의 화면과 같이 위쪽에는 사이트의 **미리보기 화면**이 나오고 **Proudction deploy** 항목에 **Published**라고 변경된다. 사이트 상단의 웹 주소를 누르면 내가 배포한 사이트의 화면이 나온다.

![](https://imgur.com/V0n69Dj.png)

<br>

## 3. Netlify 도메인으로 도메인 변경하기

처음 사이트를 배포하고 나면 도메인이 굉장히 길게 나올 것이다. 자신이 알아볼 수 있도록 도메인으로 변경할 수 있다. 아래의 **Domain settings**를 선택한다.

![](https://imgur.com/Ckafr4f.png)

<br>

내 사이트 도메인 옆에 **"…"** 를 선택해서 **Edit site name**이 나오도록 해서 선택 버튼을 선택한다.

![](https://imgur.com/2sEhU6y.png)

<br>

**Netlify**가 주는 도메인은 **xxx.netlify.com**으로 주어진다. 그 중 앞부분을 수정할 수 있다. 자신이 원하는 도메인으로 변경한다.

![](https://imgur.com/ob0bF2X.png)

<br>

도메인이 잘 변경됬는지 확인한다.

![](https://imgur.com/vpfUNJ8.png)

## 4. 자기 도메인으로 도메인 변경하기

자신이 가진 도메인이 있는 경우에는 자신의 도메인을 사용할 수 있다. 나는 도메인 구매는 [Porkbun](https://porkbun.com/)에서 하였고 도메인 관리 [CloudFlare](https://www.cloudflare.com/)에서 하고 있다.

먼저, 도메인을 구입한다. 도메인을 구입하는 과정은 인터넷에 찾아보자. 

<br>

## 4.1 **도메인 네임 서버**를 설정하기

도메인 구입이 완료되면 **도메인 네임서버**를 설정해야한다. **Netlify**에서는 **Netlify**의 **도메인 네임서버** 사용하라고 권유하지만 꼭 그럴 필요는 없는다. **Netlify**에서 사용할 도메인만 **도메인 네임서버**에서 설정하면 된다.

**Porkbun**에서 내가 구입한 도메인의 **AUTHORITATIVE NAMESERVERS**를 수정한다.

![](https://imgur.com/5hLzxX9.png)

**edit** 버튼을 통해서 **도메인 네임서버**의 주소를 변경하자. 나는 **Cloudflare**로 변경하였다.

<br>

## 4.2 **도메인 네임 서버**에 도메인 추가하기

**Cloudflare**에서 아래의 예시와 같이 **도메인**을 추가해준다.

![](https://imgur.com/ZulKdtT.png)

- **Type** : CNAME을 선택하면 도메인 연결을 도메인으로 해준다.
- **Name** : 구입한 도메인에 앞부분을 선택한다. 예를 들어, 내가 설정한 도메인은 **dgahn-netlify-test.dgahn.me**인데 여기서 **dgahn-netlify-test**를 결정한다.
- **Target** : 도메인을 어떤 도메인에 연결시킬지를 결정한다. **Netlify**에서 준 도메인을 연결시키면 된다.
- **Proxy status** : Proxy 설정은 하지 않기 때문에 DNS only를 선택한다.

위 과정을 마치면 **Save** 버튼을 선택한다.

<br>

## 4.3 Netlify에서 custom domain 설정하기


다시 **Netlify**로 돌아와서 **Set up a custom domain**을 선택한다.

![](https://imgur.com/OwcWsVn.png)

<br>

전 단계에서 추가한 도메인을 적어주고 **Verify**를 선택한다.

![](https://imgur.com/0H8ZdFf.png)

<br>

자신의 도메인이 맞는지 한번 확인하는데 내 도메인이 맞으니까. **Yes, add domain**을 선택한다.
![](https://imgur.com/AAcxyox.png)

<br>

도메인 설정이 올바르면 아래와 같이 **Netlify DNS**라고 뜬다. 시간이 좀 걸릴 수도 있으니 5분정도 기다리고 다시 확인해보는 것도 좋다.

![](https://imgur.com/HZQwtPu.png)

<br>

## 4.4 HTTPS 설정하기

HTTP 설정하는 방법은 매우 간단하다. 도메인 설정 페이지 맨 아래에 가면 아래와 같은 옵션이 있는데 **Verify DNS configuration**을 선택한다. 도메인 설정을 잘못한 경우 이 과정에서 에러가 생길 수 있는데 에러가 생겼다면 다음날 설정하는 것을 추천한다.

![](https://imgur.com/DHaqVCX.png)

<br>

설정하는데 문제가 없으면 아래와 같은 화면이 나온다. 인증서를 받는데 시간이 걸리기 때문에 완료된 것은 아니다.

![](https://imgur.com/FhZQLDV.png)

<br>

인증서 발급이 완료되서 HTTPS 설정이 완료되면 아래와 같은 화면이 나온다.

![](https://imgur.com/m6q1CpU.png)




