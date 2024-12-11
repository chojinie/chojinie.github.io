---
title: H36M dataset download
date: 2024-12-11
tags: [h36m, dataset]
categories: [research]
author : <jincho>
description : 데이터셋 다운로드 방법 기록
related_posts: True
image:
  path: /assets/img/h36m_cover.png
  alt: h36m 데이터셋 다운 방법.
---

## H3.6M Dataset

설치법 기록

H3.6M homepage에서 데이터를 다운받는다.

현재는 회원가입 받지 않아주는 듯 함. 어떻게 데이터 얻는지는 모르겠음. </br>


<a href="https://khw11044.github.io/blog/blog-etc/2021-07-17-human36m/" style="color: blue; text-decoration: underline;">블로그</a>글을 참고하였음. </br>

찾아보니까 <a href="https://github.com/anibali/h36m-fetch" style="color: blue; text-decoration: underline;">H36m toolbox</a> 라는 곳에서 친절하게도 데이터셋을 다운 받을 수 있는 모듈을 만들어주셔서 사용. </br>

(1) <a href="https://github.com/anibali/h36m-fetch" style="color: blue; text-decoration: underline;">이 곳</a>에 접속 하여 git clone https://github.com/anibali/h36m-fetch.git</br>

(2) installation이 그대로 안 먹힌다. </br>
pip install numpy tqdm h5py spacepy requests</br>
로 버전 지정없이 환경 설치해준다.

(3) 기타 환경 설치</br>
apt install make gcc gfortran</br>

git clone https://github.com/scivision/spacepy-installer</br>

python spacepy-installer/setup_spacepy.py</br>

git clone https://github.com/axel-download-accelerator/axel.git</br>

apt-get install axel</br>

(4) H3.6M homepage에 가입이 성공했으면 PHPSESSID라는 것을 얻을 수 있음. 윈도우나 mac이나 개발자 모드 들어가서 Cookies 쪽에 있다. 
![Desktip View](/assets/img/php.png){: .small} 

cd H36M-fetch로 폴더 들어가서 보면 config.ini.example이 있는데 config.ini로 이름 변경해주고,
안에 들어가서 Key에 PHPSESSID 값을 넣어주면 된다.

(5) python download_all.py > extract_all.py > video_to_images.py -> generate_labels.py 순으로 코드 진행하면 
다운로드와 파싱이 가능.

물론 필요에 따라서 프레임 간격 수나, 다운 받을 대상에 대한 이름 변경 등이 코드 내에서 진행될 수도 있음.



## Reference

https://github.com/anibali/h36m-fetch</br>
https://khw11044.github.io/blog/blog-etc/2021-07-17-human36m/</br>