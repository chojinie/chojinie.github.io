---
title: Could not load library libcudnn_cnn_train.so.8. Error
date: 2024-12-17
tags: [pytorch, information]
categories: [research, pytorch]
author : <jincho>
description : pytorch 에러 기록
related_posts: True

---

## Error

"Could not load library libcudnn_cnn_train.so.8. Error: /usr/lib/x86_64-linux-gnu/libcudnn_cnn_train.so.8: undefined symbol: _ZN5cudnn3cnn5infer22queryClusterPropertiesERPhS3_, version libcudnn_cnn_infer.so.8"


CLIFF 학습 코드 돌아가는지 확인하다가 발견한 오류 중 하나.  GPT 도 해결못하더라. 아래 방법대로하니까 해결됨. <br>

local cudnn과 pip cudnn 간의 충돌 문제라고 함. 컨테이너에 설치되어 있는 것과, 가상환경에서 설치한 것 간의 충돌인듯.<br>

pip list 통해서 아래 검색.<br>

--> nvidia-cudnn-cu12        8.9.2.26<br> 

이걸 지우면 된다.

pip uninstall nvidia-cudnn-cu12<br>

그러고 다시 원래 실행해보려던 코드 실행하면 문제 해결되어있음.

## Reference

https://machine-does-not-lie.tistory.com/entry/torch-Could-not-load-library-libcudnncnntrainso8-Error-%EC%98%A4%EB%A5%98<br>
