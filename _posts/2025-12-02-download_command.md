---
title: download_command /w curl, cookie
date: 2025-12-02
tags: [research, information]
categories: [research]
author : <jincho>
description : When gdown and curl commands don’t work
related_posts: True

---

## Gdown이나 Curl을 사용할 때 다운로드 안되는 경우.

![Desktip View](/assets/img/down_failcase.png){: .small}

때때로 파일 크기와 관계없이 gdown이나 curl 같은 일반적인 다운로드 명령어가 Google Drive에서 제대로 동작하지 않을 때가 있다.
이런 문제를 해결하기 위해 curl + 쿠키 방식을 활용한 신뢰할 수 있는 대체 방법을 정리해 둔다.

문제가 발생하면 Chrome에서 개발자 도구(F12) 를 열고 Network(네트워크) 탭으로 이동한다.
그 상태에서 Google Drive의 “무시하고 다운로드” 버튼을 클릭하면 실제 다운로드 요청과 쿠키 정보가 나타난다.
이미지와 같이 쿠키 값과 Request URL을 확보한다.

두 가지 정보를 얻었다면, 아래와 같은 형식으로 명령어를 실행한다.

![Desktip View](/assets/img/cookies.png){: .small}
![Desktip View](/assets/img/request_url.png){: .small}

그리고 아래 커맨드 규칙에 따라 입력한다.
curl -Lb \
  -H "쿠키값 전체" \
  "request url" \
  -o 저장될파일이름

예시
![Desktip View](/assets/img/success_case.png){: .small}
