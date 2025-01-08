---
title: docker container 이미지화 및 tar 저장
date: 2025-01-08
tags: [docker, information]
categories: [research]
author : <jincho>
description : 도커 컨테이너 이미지화
related_posts: True

---

## 도커 컨테이너 환경을 다른 서버에서도 구성해보자.

공용 서버를 이용하기 때문에, 기존에 구성해 놓은 환경을 통재로 다른 서버로 옮겨야 할 때가 있다.

이럴 때 필요한게, 현재 환경인 도커 컨테이너를 이미지화 시키고, 이 이미지를 다시 tar로 압축해서, 다른 서버로 scp를 통해 전달하는 것.

지금의 컨테이너 역시 기존의 도커 이미지를 토대로 생성된 것이지만, 프로젝트가 진행됨에 따라 컨테이너에 추가적인 패키지가 설치되기도 하고, 내가 고쳐놓은 패키지도 있으므로
이걸 그대로 가져가려고 함.

## 1. 도커 컨테이너의 이미지화

컨테이너라고 하는 많은 것들이 담긴 객체 정보를 담는 과정을 이미지화라고 간단히 이해해봄.

- docker ps(혹은 stop된 것도 보려면, docker ps -a) : 현재 가동 중인 컨테이너의 목록을 보여준다. 여기서 container_id 혹은 names 부분이 필요.

![Desktip View](/assets/img/docker_ps.png){: .small} 

docker commit [옵션] <컨테이너ID 또는 이름> <새로운_이미지_이름>

옵션
-a: 작성자 정보.
-m: 메세지 지정.

ex) docker commit -a 'jincho' 241225_jincho pytorch/ajahr:latest


## 2. 도커 이미지 tar로 저장.
- docker images : 이미지 목록 보여줌.

![Desktip View](/assets/img/docker_images.png){: .small} 

docker save [옵션] <파일명> [이미지명]

옵션  
-o: 저장할 파일명 지정

ex) docker save -o totitan.tar pytorch/ajahr:latest

## Reference

https://mvje.tistory.com/168
https://www.leafcats.com/240