---
layout: post
title: "익숙해지면 좋을 깃(git) 명령어 모음집"
date: 2021-01-15 10:00:00
categories: dev
tags: Git
cover: "/assets/21_01_GitCommand/21_01_thumbnail.png"
---

<img src="/assets/21_01_GitCommand/21_01_thumbnail.png" width="" alt="Git Command">

안녕하세요. API팀 김세현입니다. 어반베이스에서는 Server Side 설계, API 개발을 담당하고 있습니다. git을 항상 사용하고 있지만 스스로 기본적인 이해가 부족한 것 같아, 공부하는 마음으로 주요 명령어를 한번 정리해보았습니다. 모든 명령어 뒤에 `--help` 옵션을 사용하여 활용 가능한 옵션을 알 수 있습니다.

---

## git init

git 초기화. git으로 버전관리를 할 경로에서 사용합니다. `.git` 폴더가 생성 되며 아래와 같은 구조입니다.

```
HEAD
config
description
/branches
/hooks
/objects
/refs
```

---

## git status

현재 git 프로젝트에서 파일들의 상태를 보여줍니다. git의 영역과 상태에 대한 자세한 설명은 아래의 두 문서를 참고해 주세요.

- [https://dololak.tistory.com/303](https://dololak.tistory.com/303)
- [https://dololak.tistory.com/304](https://dololak.tistory.com/304)

---

## git add

파일의 변경 내용을 스테이징 영역(staging area)에 추가하기 위해 사용하는 명령어입니다. 스테이징 영역으로 추가된 변경 이력만 `commit`할 수 있습니다.

```bash
# 모든 변경점 추가
git add -A

# 현재 경로의 모든 변경점 추가
git add .

# README.md 파일만 추가
git add README.md
```

---

## git rm

파일을 지우거나 스테이지에서 해제할 때 사용합니다.

```bash
# 파일 삭제
git rm README.md

# README.md 파일을 추적되지 않은 상태로 만듦
git rm --cached README.md
```

---

## git restore (2.23)

워킹 트리(Working tree)의 변경된 파일을 복원해 주는 역할을 합니다.

```bash
# Unstaged 상태의 변경 파일을 원상복구
git restore [파일명]

# git add로 Staging 된 파일을 Unstaged 상태로 되돌림
git restore --staged [파일명]
```

---

## git clean

추적되지 않은 상태(`untracked`)의 파일을 삭제합니다. 삭제가 되면 복구할 수 없으니 `stash` 를 고려해보는 것도 좋습니다.

```bash
# 디렉토리를 제외한 파일만 삭제
git clean -f 

# 디렉토리포함 삭제
git clean -f -d

# .gitignore 에 설정된 파일도 삭제
git clean -f -d -x

# 가상 실행
git clean -n
```

---

## git commit

변경된 내용을 저장합니다.

```bash
# 메시지와 함께 커밋
git commit -m 'First Commit'

# 신규 파일을 제외한 변경사항을 Staging 후 커밋
git commit -a

# 이전 커밋 변경
git commit --amend
```

---

## git log

`commit` 목록을 볼 수 있습니다. 굉장히 많은 옵션이 있으니 `git log --help` 명령어로 자신에게 맞는 조합을 활용하시기 바랍니다.

```bash
# branch 그래프를 추가하여 보기
git log --graph

# 모든 branch 보기
git log --all

# commit 메시지 제목만 한줄로 보기
git log --oneline
```

---

## git show

`commit` 의 상세 정보를 확인합니다.

```bash
# 현재 branch의 가장 최근 commit 정보를 확인
git show

# 특정 commit 정보를 확인
git show [commit 해시값]

# 특정 branch의 가장 최근 commit 정보를 확인
git show [branch 명]
```

---

## git reset HEAD [file]

- `commit` 을 취소할 수 있습니다.

    ```bash
    # commit을 취소하고 해당 파일들은 스테이징 영역에 보존
    git reset --soft HEAD^

    # commit을 취소하고 해당 파일들은 Unstaging
    git reset --mixed HEAD^
    git reset HEAD^

    # commit을 취소하고 해당 파일들의 변경점 삭제
    git reset --hard HEAD^
    ```

- `push`를 취소할 수 있습니다.

    ```jsx
    git reset HEAD^
    git push -f origin 브랜치명
    git pull
    ```

---

## git remote

원격 저장소(remote repository)를 관리하는 명령어 입니다.

```bash
# 설정된 원격 저장소 보기
git remote -v

# test 라는 이름으로 원격 저장소 추가하기
git remote add test https://github.com/test/test
```

---

## git push

원격 저장소(remote repository)에 코드 변경분을 업로드합니다.

```bash
# 기본 사용법
git push [저장소명] [branch]

# 최초 1회 저장소, branch 지정. 이 후, 생략 가능
git push -u [저장소명] [branch]

# 로컬에서 생성한 branch를 push
git push --set-upstream [저장소명] [branch]
```

---

## git branch

branch에 관련한 명령어 입니다.

```bash
# 로컬 branch 목록 확인
git branch

# 원격 저장소를 포함한 모든 branch 목록 확인
git branch -a

# test 라는 branch 생성하기
git branch test

# test 로컬 branch를 origin이라는 원격 저장소의 test branch에 연결
git branch --set-upstream-to=origin/test test

# test branch 삭제
git branch -d test

# test branch 강제 삭제
git branch -D test
```

---

## git switch (2.23)

branch를 변경하는 명령어 입니다. `checkout` 에서 복원하는 기능을 제거했다고 합니다.

```bash
# test branch로 변경하기
git switch test

# test2 라는 branch를 새로 생성하고 test2 branch로 변경하기
git switch -c test2
```

---

## git checkout

branch를 변경하고 워킹 트리(Working tree)에서 변경점을 복원하는 명령어 입니다. `switch, restore` 명령어를 사용하는 것을 추천합니다.

```bash
# test branch로 변경하기
git checkout test

# test2 라는 branch를 새로 생성하고 test2 branch로 변경하기
git checkout -b test2

# Unstaged 상태의 파일을 원래대로 되돌림
git checkout -- [파일명]

# Unstaged 상태의 현재 경로의 모든 파일을 원래대로 되돌림
git checkout -- .
```

---

## git fetch

원격 저장소(remote repository)의 데이터를 가져옵니다. `pull` 로 병합하기 전에 어떤 변경점이 있나 살펴볼 때 사용하기 좋습니다.

```bash
# origin 이라는 원격 저장소의 데이터를 가져옴
git fetch origin

# 모든 원격 저장소의 데이터를 가져옴
git fetch --all

# 원격 저장소에서 삭제된 branch를 로컬에서도 삭제
git fetch --prune
```

---

## git pull

원격 저장소(remote repository)의 데이터를 가져온 후 로컬 branch에 병합합니다.

```bash
# git fetch --all && git pull 과 같습니다
git pull --all
```

---

## git stash

현재 작업중인 변경점을 임시 저장하거나 불러올 수 있습니다. 현재와 다른 branch로 가서 작업을 하기전에 사용하면 유용합니다.

```bash
# 현재 변경점 testStash 라는 이름으로 저장하기
git stash save testStash

# stash 목록(stack) 확인하기
git stash list

# testStash 라는 stash를 불러와 적용하기
git stash apply testStash

# testStash 라는 stash를 불러와 적용하는데, Staged 상태까지 적용하기
git stash apply testStash --index

# 가장 최근의 stash를 가져와 적용하고 스택에서 삭제하기
git stash pop

# 가장 최근의 stash 제거하기
git stash drop

# testStash라는 stash를 제거하기
git stash drop testStash
```

---

## git blame

특정 파일의 수정 이력을 확인할 수 있습니다. 각 라인별로 누가, 언제 마지막으로 수정 했는지 알 수 있습니다.

```bash
# test.txt 파일의 수정 이력을 확인
git blame test.txt

# test.txt 파일의 5부터 10번 라인까지만 확인
git blame -L 5,10 test.txt

# 파일명이 변경 되었다면, 변경전의 파일명과 함께 확인
git blame -C newTest.txt

# 공백 변경을 무시
git blame -w test.txt
```

---

## git diff

소스를 비교하여 볼 수 있습니다.

```bash
# 마지막으로 커밋된 소스와 현재 Unstaged 상태의 변경점과 비교
git diff

# 마지막으로 커밋된 소스와 현재 Staging 된 변경점과 비교
git diff --staged

# 커밋간 비교
git diff [커밋해시1]..[커밋해시2]

# branch간 비교
git diff [branch1] [branch2]
```

---

## git revert

지정한 커밋으로 되돌려 커밋합니다. 되돌린 이력이 남기때문에 충돌이 발생할 위험이 적으며 협업 상황에서 사용하는 것을 추천합니다.

```bash
# 특정 커밋으로 되돌리고 커밋
git revert [커밋해시]

# 특정 태그로 되돌리고 커밋
git revert [태그명]

# 특정 커밋으로 되돌리지만 커밋은 안한채로 Staging 상태
git revert [커밋해시] -n

# 병합한 커밋으로 되돌릴 때 메인이 되는 커밋을 지정하여 되돌리기
git revert [커밋해시] -m 1
```

---

## git tag

특정 커밋에 표기하는 기능입니다. 주로 릴리즈 시 이용합니다. 두 가지 종류가 있는데 Lightweight 태그와 Annotated 태그입니다. Lightweight 태그는 단순히 버전등의 이름을 남길 때 사용합니다. Annotated 태그는 만든 사람의 이름, 이메일, 날짜, 메시지까지 저장하며 GPG(GNU Privacy Guard)로 서명까지 가능합니다.

```bash
# 현재 HEAD에 v1.0.0이라는 Lightweight 태그 생성
git tag v1.0.0

# 현재 HEAD에 v1.0.0이라는 Annotated 태그 생성
git tag -a v1.0.0 -m 'message'

# 특정 커밋에 v1.0.0이라는 Lightweight 태그 생성
git tag v1.0.0 [커밋해시]

# v1.0.0 태그를 원격 저장소에 푸시하기
git push origin v1.0.0

# 모든 로컬 태그를 원격 저장소에 푸시하기
git push origin --tags

# 로컬의 v1.0.0 삭제
git tag -d v1.0.0

# 원격 저장소의 v1.0.0 태그 삭제
git push -d origin v1.0.0
```

---

## git merge

현재 브랜치를 특정 브랜치의 소스와 병합합니다. `merge` 하기 전엔 워킹 디렉토리를 `stash` 등으로 깔끔하게 정리하고 진행하는 것을 추천합니다.

```bash
# master branch를 병합
git merge master

# 병합 충돌(Conflict) 발생 시 취소
git merge --abort

# 공백으로 인한 병합 충돌을 무시하고 병합
git merge -Xignore-all-space
```

---

## git rebase

저는 `rebase`를 쓰지 않지만, 알아두면 좋으니 간단하게 정리하겠습니다. merge처럼 branch를 합치는 기능을 하는데요. 작동 방식이 merge 와는 다릅니다. 자세한 설명과 개념은 아래 링크의 문서로 대신 하겠습니다.

- [https://git-scm.com/book/ko/v2/Git-브랜치-Rebase-하기](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase-%ED%95%98%EA%B8%B0)

이런 경우에 사용하면 좋을 것 같습니다.

- 예) master 브랜치에서 feature 브랜치로 분기하여 커밋 후, master 브랜치의 새로운 커밋이 있을 때 feature 브랜치가 master 브랜치의 새로운 커밋을 병합해야 할 때, merge 커밋을 남기기 싫을 경우.
- 쉽게 말해서, 작업하는 branch를 병합할 branch에 병합 이력없이 변경 커밋만 남기고 싶을 때 사용하면 좋습니다.

```bash
# 모든 원격 저장소의 데이터를 가져옴(최신화)
git fetch --all

# 원격 저장소(origin)의 master 브랜치로 rebase
git rebase origin/master  # git merge origin/master 와 결과는 같으나 병합 이력 커밋이 없다
```

중요한 점은 원격 저장소에 올라가 사용중인 커밋을 rebase 하는 것은 최대한 지양해야 한다는 것입니다.

---

## 마치며

git의 강력하고 다양한 기능을 전부 다루진 못했습니다. 그래도 이 정도만 숙지해도 개발하는데 적지 않은 도움이 될 거라 조심스레 생각합니다. 

git을 GUI(소스트리)툴로 처음 접했습니다. 그런데 현재는 터미널에서 써드파티 앱의 도움과 명령어로 git을 사용하고 있습니다. GUI 툴은 마우스로 클릭해서 조작하기 편하고 눈으로 보기 좋은 장점이 있습니다만, 조금 느리고 답답하다는 느낌이 들 때가 있었기 때문입니다. 저와 같은 불편함을 겪었거나 아직 CLI환경에 익숙하지 않다면, 이제부터는 직접 명령어를 입력하고 그러한 명령어를 모아 자동화하는 재미를 찾아보는 것은 어떨까요?

---

## 부록 및 참고

- [https://git-scm.com/book/ko/v2](https://git-scm.com/book/ko/v2)
- [터미널 shell 환경을 개선해주는 zsh관련 설정](https://tutorialpost.apptilus.com/code/posts/tools/using-zsh-oh-my-zsh/)
