---
layout: post
title: "[Authentication] 인증방식"
date: 2020-08-15 14:00:00
categories: Authentication
permalink: /Authentication/auth
---



# 인증방식

> Cookie / Session
>
> Session Based Auth / Token Based Auth
>
> JWT

1. Cookie / Session

   - Cookie 

     - 쿠키는 **클라이언트(브라우저) 로컬에 저장**되는 키와 값이 들어있는 데이터 파일이다.

     - 사용자 인증이 유효한 시간을 명시할 수 있으며, 유효 시간이 정해지면 브라우저가 종료되어 인증이 유지된다.

     - Response Header에 Set-Cookie 속성을 사용하면 클라이언트에 쿠키를 만들 수 있다.

     - 쿠키는 사용자가 따로 요청하지 않아도 브라우저가 Request시에 Request Header에 넣어 자동으로 서버에 전송한다.

     - 사용자의 편의를 위하되, 지워지거나 조작되거나 해킹되더라도 큰 일이 없는 민감하지 않은 정보들을 브라우저에 저장하는데 사용한다.

     - 사용예시) 

       로그인 시 아이디 / 비민번호 자동완성

       공지사항 - 오늘 하루 안보기

       쇼핑몰의 장바구니

   - Session

     - 쿠키를 기반하고 있으나 사용자의 정보파일을 브라우저가 아닌, **서버측에서 저장/관리** 한다.

     - 서버에서는 클라이언트를 구분하기 위한 기한이 짧은 임시 키인 **세션ID**를 브라우저로 보내서 쿠키로 저장한다.

     -  웹 브라우저가 서버에 접속해서 브라우저를 종료할 때까지 인증 상태를 유지한다.

     - 클라이언트가 서비스를 사용할때마다 쿠키에 이 키(세션 ID)를 전송하면 서버측에서는 확인 후 가공된 데이터를 뿌려준다.

     - 사용자에 대한 정보를 서버측 DB혹은 메모리에 두기 때문에 쿠키보단 보안에 좋으나, 사용자가 많아질수록 **서버 부하**가 심해질 수 있다. 

     - 사용예시)

       서비스 제공자가 직접관리해야 할 정보들(민감한 정보들)

2. Session Based Auth / Token Based Auth

   - **Session Based Auth**

     ![session01](..\img\session01.JPG)

     - Client가 접속할때, 서버측에서는 Session ID를 생성해 쿠키에 넣어 응답해준다. 클라이언트에서 재 요청시  쿠키를 서버측에 보내면, Session ID를 보고 맞는 페이지를 뿌린다.

     ![session02](..\img\session02.JPG)

     - Client가 한 번 접속할 때마다 Session을 찾고, timeout을 업데이트 하고, 권한을 확인한 후 그에 맞는 페이지를 내려주는데 이는 상당히 높은 IO작업이 된다. 그래서 많은 웹 서버에서는 RAM이라는 공간에 DB형식으로 Session을 저장할 수 있도록 했다. (가장 기본적인 구성)

     ![session03](..\img\session03.JPG)
     - 서비스의 트래픽이 많아지게 되면, 하나의 웹 서버로는 감당이 어려워진다. 위 그림과 같이 웹서버를 여러개 만들고 앞에 로드밸런스 서버를 구성한다. 이때의 문제는 각 서버에서 세션 공유가 되지 않는 다는 점이다.
     - 예를들어 클라이언트가 첫번째 서버에서 Session ID를 발급받았을 경우 Client는 1번 서버에 대한 Session ID를 가지게 된다. 이후 다름 Request시 로드밸런스 서버가 두번째 서버로  요청을 보낼 경우, 첫번째 Session ID에 대한 값을 찾을 수 없다. Client는 로그아웃 된 페이지를 보게된다.

     ![session04](..\img\session04.JPG)
     - 세션 전용 DB를 만들어서 각 서버들이 해당 세션 DB에서 Session ID를 찾을 수 있도록한다.
     - 이 경우에도 모든 트래픽이 하나의 DB로 몰리게 되고, DB증설이 필요하게 될 것이다. 

   - 결국, 이렇게 복잡해지는 구성을 보완하기 위해 생긴것이 Token Based Auth이다.

   

   - **Token Based Auth**

     ![token01](..\img\token01.JPG)
     - 서버 내부에서 **Session ID를 따로 관리하지 않고**, 발급받은 **토큰**을 다른 서비스를 이용할때 Request로 보내주기만 하면, 해당 토큰이 옳바른지 아닌지만 확인 후 **권한**을 준다.
     - 오직 Request에 토큰만 보고 권한을 준다.
     - 가장 대표적인 Token방식의 Auth는 JWT(Json Web Token)이다.

3. JWT

   - 구성방식은 (<https://jwt.io/>)에서 확인이 가능하다.

   - Token 구성

     - Header / PayLoad / Signature
     - Header : 어떤 알고리즘과 어떤 타입인지 정의

     ![token02](..\img\token02.JPG)

     - PayLoad : 언제 토큰이 끝나는지, 넘길 내용들을 넣는다. 이때 Header와 PayLoad 부분은 encryption이 되지 않기에 민감한 정보는 넣으면 안된다.

     ![token03](..\img\token03.JPG)

     - Signature : Header와 PayLoad의 값을 비밀키와 함께 암호화해서 해쉬값을 받아 온다.

     ![token04](..\img\token04.JPG)

     - 암호화된 모습

     ![token05](..\img\token05.JPG)

   - SpringBoot 에서 JWT를 사용해보기(+a)

     - <https://velog.io/@minholee_93/Spring-Security-JWT-Security-Spring-Boot-10> 
