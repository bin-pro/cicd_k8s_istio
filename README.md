# cicd_k8s_istio

### 프로세스 요약

1. **코드 푸시**:  
개발자는 작성한 코드를 Github에 푸시한다.

2. **Github 트리거**:   
    - Github는 Jenkins에 Webhook을 통해 트리거 메세지를 보낸다.  
    - Github 저장소 구성:  
        - 주요 폴더: `application folder`, `gitops folder`, `.gitignore`  
        - `application folder`의 하위 구조:  
            1. `frontend`  
            2. `backend`  
        - `gitops`의 하위 구조:  
            1. `.jenkins/Jenkinsfile`, ...  
            2. `.docker/Dockerfile`, ...  
            3. `.harbor/config.yml(json)`, ...  
            4. `.cicd/[설정 파일명(manifest)]`  

3. **Jenkins 작업**:   
    1. 변경된 코드를 감지한다.  
    2. 해당 코드를 빌드 및 테스트한다.  
    3. 도커 이미지로 코드를 빌드한다.  
    4. GitOps 내용을 새롭게 빌드된 도커 이미지 정보로 업데이트한다.

4. **도커 이미지 전송**:  
Jenkins는 빌드된 도커 이미지를 HARBOR에 push하며, HARBOR는 k8s가 이미지를 pull 할 수 있도록 설정되어 있다.

5. **GitOps 도커 이미지 버전 갱신**:  
Jenkins가 Github 내 `gitops/.docker/Dockerfile`의 GitOps 도커 이미지 버전을 최신화하면, Github는 k8s 클러스터 내의 ArgoCD에 Webhook 트리거 메세지를 전송한다.

6. **ArgoCD 동기화**:  
ArgoCD는 GitOps 내 Application 매니페스트 파일 변경을 감지하고, k8s의 현재 매니페스트와 차이가 있을 경우 k8s의 매니페스트를 최신 버전으로 업데이트한다.

7. **k8s 배포**:  
k8s는 AWS AutoScailing Group을 사용하여 Pod를 배포한다.

8. **MSA 모니터링**:  
k8s 내에서 Istio와 함께 마이크로서비스 아키텍처를 모니터링하고 관리한다.


test
