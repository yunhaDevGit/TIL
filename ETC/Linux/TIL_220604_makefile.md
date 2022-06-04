- **Makefile**
    - linux 상에서 반복적으로 발생하는 컴파일을 쉽게 하기 위해서 사용하는 `make` 프로그램의 설정 파일
    - Makefile을 사용하여 library 및 컴파일 환경을 관리할 수 있다
    - 프로그램 개발 시 라인이 길어지게 되면 여러 개의 모듈로 나누어 개발이 진행하게 되는데 이때 입력 파일이 바뀌면 바뀐 파일과 관계 있는 파일 또한 다시 컴파일 해야 하는 불편함이 발생한다. 이때 Makefile을 만들어 빌드(실행 파일 만들기) 시 불편함을 줄일 수 있다.
    
    **Makefile의 기본 구조**
    
    - 파일명 : Makefile
    - 기본 구조 : 매크로 정의, 룰, 명령어
        
        ```makefile
        CC = gcc                            # 매크로 정의
        target1 : dependency1 dependency2   # 룰1
        	command1                          # 명령
        	command2
        
        target2 : dependency3 dependency4 dependency5
        	command3
        	command4
        ```
        
    
    **target**
    
    - 원하는 변수 명 설정
    
    **command**
    
    - 꼭 `Tab`을 사용하여 작성
    - shell 명령어, C언어, Python 등 다양한 언어로 command를 작성할 수 있다
    
    **Prerequisite (전제 조건)**
    
    - target 옆에 작성
    - target을 만들기 위해 필요한 전제 조건을 작성
    
    **동작 원리**
    
    - make 프로그램은 처음으로 인식한 target의 Prerequisite에 접근하고 해당 내용에 적힌 내용이 target이 된 영역으로 가 꼬리에 꼬리를 물어 command가 실행된다.
    - 매크로 이름은 사용자가 설정할 수 있으며 보통 대문자로 작성한다. 
    매크로 이름을 설정했다면 해당 변수에 대응 시키고자 하는 것을 `=` 우측에 작성해준다.
    설정한 매크로에 대응하는 값을 매칭 시켰다면 기본 룰 기반에서 사용할 때 매크로로 설정한 이름을 `$()` 안에 넣어주기만 하면 된다.
    
    ```makefile
    TARGET = test # 매크로 정의
    
    all : $(TARGET) # 매크로 사용
    ```
    
    - `$@`와 `$^`의 사용
        - `$@`는 해당 블록의 target을 의미하는 표시
        - `$^`는 prerequisite 영역을 의미
    - `.PHONY` 사용
        - target으로 작성한 이름과 디렉토리 내에 파일 이름이 서로 같을 경우 make 프로그램이 오작동이 일어나지 않도록 `.PHONY` 작성
        - `phony = 가짜`라는 의미로 phony target은 실제 파일 이름이 아닌 target을 의미한다. make 명령이 실행되는 디렉토리에 Makefile의 target과 같은 이름의 파일이 존재할 경우 충돌이 발생하는데 .PHONY에 명시하여 이를 피할 수 있다.
