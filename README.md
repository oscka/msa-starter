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

![image](https://user-images.githubusercontent.com/2344830/193701469-c1f086b9-92ee-48fb-8e19-76448140d596.png)

## 준비

설치 및 실행을 위해 우선은 설치 agent로 사용할 pc와 설치할 대상이 필요합니다.
설치 대상은 vm이면 무엇이든 상관 없습니다(vagrant,azure,aws,gcp)만, 설치 agent로 사용할 pc와 설치대상은
네트워크로 연결 가능해야 합니다(port 22,6443)

설치 agent의 설치 스크립트는 ansible로 작성되어 있으며 실행되어야 하므로 미리 ansible 설치가 필요합니다.

### ansible 설치

(작성중) ansible 설치방법 설명

### 설치프로젝트 clone

설치 프로젝트를 로컬에 clone 하여 다운로드 합니다.

```bash
git clone https://github.com/oscka/msa-starter-kit.git

```

### 설치대상정보 업데이트

ansible에서는 설치 대상 정보를 인벤토리(Inventory) 라는 개념으로 관리하며 인벤토리 정보를 설치 대상 VM정보에 맞게 업데이트 해 주어야 합니다.

기본

```bash
; group_name
[vms]
;vm_name ansible_host_ip ansible_user_name ansible_port ansible_ssh_private_key_file
step1 ansible_host=192.168.56.10 ansible_user=vagrant ansible_port=22 ansible_ssh_private_key_file=/home/ska/git/oscka/ansible-handson/test-vm1/.vagrant/machines/default/virtualbox/private_key
#step2 ansible_host=192.168.56.20 ansible_user=vagrant ansible_port=22 ansible_ssh_private_key_file=/home/ska/git/oscka/ansible-handson/test-vm2/.vagrant/machines/default/virtualbox/private_key
```


## 실행

(작성중) 스크립트를 실행하여 설치를 수행하는 방법-향후 프로젝트 구조에 따라

## 참고
