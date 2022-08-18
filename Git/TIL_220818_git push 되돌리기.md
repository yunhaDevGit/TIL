# Git Push 되돌리기

### 원하는 시점 또는 바로 최근 Commit으로 돌리기
```BASH
# 가장 최근의 commit을 취소 (기본 옵션: --mixed)
$ git reset HEAD^

# 원하는 시점으로 워킹 디렉터리를 되돌린다.
$ git reset HEAD@{number} 
# 또는
$ git reset [commit id]
```

### 수정한 후 다시 add, commit
수정하고 싶은 부분을 수정한 후에 다시 add & commit
```BASH
# add
$ git add .

# commit
$ git commit -m "Write commit messages"
```

### 원격 저장소에 강제 push
```BASH
$ git push origin [branch name] -f
# 또는
$ git push origin +[branch name]
 
 
# master branch를 원격 저장소(origin)에 강제로 push
$ git push origin +master
```
