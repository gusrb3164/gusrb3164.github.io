---
layout: post
title: API 요청 테스트를 위한 curl 사용해보기
description: API 요청 테스트를 위해 가장 주로 쓰이는 curl에 대해서 알아보자
categories: Web
tags: [Programming, Web]
---

## curl 이란

command line 용 data transfer tool 이다. HTTP/HTTPS/FTP/LDAP/SCP/TELNET/SMTP/POP3 등 주요한 프로토콜을 지원하며 Linux/Unix 계열 및 Windows 등 주요한 OS 에서 구동되므로 주로 HTTP, HTTPS 요청을 보내는데 사용된다. Linux나 Mac에는 기본으로 있으며 윈도우는 추가로 다운받아야 한다.

## curl HTTP 옵션

- -X, --request <command> : 요청 메서드를 지정
- -H, --header : HTTP Header에 에 데이터 추가. ex) Authorization, Content-Type
- -d, --data <data> : POST 의 body 에 보낼 데이터 추가
- --data-ascii <data> : POST 의 body 에 보낼 ASCII 데이터 추가
- --data-binary <data> : POST 의 body 에 보낼 이진 데이터 추가
- -i : 서버 요청의 응답 헤더를 메세지로 출력

> ex)  
> curl -X POST\\  
> -H Content-Type:application/json\\  
> -H Authorization: Bearer 1234\\  
> -d @data.json\\  
> http://example.com/api
