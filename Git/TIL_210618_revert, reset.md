# Git Reset, Revert

현재 근무 중인 회사에서는 Gitlab을 통해 소스 코드 관리를 한다.
Intellij와 GitKraken이라는 아주 편리한 툴을 사용해서 하는데, 
GitKraken은 git의 여러 명령어와 그래프를 확인할 수 있는 툴이다.
git으로 소스 코드를 관리한다면, bash로 git 연습 후 사용하면 좋을 것 같다

사실 오늘 이 글을 쓰게 된 이유는 회사에서 프로젝트를 하면서 각자 브랜치를 따고, merge를 하는 방식으로 진행을 하는데, 항상 push나 merge 하기 전, 부모 branch로부터 pull을 받고 rebase를 하는 방식을 써와서 그동안 history 관리에 큰 어려움이 없었다.
그런데 오늘 수석님이 pull을 하기 전 push와 merge를 하면서 커밋 이력 그래프가 보기 힘들게 되어 그 부분을 도와 드리면서 아직 git에 대한 이해가 부족하다는 것을 알게 되었다.




git revert와 reset 모두 과거 커밋을 건드리는 작업이다.
(잘 못 실행시키면 커밋이 다 날라가는 낭패를 볼 수 있는 작업이니 할 때 특별히 유의해서 하는 것을 추천한다ㅎㅎ)

차이점부터 말하자면 reset은 시간을 과거의 특정 사건(commit)으로 되돌린다고 생각하고,
revert는 현재에 있으면서 과거의 특정 사건들만을 없던 일로 만드는 거라고 생각하면 된다.

### reset

reset은 특정 사건으로 되돌아가는데 과거로 되돌아 갔으니 해당 사건(commit) 이후의 사건(commit)들은 모두 사라진다.
그리고 과거의 이력이 커밋에 안남고 깔끔히 과거로 돌아가지만 revert는 특정 사건을 revert 했다는 이력이 남는다.

reset에는 세가지 종류가 있다
soft : index 보존 (add한 상태, staged 상태), working directory의 파일 보존. 즉, 모두 보존한다 (commit 하기 전 상태)
mixed : index 취소 (add하기 전 상태, unstaged 상태), working directory의 파일 보존 (기본 옵션) (add 하기 전 상태)
hard : index 취소 (add하기 전 상태, unstaged 상태), working directory의 파일 삭제. 즉, 모두 취소
커밋되었던 소스 코드 다 날라간다

hard 방법으로 reset 하는 것이 가장 깔끔하지만 해당 커밋 이후의 작업들이 모두 사라지므로 확실할때만 신중히 사용하는 것이 좋다
mixed의 경우 soft와 달리 working directory는 유지되지만 index가 취소되므로 취소된 파일들을 다시 add 해줘야 한다


### revert

revert는 특정 사건을 골라서 없던일로 만든다. 또한 과거 특정 사건을 없애주지만 revert 했다는 이력이 남는다. 그리고, 없애려는 특정 사건의 과거와 미래와 얽혀 충돌이 나는 경우가 많다

revert는 이력을 남겨주므로 뭘 고쳤는지 알 수 있다는 점이 장점이다. 
다른 사람과 공유하는 브랜치에서 이전 어떤 커밋을 수정하고 싶을 경우 revert를 사용하는 것이 좋다. (그래야 커밋 히스토리가 바뀌지 않아 충돌이 발생하지 않는다)
