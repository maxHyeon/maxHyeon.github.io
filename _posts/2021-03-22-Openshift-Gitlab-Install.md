---
layout: posts
title: "Openshift Gitlab Helm 설치"
date: 2021-03-17 12:00:00 -0400
categories: Openshift
---
## Openshift Gitlab Install
* Openshift Gitlab Helm Install
1. Helm Repo Add
    * 레퍼런스는 여기 확인
        - https://docs.gitlab.com/charts/quickstart/index.html
    * Helm Repo Add
        ```bash
          helm repo add gitlab https://charts.gitlab.io/
        ```
    * OC login and helm install
        ```bash
          oc login -u ~~~ 
          # 새로운 프로젝트 생성해서 네임스페이스 만드는걸 추천함
          oc new-project gitlab 
          # Helm Install 
          # domain 은 앞에 자동으로 깃렙이 붙는다. readness probe를 저걸로 해서 잘못 입력하면 다시 설치하는 참사가 발생한다. 
          helm install gitlab gitlab/gitlab \
          --set global.hosts.domain=apps.ocp.example.com \
          --set certmanager-issuer.email=me@example.com
        ```
    * Service Account 권한 부여. 대부분 컨테이너가 특정 uid로 실행되기에 SCC 부여하자.
        ```bash
          # 이거 안하면 다음으로 안넘어감.
          oc adm policy add-scc-to-user anyuid -z gitlab-shared-secrets
          # 자동으로 SA가 생성되니 콘솔에서 보고 해도 되고 oc get sa 로 gitlab 관련 sa 체크해서 권한부여 한다.
          oc adm policy add-scc-to-user anyuid -z gitlab-cainjector
          oc adm policy add-scc-to-user anyuid -z gitlab-cert-manager
          oc adm policy add-scc-to-user anyuid -z gitlab-certmanager-issuer
          oc adm policy add-scc-to-user anyuid -z gitlab-gitlab-runner
          oc adm policy add-scc-to-user anyuid -z gitlab-nginx-ingress
          oc adm policy add-scc-to-user anyuid -z gitlab-nginx-ingress-backend
          oc adm policy add-scc-to-user anyuid -z gitlab-prometheus-server
        ```
2. 설치가 완료된 후 깃랩 접속 된 뒤 확인
    * 초기 root 패스워드를 옵션으로 설정하지 않은 경우 아래의 명령으로 root pw확인
        ```bash
          kubectl get secret gitlab-gitlab-initial-root-password -ojsonpath='{.data.password}' | base64 --decode ; echo
        ```
    * 로그인은 root 에 위에서 확인한 명령으로 로그인 하자

끝
