<div align="center">

# Semiconductor Design Automative

**반도체 설계 흐름을 이해하고, Linux에서 작업하며, Git으로 변경 이력을 관리하는 기초 실습**

![Semiconductor](https://img.shields.io/badge/Study-Semiconductor_Design-16324F?style=flat-square)
![Linux](https://img.shields.io/badge/Environment-Linux-FCC624?style=flat-square&logo=linux&logoColor=black)
![Git](https://img.shields.io/badge/Version_Control-Git-F05032?style=flat-square&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/Repository-GitHub-181717?style=flat-square&logo=github&logoColor=white)

Semiconductor Design Automative 주제로 진행한 실습을 SoC 100 수업 자료와 함께 정리한 기록입니다.

</div>

---

## Overview

반도체 설계 자동화를 이해하고 활용하려면 설계 흐름에 대한 이해와 함께 설계 파일을 다루는 작업 환경, 변경 이력을 관리하는 방법이 필요합니다. 이번 실습에서는 **설계 개념 → Linux 작업 환경 → Git 기반 버전 관리**를 연결해 학습했습니다.

| Session | 학습 주제 | 핵심 내용 |
| :---: | --- | --- |
| 01 | 반도체 설계의 배경과 방법 | EDA, 설계 추상화, Full Custom / Semi Custom, FPGA |
| 02 | Linux 기본·응용 명령어 | 경로, 파일 관리, Vim, Shell, 검색, 권한, 프로세스 |
| 03 | Git / GitHub 환경 구성 | 버전 관리, SSH 연결, Commit / Push, Markdown, Sourcetree |

## 01. Semiconductor Design Fundamentals

### 설계 자동화가 필요한 이유

반도체의 집적도가 높아지면서 설계자가 모든 소자와 배선을 직접 다루기 어려워졌습니다. 수업에서는 규모의 경제와 무어의 법칙을 배경으로, 수작업 중심 설계가 **EDA(Electronic Design Automation)**와 **HDL(Hardware Description Language)**을 활용하는 방식으로 발전한 흐름을 살펴봤습니다.

### 설계 추상화 수준

| 추상화 수준 | 설계 관점 | 다루는 내용 |
| --- | --- | --- |
| System | 시스템 전체 | 모듈 구성과 인터페이스 |
| RTL | 레지스터 간 동작 | 데이터 이동과 논리 연산 |
| Gate | 논리 게이트 연결 | 게이트 수준 Netlist |
| Circuit | 전기적 회로 | 트랜지스터, 저항, 커패시터 |
| Device | 소자 자체 | 소자의 구조와 특성 |

**상위 수준에서는 기능과 동작을, 하위 수준에서는 회로와 소자의 구현을 더 구체적으로 다룹니다.**

### Full Custom과 Semi Custom 비교

| 구분 | Full Custom | Semi Custom |
| --- | --- | --- |
| 설계 방식 | 트랜지스터 등 회로 요소와 레이아웃을 직접 설계 | RTL과 Standard Cell Library를 활용 |
| 주요 작업 | 회로 설계, 물리적 레이아웃 설계 | HDL 작성, 논리 합성, 자동 배치·배선 |
| 특징 | 세부 특성을 직접 조정할 수 있으나 설계 부담이 큼 | 검증된 셀과 자동화 도구로 설계 생산성을 높임 |

- **Logic Synthesis:** RTL로 기술한 동작을 목표 셀 라이브러리의 논리 회로로 변환합니다.
- **APR(Auto Place & Route):** 셀을 배치하고 셀 사이의 배선을 연결합니다.
- **Front End / Back End:** 수업에서는 논리 설계·합성과 물리 구현으로 이어지는 흐름을 구분해 학습했습니다.
- **FPGA:** 제조 후에도 논리 구성을 바꿀 수 있는 소자로, 회로 동작 검증과 프로토타이핑에 활용합니다.

> RTL은 하드웨어 동작을 기술한 모델이며, 합성과 물리 구현을 거쳐 실제 회로 구조로 구체화됩니다.

## 02. Linux Command Practice

### 파일과 경로 다루기

| 목적 | 명령어 예시 | 핵심 개념 |
| --- | --- | --- |
| 현재 위치 확인 | `pwd` | 작업 중인 디렉터리의 절대 경로 |
| 경로 이동 | `cd ..`, `cd ~` | 상위 디렉터리와 사용자 홈 |
| 파일 목록 확인 | `ls -la` | 숨김 항목과 상세 정보 |
| 도움말 확인 | `man cp` | 명령어의 사용법과 옵션 |
| 파일 생성 | `touch notes.txt` | 파일이 없으면 빈 파일 생성 |
| 디렉터리 생성 | `mkdir -p practice/linux` | 중간 경로까지 생성 |
| 디렉터리 복사 | `cp -r src backup` | 하위 항목을 재귀적으로 복사 |
| 이동·이름 변경 | `mv old.txt new.txt` | 목적지에 따라 이동 또는 이름 변경 |
| 내용 확인 | `cat`, `less`, `head`, `tail` | 전체·페이지·앞부분·뒷부분 확인 |

절대 경로는 `/`에서 시작하고, 상대 경로는 현재 작업 위치를 기준으로 해석됩니다. `cp -r`처럼 경로를 다루는 작업에서는 **현재 위치와 원본·목적지 경로**를 먼저 확인합니다.

### 명령어 조합 예제

아래 코드는 수업 명령어를 복습할 수 있도록 구성한 Bash 예제입니다.

```bash
mkdir -p soc100-practice/linux          # 실습용 디렉터리 생성
cd soc100-practice/linux                # 실습 위치로 이동
pwd                                    # 현재 경로 확인

printf 'INFO: start\nERROR: example\n' > run.log  # 예제 로그 생성
cat run.log                            # 전체 내용 확인
grep -n 'ERROR' run.log                 # 패턴과 일치하는 행의 번호 확인
cat run.log | grep 'ERROR'              # 앞 명령의 출력을 다음 명령의 입력으로 전달
grep 'ERROR' run.log > errors.log       # 검색 결과를 파일로 저장, 기존 내용 덮어쓰기
printf 'INFO: end\n' >> run.log         # 기존 파일 끝에 내용 추가

cp run.log run_backup.log               # 파일 복사
mv run_backup.log archived.log          # 파일 이름 변경
ln -s run.log latest.log                # 원본 경로를 가리키는 심볼릭 링크 생성
ls -l latest.log                        # 링크 대상 확인

tar -czvf logs.tgz run.log errors.log archived.log  # 파일을 묶고 gzip 압축
tar -tzvf logs.tgz                      # 압축 파일 내부 목록 확인
mkdir extracted                        # 압축 해제용 디렉터리 생성
tar -xzvf logs.tgz -C extracted         # 지정한 디렉터리에 압축 해제
```

### Shell과 시스템 관리

| 항목 | 예시 | 이해한 내용 |
| --- | --- | --- |
| Shell 설정 적용 | `source ~/.bashrc` | Bash 설정을 현재 Shell에 반영 |
| 별칭 | `alias ll='ls -al'` | 긴 명령을 짧게 사용. 현재 Shell 기준 |
| 하드 링크 | `ln original.txt hard.txt` | 같은 파일 데이터를 참조하는 이름 추가 |
| 심볼릭 링크 | `ln -s original.txt soft.txt` | 대상 경로 참조. 원본 삭제 시 링크가 끊길 수 있음 |
| 권한 | `chmod 755 script.sh` | 소유자 `rwx`, 그룹·기타 사용자 `r-x` |
| 프로세스 확인 | `ps -ef` | 실행 중인 프로세스와 PID 확인 |
| 종료 요청 | `kill <PID>` | 지정한 프로세스에 종료 신호 전달 |

권한의 숫자는 `r=4`, `w=2`, `x=1`을 더해 표현합니다. 디렉터리의 `x`는 해당 디렉터리에 진입하고 내부 경로에 접근하는 권한입니다.

| 명령 연결 | 실행 조건 |
| --- | --- |
| `command1 ; command2` | 첫 명령의 성공 여부와 관계없이 다음 명령 실행 |
| `command1 && command2` | 첫 명령이 성공하면 다음 명령 실행 |
| `command1 \|\| command2` | 첫 명령이 실패하면 다음 명령 실행 |
| `command1 \| command2` | 첫 명령의 표준 출력을 다음 명령의 표준 입력으로 전달 |

### Vim 핵심 조작

| 모드·기능 | 키 | 동작 |
| --- | --- | --- |
| 입력 모드 | `i`, `a`, `o` | 현재 위치, 다음 위치, 다음 행에서 입력 |
| 일반 모드 복귀 | `Esc` | 입력·선택 상태에서 일반 모드로 복귀 |
| 비주얼 모드 | `v`, `V` | 문자 단위, 행 단위 선택 |
| 선택 영역 편집 | `y`, `d`, `c` | 복사, 삭제, 삭제 후 입력 |
| 저장·종료 | `:w`, `:q`, `:wq` | 저장, 종료, 저장 후 종료 |
| 변경 폐기 후 종료 | `:q!` | 저장하지 않고 종료 |

## 03. Git & GitHub Practice

### Git과 GitHub의 역할

**Git은 변경 이력을 관리하는 도구이고, GitHub는 Git 저장소를 원격으로 보관하고 공유하는 서비스입니다.**

| 단계 | 공간 | 수행하는 작업 |
| --- | --- | --- |
| 파일 수정 | Working Directory | 실제 파일 작성·수정 |
| `git add` | Staging Area | 다음 Commit에 포함할 변경 선택 |
| `git commit` | Local Repository | 선택한 변경을 하나의 버전으로 기록 |
| `git push` | Remote Repository | 로컬 Commit을 원격 저장소에 전송 |

Commit만 하면 변경 이력은 로컬에 남습니다. GitHub에 반영하려면 Push가 필요합니다. `git add` 이후 파일을 다시 수정했다면, 추가 수정분을 Commit에 넣기 위해 다시 Add해야 합니다.

### SSH 인증과 저장소 연결

수업에서는 SSH Key를 생성하고 **공개키를 GitHub에 등록**해 원격 저장소에 연결하는 방법을 학습했습니다.

```bash
git --version                          # Git 설치 확인
git config --global user.name "Your Name"        # Commit 작성자 이름 설정
git config --global user.email "you@example.com" # Commit 작성자 이메일 설정

ssh-keygen -t rsa -b 4096 -C "you@example.com"    # 수업에서 사용한 SSH Key 생성 예시
cat ~/.ssh/id_rsa.pub                   # 공개키 출력 후 GitHub에 등록
ssh -T git@github.com                  # GitHub SSH 인증 확인
```

`id_rsa.pub`는 등록할 공개키이고, `id_rsa`는 외부에 공유하거나 저장소에 올리면 안 되는 개인키입니다. Git 작성자 설정과 SSH 인증은 서로 다른 설정입니다.

### README 수정 후 업로드

GitHub에서 README가 포함된 저장소를 만든 뒤 진행하는 예시입니다. `USERNAME`과 `REPOSITORY`는 실제 계정과 저장소 이름으로 바꿉니다.

```bash
git clone git@github.com:USERNAME/REPOSITORY.git  # 처음 한 번 원격 저장소 복제
cd REPOSITORY                                  # 복제한 저장소로 이동
git pull                                       # 원격 변경을 로컬에 반영

# 에디터에서 README.md를 수정하고 저장한 뒤 실행
git status                                     # 변경 파일 확인
git diff                                       # 아직 Stage하지 않은 변경 확인
git add README.md                              # README 변경 선택
git diff --staged                              # Commit에 포함될 변경 확인
git commit -m "docs: summarize SoC100 practice" # 학습 내용 기록
git push origin main                           # 원격 main 브랜치로 전송
git log --oneline                               # Commit 이력 확인
```

이 예시는 기본 브랜치가 `main`인 경우입니다. 작업 전 Pull로 원격 변경을 먼저 반영하며, 충돌이 발생하면 해당 내용을 해결한 뒤 공유합니다.

### CLI와 Sourcetree 비교

| 작업 | CLI | Sourcetree |
| --- | --- | --- |
| 저장소 복제 | `git clone` | Clone |
| 원격 변경 반영 | `git pull` | Pull |
| 변경 상태 확인 | `git status`, `git diff` | File Status |
| 변경 선택 | `git add` | Stage Selected |
| 버전 생성 | `git commit` | Commit |
| 원격 전송 | `git push` | Push |
| 이력 확인 | `git log` | History |

CLI와 GUI는 같은 저장소와 변경 이력을 다룹니다. **명령어로 작업 원리를 이해하고, GUI로 변경 내용과 이력을 확인**할 수 있습니다.

## Verification · 실습 확인 기준

| 확인 항목 | 확인 방법 | 정상 동작의 기준 |
| --- | --- | --- |
| 경로 이해 | `pwd`, `ls -la` | 의도한 위치와 파일을 확인할 수 있음 |
| 파일 편집 | Vim 저장 후 `cat` | 편집한 내용이 파일에 반영됨 |
| 검색·리다이렉션 | `cat errors.log` | 예제의 `ERROR` 행이 저장됨 |
| 압축·해제 | `tar -tzvf logs.tgz`, `ls extracted` | 압축 목록과 해제된 파일을 확인할 수 있음 |
| SSH 인증 | `ssh -T git@github.com` | GitHub 사용자명과 인증 성공 안내가 표시됨 |
| 로컬 버전 기록 | `git log --oneline` | 작성한 Commit 메시지가 표시됨 |
| 원격 반영 | GitHub 저장소 새로고침 | 최신 README와 Commit이 표시됨 |
| CLI·GUI 이력 일치 | Sourcetree History 비교 | CLI에서 만든 Commit도 함께 확인됨 |

위 표는 복습 시 사용할 확인 기준입니다. 실행 로그와 화면 캡처는 이 문서에 포함하지 않았습니다.

## Takeaways

- **설계 관점:** 추상화 수준에 따라 같은 반도체를 시스템, RTL, 게이트, 회로, 소자 관점으로 볼 수 있음을 배웠습니다.
- **작업 환경:** Linux에서 경로와 파일을 관리하고, 검색·파이프·리다이렉션으로 필요한 정보를 추출하는 방법을 익혔습니다.
- **변경 관리:** Add, Commit, Push의 역할을 구분하고, 설계 파일과 학습 기록의 변경 이력을 남기는 기본 흐름을 학습했습니다.

앞으로 RTL 코드, 테스트벤치, 시뮬레이션 결과를 함께 관리하며 **무엇을 수정했고 어떻게 검증했는지 추적할 수 있는 설계 기록**으로 확장하고자 합니다.

## References

업로드된 수업 자료를 바탕으로 요약했습니다. 명령어 예제와 확인 기준은 복습을 위해 구성했습니다.

1. `SoC100_1.pptx` · Session 1: Socio-economical Background of Semiconductor Design Development
2. `SoC100_2(1).pptx` · Session 2: Basic Linux Command (Fast Track)
3. `SoC100_3.pptx` · Session 3: Git / GitHub Configuration
