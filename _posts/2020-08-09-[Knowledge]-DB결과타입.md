---
layout: post
title: "[Knowledge] DB결과타입"
date: 2020-08-09 14:00:00
categories: Knowledge
permalink: /Knowledge/dbType
---

# DB결과 타입

1. 현재 구성

   - 단일 조회의 경우, command 객체를 이용해 가져온다.

   - 다중 조회의 경우 ArrayList 혹은 Hashmap을 이용해 결과값을 가져오고 있다.

   - 처리해야할 파라미터가 점점 많아져서 지금은 너무 방대해진 것 같다.

     

2. 생각해 본 내용

   - 단일 조회용 같은 경우엔 Optional<객체>을 사용해 NPE(NullPointException)잡을 수 있도록
   - 다중 조회용 같은 경우엔 List사용 등 
   - 큰 부분을 먼저 정해도 좋을것 같다.







