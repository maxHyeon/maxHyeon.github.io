---
layout: posts
title: "리눅스 CR/LF 변환 파이썬 & 끝에 라인개행 들어간 파일 제거"
date: 2021-03-17 12:00:00 -0400
categories: linux
---
##개요
* 리눅스의 라인개행은 윈도우의 라인개행과 다르다. 고객의 요청으로 많은 건수의 파일을 이동할 일이 있었는데,   
  파일 리스트를 윈도우에서 작성해서 넘어오는 바람에 원치않은 라인개행 문자가 파일 끝에 들어가게 되어 해결방법을 남긴다.
1. 파일 리스트 라인개행 리눅스 라인으로 변환
    * 구글링을 해보면, sed를 이용한 방법, awk를 이용한 방법 등 많은 방법이 있는데, 잘 동작하지 않아 간단하게 파이썬으로 작성 하였다.
        ```bash
          cat << EOF > ./conversion_crlf.py
          import os
          # os.path.isfile() 같은거 쓸때 유용하다. 
          before = open('before.txt')
          after = open('after.txt','w')
          for beforeFileName in before:
            # 단순 CRLF 변경인 경우는 \r 만 넣어주면 되지만
            after.write(beforeFileName.rstrip()+'\r')
            # 실수로 라인개행이 들어간 상태로 파일을 생성한 경우에는 '?' 를 추가 해 주어야 잘 지워진다.
            after.write(beforeFileName.rstrip()+'?'+'\r')
          before.close()
          after.close()
          EOF

          python ./conversion_crlf.py
        ```
2. 끝에 라인 개행이 들어간 파일 지우기 
    * Python에도 isfile() 수행한 이후에 Delete가 가능하지만 뒤에 ? 가 들어간 파일은 지워지지 않는다. 리눅스 for 로 지우자 잘 지워진다.
        ```bash
          for i in $(cat ./after.txt); do rm $i ; done
        ```

끝

