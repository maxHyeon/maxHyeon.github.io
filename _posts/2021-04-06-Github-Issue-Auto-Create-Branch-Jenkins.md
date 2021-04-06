---
layout: posts
title: "Github Issue 생성시 Feature Branch Auto Generate"
date: 2021-04-06 12:00:00 -0400
categories: Jenkins
---
## Github Issue 생성시 Feature Branch Auto Generate
# 개요
1. Jenkins Plugin 설치
2. Github Hook 설정
3. Jenkins 파이프라인 설정

# Jenkins Plugin 설치
1. Jenkins Generic Webhook Plugin 이 필요하다. 
    - Generic Webhook Trigger plugin 설치 진행
        * https://plugins.jenkins.io/generic-webhook-trigger/
# Github Hook 설정
1. Github Project 의 Settings 의 Webhook 설정에서 설정을 진행한다.
    - Payload URL 에 Jenkins 주소 입력 
        * http://[host]/generic-webhook-trigger/invoke?token=[token] => invoke 부분은 추가 보안 설정임 추후에 Jenkins 에서 설정 진행
    - Let me select individual events 클릭 후 ISSUE 체크박스 체크
    - ![image](https://user-images.githubusercontent.com/11882246/113653564-1041a180-96d1-11eb-87d8-f65ea9c200dd.png)
# Jenkins 파이프라인 설정
1. Build Triggers 설정
    - Buid Trigger 의 Generic Webhook Trigger 선택 
        * ![image](https://user-images.githubusercontent.com/11882246/113654637-39633180-96d3-11eb-94a4-363043b0f381.png)
    - Webhook 에서 Json Path 로 Branch 생성에 필요한 Issue 정보 받아서 Param 으로 변환
        1. 이슈 정보 
            - TITLE / $.issue.title
        2. Action
            - ACTION / $.action
        3. HTMLURL
            - HTMLURL / $.repository.html_url
        4. REPONAME
            - REPONAME / $.repository.name
        5. OWNER
            - OWNER / $.repository.owner.login
2. TOKEN 설정
    - Github 의 TOKEN 이 아닌 Webhook 시 보안을 위한 Token 을 설정한다. 
        * Build Triigers 아래의 Token 항목에서 설정. 
3. Pipeline Script 설정
    ```
        node {
          stage('hello') { // for display purposes
              echo "$TITLE"
              echo "$ACTION"
              echo "$HTMLURL"
              echo "$REPONAME"
          }
          stage ('create repo') {
                withCredentials([usernamePassword( credentialsId:'maxHyeon',usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                //echo "$TOKENID"
                //echo "$TOKEN"
                //echo "$HTMLURL".substring(0,7).concat("$TOKENID").concat(":").concat("$TOKEN").concat(8)
                String FULLURL = HTMLURL

                def BASEURL = FULLURL.substring(8)
                sh '''
                  # 다른 plugin 없이 git 자체의 명령으로 최소화 함
                  rm -rf ./$REPONAME
                  git clone https://$GIT_USERNAME:$GIT_PASSWORD@$BASEURL -b develop
                  cd $REPONAME
                  git checkout -b feature/$TITLE --track origin/develop
                  git push origin feature/$TITLE
                  cd ..
                  rm -rf ./$REPONAME
                '''

              }
           }
        }

    ```
         
끝
