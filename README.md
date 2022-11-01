# msa-starter-kit

## 소개

msa(MicroService Architecture)를 시작하기 위한 starter kit 입니다. 사용자는 이 starter kit을 통하여
가지고 있는 VM 또는 Cloud 환경(AWS,Azure,GCP..)에 MSA Architecure를 설치하고
서비스를 구성하여 테스트하고 이를 devops환경으로 이용할 수 있습니다.

사용자는 이 starter-kit을 통하여 msa를 구성하는 outer, inner 아키텍처의 기본 구성요소를 자유롭게 선택하여 환경을 구성하고 설치, 제거할 수 있습니다.

### starter-kit 구성도

다음 그림과 같이 설치 Agent가 github의 script code를 받아 설치대상 VM에 설치하도록 되어 있습니다.

- 설치 Agent - github에서 ansible code를 받아 실행할 PC또는 notebook.
- 설치대상 VM - 개발환경 혹은 테스트환경으로 이용할 VM 혹은 서버, 설치할 솔루션 혹은 app에 따라 높은 사양이 필요할 수 있음

![image](https://user-images.githubusercontent.com/112376183/195263573-d76fd0a9-61b1-47fc-a025-0469da92af92.png)

## 준비

설치 및 실행을 위해 우선은 설치 agent로 사용할 pc와 설치할 대상이 필요합니다.
설치 대상은 vm이면 무엇이든 상관 없습니다(vagrant,azure,aws,gcp)만, 설치 agent로 사용할 pc와 설치대상은
네트워크로 연결 가능해야 합니다(port 22,6443)

설치 agent의 설치 스크립트는 ansible로 작성되어 있으며 실행되어야 하므로 미리 ansible 설치가 필요합니다.

### ansible 설치

- 공식 document - https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
  - 설치 가이드를 참고하여 설치를 수행합니다. 버전에 따라 오동작할 수 있으며 2022.10기준 최신버전은 core 2.13.4 입니다.
  - 설치 후 다음과 같이 대상을 인벤토리에 등록하여 ping 테스트를 수행합니다.

```bash
# export ANSIBLE_HOST_KEY_CHECKING=False  # ssh key check를 무시하는 ssh설정
ansible -i hosts-vm all -m ping
# ansible all -m ping
```
### 설치프로젝트 clone

설치 프로젝트를 로컬에 clone 하여 다운로드 합니다.

```bash
git clone https://github.com/oscka/msa-starter-kit.git

```

### 설치대상정보 업데이트

ansible에서는 설치 대상 정보를 인벤토리(Inventory) 라는 개념으로 관리하며 인벤토리 정보를 설치 대상 VM정보에 맞게 업데이트 해 주어야 합니다.

기본적으로 host_ip, user_name, port등이 이에 해당됩니다.
기본 설치 계정은 클라우드 환경 또는 vagrant를 통해 생성시 sudo 권한을 가지고 있습니다. 이에 따라 설치와 관련된 권한은 root(become=true)를 이용할 수 있으며 설치 후 해당 계정을 계속 이용할 것인지는 상황에 맞게 판단합니다.

```bash
; group_name
[vms]
;vm_name ansible_host_ip ansible_user_name ansible_port ansible_ssh_private_key_file
step1 ansible_host=192.168.56.10 ansible_user=vagrant ansible_port=22 ansible_ssh_private_key_file=../test-vm1/.vagrant/machines/default/virtualbox/private_key
```


## 실행

프로젝트의 구성은 다음과 같습니다.
```
.
├── playbooks                   # playbook 경로, script 실행경로
├── roles                       # 설치 role 경로
│   ├── role-k8s-apps
│   │   ├── defaults
│   │   ├── files
│   │   ├── handlers
│   │   ├── meta
│   │   ├── tasks
│   │   ├── templates
│   │   ├── tests
│   │   └── vars
│   ├── role-k8s-demo-service
│   └── role-tools
└── test-vm1                    # vagrant vm 경로
```

다음과 같이 상황에 맞게 스크립트를 실행하여 설치를 수행합니다. 기본이 되는 태그는 tool-basic 이고(설치되어 있어야 다른 설치시 오류가 발생하지 않음), argocd, pinpoint등 외부로 연결되어야 하는 솔루션은 ingress-nginx가 미리 설치되어 있어야 설치가 가능합니다.

```bash
cd playbook
# 전체 설치
./run-play.sh  "tool-basic, helm-repo, k3s, ingress-nginx, jenkins, argocd, loki-stack, pinpoint, mysql, demo-api-argocd, demo-fe-argocd"

# 기본 환경만 설치(cluster,kubectl,k9s등)
./run-play.sh  "tool-basic, helm-repo, k3s"

# 기본 환경 설치 후 cicd 도구 설치(의존성이 필요한 경우를 제외하고 해당 도구만 설치할 수 있음)
# 서비스 노출을 위해서는 ingress-nginx 가 먼저 설치되어 있어야 함
./run-play.sh  "ingress-nginx, argocd"

# 기본환경+shell환경만 설치(ohmyzsh+자동완성)
# oh-my-zsh+auto complete+alias...
./run-play.sh  "tool-basic, ohmyzsh"
```

## 참고

### Jenkins 설치 및 CI구성

Jenkins의 경우 job실행 속도 문제로 클러스터 밖의 환경에 별도로 설치하도록 구성되어 있으며 설치만 수행하고 있음
이후 아래와 같은 작업이 필요하다.

```bash
#1.jenkins계정에 docker 실행권한 부여(재시작,재로그인 후 반영)
sudo usermod -aG docker jenkins
sudo service docker restart

#2.계정 생성 및 비밀번호 변경
#admin/admin1234 로 관리자 계정 생성(UI에서)
cat /var/lib/jenkins/secrets/initialAdminPassword

#3.로그인 후 플러그인 설치
# 플러그인 3개 - git parameter, workspace cleanup, docker pipeline

#4.secret 생성 - git-credential, imageRegistry-credential
#jenkins관리 - credential상에 위의 이름으로 생성하고 각각 github의 accesstoken 정보와 docker hub의 ID/PW정보를 입력해 둔다.

#5.job 생성
#- sample-api-build를 pipeline job으로 생성
#- 이 빌드는 매개변수가 있습니다. 를 선택하여 TAG를 입력
#- pipeline script는 SCM에서 가져온 Jenkinsfile을 선택

#6. 빌드 도구 설치
# skaffold, kustomize 설치
# jenkins계정에서 docker 수행이 가능해야 함
```

