
# Packer

* [패커(Packer)][packer]는 Hashicorp에서 만든 범용적 가상 머신/컨테이너 이미지 생성기

[packer]: https://www.packer.io/
 
## Hashicorp
인프라 자동화와 관련된 다양한 도구들을 개발하고 있음.
 
 * Vagrant: Create and configure portable development environments
 * Serf: Decentralized solutio for cluster membership, failure detection, and orchestration.
 * Consul: Distributed highly available tool for service discovery, configuration and orchestration
 * Terraform: Create, combine and manage infrastructure across multiple providers
 * Vault: entrally store, secure and control access to distributed secrets
 * Nomad: Cluster manager and scheduler to deploy applications across any infrastructure

# Packer

> Packer is a tool for creating machine and container images for multiple platforms from a single source configuration.
> 패커(Packer)는 하나의 설정 소스(sigle source configuration)로부터 여러 플랫폼을 지원하는 머신/컨테이너 이미지를 만드는 도구이다.
-- [Packer offcial website][packer]

* 한 마디로, 범용적인 머신/컨테이너 이미지 생성기
* 하나의 설정 소스 -> 다양한 이미지 생성(1:n)

## 설치

https://www.packer.io/downloads.html

```
brew install packer
```

## 용어

* Artifacts: 하나의 빌드의 결과. 이미지를 나타내는 아이디들이나 파일들로 구성됨
* Builds: 하나의 플랫폼에서 하나의 이미지를 만들기 위한 작업 단위
* Builders: 특정 플랫폼의 이미지를 만들기 위한 플러그인
* Commands: 패커의 명령어
* Post-processors: 빌드가 끝난 후 어떤 작업을 하기 위한 플러그인
* Provisioners: 빌드(서버 설정)을 진행할 플러그인
* Templates: 빌드를 위한 JSON 템플릿 파일

## 빌드 과정

1. Builder로 인스턴스 실행 
2. Provisioner로 배포 
3. Image(Artifact) 생성
4. Post-processors

## 빌더

이미지 형식. [지원하는 빌더 목록은 아래와 같다.][docs]

```
Amazon EC2 (AMI), Azure Resource Manager, CloudStack, DigitalOcean, Docker, Google Compute Engine, Null, OpenStack, Parallels, QEMU, VirtualBox, VMware, Custom
```

[docs]: https://www.packer.io/docs/

## 프로비저너

이미지와 환경을 구성하기 위한 도구. 현재 패커에서 지원하는 목록은 아래와 같다.

```
Remote Shell, Local Shell, File Uploads, PowerShell, Windows Shell, Ansible Local, Ansible Remote, Chef Client, Chef Solo, Puppet Masterless, Puppet Server, Salt, Windows Restart, Custom
```

## Packer 명령어

Packer는 기본적으로 명령행 도구를 사용한다.

```
$ packer
usage: packer [--version] [--help] <command> [<args>]

Available commands are:
    build       build image(s) from template
    fix         fixes templates from old versions of packer
    inspect     see components of a template
    push        push a template and supporting files to a Packer build service // Atlas
    validate    check that a template is valid
    version     Prints the Packer version
```

## 템플릿

```json
{
  "builders": [{
    // ...
  }],
  "provisioners": [{
    // ...
  }]
}
```

## 도커 이미지 만들기

```
FROM ubuntu:14.04

RUN apt-get update
RUN apt-get install -y wget
```

## 패커로 같은 이미지 만들기

```
{
  "builders": [{
    "type": "docker",
    "image": "ubuntu:16.04",
    "export_path": "./nacyot-ubuntu-wget.tar"
  }],
  "provisioners": [{
      "type": "shell",
      "inline": [
        "apt-get update",
        "apt-get install -y wget"
      ]
  }],
  "post-processors": [{
      "type": "docker-import",
      "repository": "nacyot/ubuntu",
      "tag": "wget"
  }]
}
```

### Ansible 사용하기


### VirtualBox 이미지 만들기
### AWS AMI(Amazon Linux) 만들기

```json
{
  "builders": [{
    "type": "amazon-ebs",
    "access_key": "<AWS_ACCESS_KEY>",
    "secret_key": "<AWS_SECRET_KEY>",
    "region": "ap-northeast-1",
    "source_ami": "ami-cbf90ecb",
    "instance_type": "m3.medium",
    "ssh_username": "ec2-user",
    "ami_name": "CustomImage {{isotime | clean_ami_name}}"
  }],
  "provisioners": [{
    "playbook_file" : "ansible/playbook.yml",
    "type": "ansible-local",
    "playbook_dir": "/Users/../ansible"
  }]
}
```

### 같은 환경을 Vagrant에서 실행하기


