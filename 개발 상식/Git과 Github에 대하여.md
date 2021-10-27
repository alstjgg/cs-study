**Git(깃)** 은 기본적으로 프로그램의 소스코드를 효율적으로 관리하기 위한 오픈소스 **버전 관리 도구** 즉, **Version Control System(VCS)** 이다

Git은 **Github(깃허브)** 와 **Gitlab(깃랩)** 등의 대형 웹 버전 관리 시스템을 제공한다

# 버전 관리 도구와 Git

## Git 과 SVN

대표적으로 사용되는 버전 관리 도구에는 GIT와 SVN(subversion)이 있다

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/09434a0a-1708-4c3e-b7da-e88069fc97f5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20211027%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211027T130806Z&X-Amz-Expires=86400&X-Amz-Signature=c0e90bce0873b3e93db8d3b95a79c80b3afab4d7d514ecec15cf7c52c31669da&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

[SVN vs. Git](https://www.notion.so/08eca449c3604c5190fdc91791ee812f)

**SVN**은 소스 변경 사항과 히스토리가 서버에 즉시 반영되기 때문에 변경 사항 및 히스토리 관리가 용이하다. 그러나 중앙 저장소 데이터 손실 시 복구가 어려우며, 서버와 클라이언트 간 버전 관리에 문제가 생긴다는 단점이 있다.

그러한 문제점을 로컬, 클라이언트, 리모트 간의 종속성을 꺠고 강력한 **확장성**과 **분산성**을 통해 해결한 툴이 Git이다.

## **Git의 장점**

- 빠른 수행 속도
실제 작업은 로컬에서만 이루어지고 반영할 때에만 네트워크를 이용
- 데이터 무결성 보장
원격저장소와 로컬을 분리
모든 커밋에 대해 Checksum(Hash) 생성
- 비선형 워크플로우 제공
브랜치 기능
- 완벽한 분산 제공(DVCS)

∴ GIT은 분산 버전 관리 도구이며, 중앙관리식 버전 관리 도구인 SVN에 비해 여러가지 장점이 있다

# Git Workflow : 기본적인 파일 관리 흐름

## Git File Lifecycle에 대해 잠시...

작업 디렉토리(Working Directory) 또는 작업 트리(Working Tree)에 있는 파일은 크게 **Tracked**와 **Untracked**로 나뉘어진다

- **Tracked** : Git이 알고있는, 저장소에서 관리하는 파일. 스냅샷이 존재한다
- **Untracked** : 그 외 파일

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/13952f57-2566-4b14-8f2d-58419232518d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20211027%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211027T130829Z&X-Amz-Expires=86400&X-Amz-Signature=447047b9fe025455570592946590ae5b963a7ac521b5e325e308c79476283b21&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

Git File Lifecycle

Git에서 파일은 위와 같은 lifecycle을 가지게 된다

- **Unmodified** : 수정되지 않은 파일
- **Modified** : 수정된 파일
- **Staged** : commit으로 repository에 기록할 파일이자 stage에 올라간 파일

그렇다면 Git에서 각 파일의 상태를 확인하고 또 변경하기 위해서 어떻게 해야할까

## 파일의 상태 확인하기

git status 명령어를 통해 현재 Git Repository 내 파일들의 상태를 확인한다

비어있는 저장소의 경우 아래와 같은 메세지를 확인 할 수 있다

```powershell
$ git status
On branch master		// 현재 master 브랜치에 있다.

No commits yet			// 아직 commit한 파일이 없다.

nothing to commit ...		// commit할 파일, stage에 올라온 파일이 없다.
```

새 파일 README를 생성한 후 상태를 조회하면 아래와 같은 메세지를 확인할 수 있다

```powershell
$ echo 'My Project' > README
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README

nothing added to commit but untracked files present (use "git add" to track)
```

Untracked file README 가 존재한다는 것을 알 수 있다

git status 메세지로 파일 상태 확인하기
> Changes not staged for commit ... : 수정만 된 **modified** 상태
> Changes to be committed ... : commit 직전, **staged** 상태
> nothing to commit, working tree clean ... : **unmodified** 상태

## Staging : Untracked → Staged, Modified → Staged

git add {filename} 명령어를 통해 파일의 현재 상태를 Stage로 올리는 staging을 한다

새로 생성한 파일(Untracked) 또는 작업 공간에서 수정한 파일(Modified)을 Stage 상태로 변경하는 것을 **Staging**이라고 한다

Staging 이후 파일의 상태를 조회하면 아래와 같은 메세지를 확인할 수 있다

```powershell
$ git add README

$ git status
On branch master	// master 브랜치다

No commits yet		// 아직 commit 된 건 없고

Changes to be committed:	// commit 될 변경 사항은 다음과 같다.
  (use "git rm --cached <file>..." to unstage)
        new file:   README		// 새로운 파일 README!
```

commit되지 않은 수정사항이 있다는 것을 알 수 있다

만약 staging 이후에 파일을 수정하였다면 git add 명령어를 통해 현재 파일의 상태를 다시 staging 해주어야한다

## Commit : Stage → Version

git commit 명령어를 통해 stage에 올라온 파일을 version으로 만들어 저장하며, 이때 버전은 local repository에 저장된다

이때 -m 옵션과 함께 사용하여 커밋 메세지도 함께 저장하는 것이 보통이다

```powershell
$ git commit -m "commit message"
[master (root-commit) 3f08ac6] commit message
 1 files changed, 2 insertions(+)
 create mode 100644 README
 create mode 100644 modify-test

$ git status
On branch master
nothing to commit, working tree clean
```

작업 공간에 있는 파일이 전부 version으로 저장된 것을 확인할 수 있다

-a 옵션을 이용하면 modified 파일을 staged로 만들어 준 후 commit 하도록 할 수 있다

—amend 옵션을 이용하여 변경사항에 마지막 커밋을 덮어씌울 수 있는데, push한 변경사항에 대해서는 사용하지 않도록 한다

## Diff : 파일 비교하기

staged 상태와 unstaged(unmodified, modified) 상태의 차이를 확인할 수 있다

- git diff : staged 상태와 unstaged 상태 비교
- git diff —[staged|cached] : staged 상태와 commited 상태 비교
- git diff —name-only HEAD~1 HEAD : 변경된 파일만 비교

## rm : 파일 삭제하기

- git rm {filename} : 실제 디렉토리의 파일을 삭제하고 staged 상태로 변경
- git rm —cached : 실제 디렉토리의 파일은 삭제하지 않고 GIT 내에서만 삭제

## 영역에 대해 잠시..

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e465d8bf-622e-4e5b-bdd3-0c9bd410a790/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20211027%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211027T130851Z&X-Amz-Expires=86400&X-Amz-Signature=1547e4c933832f0ed140da6a3eccb079600dbaafcdb357de3d681cfb538e3b77&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

- **Working Directory**
로컬에서 실제 작업하고 있는 작업 공간이자, working tree라고도 불린다
- **Staging Area / Index / Stage**
버전으로 만들 파일이 대기하는 곳
 .git/index 파일에 저장
- **Local Repository**
stage에서 대기하고 있던 파일들을 버전으로 만들어 저장하는 곳
프로젝트의 메타 데이터 및 객체 오브젝트들이 저장되어 있다
.git/HEAD 파일에 저장
    - **Branch** : 브랜치에 담긴 마지막 커밋
    - **Head** : 현재 브랜치를 가르키는 포인터, 마지막 커밋의 스냅샷
    - HEAD~{num} : HEAD 로부터 num번째 이전 커밋
    - HEAD ^{num} : HEAD의 부모들

∴ Working directory에서 파일을 수정한 후, 이를 Staging Area로 보내어 Git이 관리하게 되며 이때 파일의 스냅샷이 생긴다. 이 영역의 파일들을 commit하여 Git directory에 영구적인 스냅샷으로 남게 된다.

## Push : Local repository → Remote repository

git push <저장소명> <브랜치명> 명령어를 통해 로컬 저장소에 저장된 코드 변경이력을 원격 저장소에 저장한다

## Pull : Remote repository → Local repository

git push <원격 저장소명> <브랜치명> 명령어를 통해 원격저장소에서 로컬저장소로 업데이트한다

원격 저장소에서 마스터 파일을 가져오는 Fetch 작업과 충돌없이 병합하는 Merge 작업이 함께 일어난다 Git pull = git fetch + git merge FETCH_HEAD

- Merge 없이 최신 업데이트 상황을 반영하고자 할떄는 git fetch <원하는 저장소 이름> 명령어를 이용할 수 있다
- 현재 체크아웃된 브랜치를 기준으로 name 을 merge 하고자 할 때에는 git merge <branch-name> 명령어를 사용한다

## Reset

Staging Area와 Working Directory의 상태를 원래대로 돌려놓는다

이때, HEAD를 기준으로 둘 수도 있고, 브랜치를 기준으로 두어 그 브랜치의 마지막 커밋으로 HEAD를 변경할 수 도 있다

git reset —mixed 명령어를 통해 HEAD를 옮기고 staging area를 비우며, working directory는 유지된다

git reset —soft 명령어를 통해 HEAD를 옮기고, staging area와 working directory는 유지한다 

git reset —hard 명령어를 통해 HEAD를 옮기고 staging area를 비우며 working directory를 해당 스냅샷으로 되돌린다

## Checkout

git checkout {branch} 명령어를 통해 HEAD를 브랜치의 HEAD로 옮기며, 브랜치도 변경된다

reset과 유사해 보이지만, rest 는 HEAD를 옮겨서 staging area를 업데이트하는 것에 중심을 두는 반면, checkout은 working directory를 업데이트하는 것이 주요 목적이다

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d323ca89-5f4b-4da6-9336-d1738b9a231d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20211027%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211027T130903Z&X-Amz-Expires=86400&X-Amz-Signature=28a15cb77a617079082b8c54e09b678325e41c029dff179e1e9084deffc091a6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

위 그림에서처럼 명령어를 통해 HEAD가 commit A를 가르키도록 변경하고자 할 때,
reset 의 경우 HEAD가 가르키고 있는 develop branch를 commit A로 변경하며,
checkout의 경우 HEAD자체가 다른 브랜치를 가르키게 된다

# Branch : Workflow 여러개 관리하기

브랜치를 통해 여러개의 workflow를 만들 수 있다

## 브랜치 관리하기

git branch {name} 명령어를 통해 name 브랜치를 생성한다

-d 옵션을 사용하여 name 브랜치를 삭제할 수 있다

-a 옵션을 사용하여 remote와 local 브랜치를 전부 볼 수 있다

git checkout -b {name} 명령어를 이용하여 name 브랜치를 생성하고 해당 브랜치로 checkout하도록 할 수 있다

git branch —contains {SHA} 명령어를 통해 해당 커밋이 포함된 브랜치를 찾을 수 있다

## 브랜치의 종류

### Master Branch

**제품으로 출시**될 수 있는 브랜치

- 배포(Release) 이력을 관리하기 위해 사용한다
- 배포 가능한 상태만을 관리한다

### Develop Branch

**다음 출시 버전을 개발**하기 위한 브랜치

![https://gmlwjd9405.github.io/images/types-of-git-branch/develop-branch.svg](https://gmlwjd9405.github.io/images/types-of-git-branch/develop-branch.svg)

- 기능 개발을 위한 브랜치들을 병합하기 위해 사용한다
- 모든 기능이 추가되고 버그가 수정되어 배포가 가능한 develop branch는 master branch에 merge된다

## Feature Branch

**기능을 개발**하는 브랜치

![https://gmlwjd9405.github.io/images/types-of-git-branch/feature-branch.svg](https://gmlwjd9405.github.io/images/types-of-git-branch/feature-branch.svg)

- 새로운 기능 개발 및 버그 수정이 필요할 때 마다 develop branch로부터 분기한다
- 공유할 필요가 없는 작업이 이루어지므로, local repository에서 관리한다
- 개발이 완료된 feature branch는 develop branch로 merge하여 다른 사람들과 공유한다

**Feature branch 의 생성 및 종료 과정**

```powershell
// feature 브랜치(feature/login)를 'develop' 브랜치('master' 브랜치에서 따는 것이 아니다!)에서 분기
$ git checkout -b feature/login develop

/* ~ 새로운 기능에 대한 작업 수행 ~ */

/* feature 브랜치에서 모든 작업이 끝나면 */
// 'develop' 브랜치로 이동한다.
$ git checkout develop
// 'develop' 브랜치에 feature/login 브랜치 내용을 병합(merge)한다.
# --no-ff 옵션: 아래에 추가 설명
$ git merge --no-ff feature/login
// -d 옵션: feature/login에 해당하는 브랜치를 삭제한다.
$ git branch -d feature/login
// 'develop' 브랜치를 원격 중앙 저장소에 올린다.
$ git push origin develop
```

—no-ff 옵션을 통해 feature branch에 존재하는 모든 commit 이력을 합쳐서 새로운 커밋 객체를 만들어 develop branch에 merge한다

### Release Branch

**이번 출시 버전을 준비**하는 브랜치

![https://gmlwjd9405.github.io/images/types-of-git-branch/release-branch.svg](https://gmlwjd9405.github.io/images/types-of-git-branch/release-branch.svg)

- 배포를 위한 전용 브랜치이다
- 한 팀은 배포를 위한 준비를 이 브랜치에서 하고, 다른 팀은 다른 배포를 위해 다른 브랜치에서 기능 개발을 계속 할 수 있다
- develop branch에서 배포할 기능들이 완성되면 release branch를 분기한다
    - Release branch를 만드는 순간부터 배포 사이클이 시작된다
    - 최종 배포를 위해 버그 수정, 문서 추가 등 release와 직접적으로 관련된 작업을 한다
    - 이러한 작업을 제외하고는 release branch에 merge하지 않는다
- release branch아 배포 가능한 상태가 되면 master branch에 merge한다
- 배포 준비 중에 release branch가 변경되었을 수 있으므로 develop branch에 merge한다

**Release branch 생성 및 종료 과정**

```powershell
// release 브랜치(release-1.2)를 'develop' 브랜치('master' 브랜치에서 따는 것이 아니다!)에서 분기
$ git checkout -b release-1.2 develop

/* ~ 배포 사이클이 시작 ~ */

/* release 브랜치에서 배포 가능한 상태가 되면 */
// 'master' 브랜치로 이동한다.
$ git checkout master
// 'master' 브랜치에 release-1.2 브랜치 내용을 병합(merge)한다.
# --no-ff 옵션: 위의 추가 설명 참고
$ git merge --no-ff release-1.2
// 병합한 커밋에 Release 버전 태그를 부여한다.
$ git tag -a 1.2

/* 'release' 브랜치의 변경 사항을 'develop' 브랜치에도 적용 */
// 'develop' 브랜치로 이동한다.
$ git checkout develop
// 'develop' 브랜치에 release-1.2 브랜치 내용을 병합(merge)한다.
$ git merge --no-ff release-1.2
// -d 옵션: release-1.2에 해당하는 브랜치를 삭제한다.
$ git branch -d release-1.2
https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html
```

### Hotfix Branch

**출시 버전에서 발생하는 버그를 수정**하는 브랜치

![https://gmlwjd9405.github.io/images/types-of-git-branch/hotfix-branch.svg](https://gmlwjd9405.github.io/images/types-of-git-branch/hotfix-branch.svg)

- 배포 전 긴급하게 수정을 해야하는 사항이 발견되는 경우 develop branch에서 분기하여 수정하면 배포 가능한 버전을 만드는데에 시간이 많이 소요되고 안정성을 보장하기 어렵다
- 따라서 이 경우 master branch에서 분기한다
- 수정 완료 후 master branch에 merge한 후 배포가 이루어진다
- hotfix branch에서 변경된 부분은 develop branch에도 반영하기 위해 merge한다

**Hotfix branch 생성 및 종료 과정**

```powershell
// release 브랜치(hotfix-1.2.1)를 'master' 브랜치(유일!)에서 분기
$ git checkout -b hotfix-1.2.1 master

/* ~ 문제가 되는 부분만을 빠르게 수정 ~ */

/* 필요한 부분을 수정한 후 */
// 'master' 브랜치로 이동한다.
$ git checkout master
// 'master' 브랜치에 hotfix-1.2.1 브랜치 내용을 병합(merge)한다.
$ git merge --no-ff hotfix-1.2.1
// 병합한 커밋에 새로운 버전 이름으로 태그를 부여한다.
$ git tag -a 1.2.1

/* 'hotfix' 브랜치의 변경 사항을 'develop' 브랜치에도 적용 */
// 'develop' 브랜치로 이동한다.
$ git checkout develop
// 'develop' 브랜치에 hotfix-1.2.1 브랜치 내용을 병합(merge)한다.
$ git merge --no-ff hotfix-1.2.1
https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html
```

# Github

Git이 로컬에서 관리되는 VCS 툴이라면, Github는 클라우드 방식으로 관리되는 VCS 서비스이다

오픈소스를 공유하고 다른사람들과 협업하기 위해서는 깃허브에서 제공해주는 클라우드 서버를 이용하는 것이 편리하다

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/44c67915-49f5-47a8-b414-c5b71356abe7/Untitled.png)

# 기타

- git init : GIT 디렉토리 시작
- git branch : 새로운 브랜치 생성

### log 명령어

커밋 히스토리를 조회한다

이때 **Author**는 실제로 커밋을 한 사람, **Commiter**는 커밋을 GIT Repo에 저장한 사람을 지칭한다

- git log : 기본적인 조회
- git log —online : 깔끔하게 조회
- git log -p : 각 커밋의 diff도 조회
- git log —stat : 변경된 파일 수, inserted/deleleted line수 등 각 커밋의 통계도 조회
- git log —graph : 브랜치와 머지의 히스토리도 그래프 모양으로 조회

## .gitignore

자동생성되는 파일 등을 git이 무시하도록 하는 파일으로, 수동으로 생성할 수 있다

glob 패턴을따른다

- *.a : 확장자가 a인 파일 무시
- !lib.a : 확장자가 a이여서 위 명령어로 인해 무시되어야하지만, 파일명이 lib인 파일은 무시X
- build/ : build 하위 디렉토리에 있는 파일 무시
- /TODO : 현대 디렉토리의 TODO 파일 무시 (TODO 하위 디렉토리를 무시하는 것이 아님)
- doc/*.txt : doc 하위 디렉토리에서 확장자가 txt인 파일 무시
- doc/**/*.pdf : doc 하위 디렉토리 아래의 모든 pdf 파일 무시

# 비고

## 참고한 자료

- [https://ko.wikipedia.org/wiki/깃_(소프트웨어)](https://ko.wikipedia.org/wiki/%EA%B9%83_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4))
- [https://codedosa.com/653](https://codedosa.com/653)
- [https://www.slideshare.net/ky200223/git-89251791](https://www.slideshare.net/ky200223/git-89251791)
- [https://velog.io/@soyi47/GitGithub-staging-commit](https://velog.io/@soyi47/GitGithub-staging-commit)
- [https://velog.io/@lucasonestar/Git-work-flow-Fundamental-Command](https://velog.io/@lucasonestar/Git-work-flow-Fundamental-Command)
- [https://newbedev.com/what-s-the-difference-between-git-reset-and-git-checkout](https://newbedev.com/what-s-the-difference-between-git-reset-and-git-checkout)
- [https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html](https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html)

## 참고할만한 자료

- Github Cheat Sheet

[GitHub - tiimgreen/github-cheat-sheet: A list of cool features of Git and GitHub.](https://github.com/tiimgreen/github-cheat-sheet)

- Git에 대해 알아보자!

[https://www.slideshare.net/ky200223/git-89251791](https://www.slideshare.net/ky200223/git-89251791)

- Commit을 하기 위한 방법론 3가지 비교

[https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)
