# [Spring Batch] 배치 어플리케이션?

## 배치 어플리케이션이란?

배치(Batch)란 **일괄 처리**란 뜻을 가지고 있습니다. 

매일 전 날의 데이터를 집계해야 한다고 할 때 웹 어플리케이션(Tomcat + Spring MVC)을 사용하여 처리를 하게 된다면 큰 데이터를 읽고, 가공하고, 저장하면서 해당 서버는 순식간에 CPU, I/O 등의 자원을 다 써버려서 다른 Request 처리를 못하게 될 수 있습니다. 

이렇듯 단발성으로 대용량의 데이터를 처리하는 어플리케이션을 **배치 어플리케이션**이라고 합니다.

웹 애플리케이션을 개발할 때 **Spring MVC를 사용함으로 비즈니스 로직에 최대한 집중** 할 수 있었던 것처럼 **Spring Batch를 사용하여 배치 프로그램의 비즈니스 로직에 집중할 수 있습니다.**

**배치 애플리케이션은 아래의 조건을 만족해야 합니다.**

- 대용량 데이터 - 배치 어플리케이션은 대량의 데이터를 가져오거나, 전달하거나, 계산하는 등의 처리를 할 수 있어야 합니다.
- 자동화 - 배치 어플리케이션은 심각한 문제 해결을 제외하고는 **사용자 개입 없이 실행** 되어야 합니다.
- 견고성 - 배치 어플리케이션은 잘못된 데이터를 충돌/중단 없이 처리할 수 있어야 합니다.
- 신뢰성 - 배치 어플리케이션은 무엇이 잘못되었는지를 추적할 수 있어야 합니다 (로깅, 알림)
- 성능 - 배치 어플리케이션은 **지정한 시간 안에 처리를 완료**하거나 동시에 실행되는 **다른 어플리케이션을 방해하지 않도록 수행**되어야 합니다.

## Spring Batch?

Spring Batch는 Accenture의 배치 노하우와 Spring Framework가 함께 만들었습니다. 

Spring Batch는 Spring의 특성을 그대로 가져와 **DI, AOP, 서비스 추상화** 등 Spring의 3대 요소를 모두 사용할 수 있습니다.

Spring Batch 4.0에서 지원하는 Reader & Writer는 아래와 같습니다. (Reader : 데이터 읽어오는 모듈, Writer : 데이터 쓰는 모듈)

| DataSoruce | 기술 | 설명 |
| --- | --- | --- |
| Database | JDBC | 페이징, 커서, 일괄 업데이트 등 사용 가능 |
| Database | Hibernate | 페이징, 커서 사용 가능 |
| Database | JPA | 페이징 사용 가능(현재 버전에선 커서 없음) |
| File | Flat file | 지정한 구분자로 파싱 지원 |
| File | XML | XML 파싱 지원 |

## Batch vs Quartz

Spring Batch와 Spring Quartz는 역할이 완전히 다릅니다. 

**Quartz는 스케줄러의 역할**이지, Batch와 같이 **대용량 데이터 배치 처리에 대한 기능을 지원하지 않습니다.** 

반대로 Batch 역시 Quartz의 다양한 스케줄 기능을 지원하지 않아 보통은 Quartz + Batch를 조합해서 사용합니다.

정해진 스케줄마다 Quartz가 Spring Batch를 실행하는 구조라고 생각하면 됩니다.

## Spring Batch 용어

**Job**

- Job은 배치 처리 과정을 하나의 단위로 만들어 놓은 객체입니다. 또한 배치 처리 과정에 있어 최상단에 위치하고 있습니다.

**JobInstance**

- JobInstance는 Job의 실행의 단위를 나타냅니다. Job을 실행시키게 되면 하나의 JobInstance가 생성되게 됩니다.
- 예를들어 1월 1일 실행, 1월 2일 실행을 하게 되면 각각의 JobInstance가 생성되며 1월 1일 실행한 JobInstance가 실패하여 다시 실행을 시키더라도 이 JobInstance는 1월 1일에 대한 데이터만 처리하게 됩니다.

**JobParameters**

- JobParameters는 JobInstance를 구분 하는 객체입니다.
- JobParameters는 JobInstance 구별 외에도 개발자 JobInstance에 전달되는 매개변수 역할도 하고 있습니다.
- JobParameters는 String, Double, Long,Date 4가지 형식만을 지원합니다.

**JobExecution**

- JobExecution은 JobInstance에 대한 실행 시도에 대한 객체입니다.
- 1월 1일에 실행한 JobInstance가 실패하여 재실행을 하여도 동일한 JobInstance를 실행시키지만 두 번 실행에 대한 JobExecution은 별도로 생기게 됩니다.
- JobExecution은 JobInstance 실행에 대한 상태, 시작 시간, 종료 시간, 생성 시간 등의 정보를 담고 있습니다.

**Step**

- Step은 Job의 배치 처리를 정의하고 순차적인 단계를 캡슐화 합니다.
- Job은 최소한 1개 이상의 Step을 가지며, Job의 실제 일괄 처리를 제어하는 모든 정보가 들어있습니다.

**StepExecution**

- StepExecution은 JobExecution과 동일하게 Step 실행 시도에 대한 객체를 나타냅니다.
- Job이 여러 개의 Step으로 구성되어 있을 경우 이전 단계의 Step이 실패하게 되면 다음 단계가 실행되지 않음으로 실패 이후 StepExecution은 생성되지 않습니다.
- StepExecution 또한 JobExecution과 동일하게 실제 시작이 될 때만 생성됩니다.
- StepExecution에는 JobExecution에 저장되는 정보 외에 read 수, write 수, commit 수, skip 수 등의 정보들도 저장 됩니다.

**ExecutionContext**

- ExecutionContext란 Job에서 데이터를 공유할 수 있는 데이터 저장소입니다.
- Spring Batch에서 제공하는 ExecutionContext는 JobExecutionContext, StepExecutionContext 2가지 종류가 있으나 이 2가지는 지정되는 범위가 다릅니다.
    - JobExecutionContext의 경우 Commit 시점에 저장됩니다.
    - StepExecutionContext는 실행 사이에 저장됩니다.
        - ExecutionContext를 통해 Step간 Data 공유가 가능하며 Job 실패 시 ExecutionContext를 통한 마지막 실행 값을 재구성할 수 있습니다.
        

**JobRepository**

- JobRepository는 위에서 말한 모든 배치 처리 정보를 담고 있는 매커니즘입니다.
- Job이 실행되게 되면 JobRepository에 JobExecution과 StepExecution을 생성하게 되면 JobRepository에서 Execution 정보들을 저장하고 조회하며 사용하게 됩니다.

**JobLauncher**

- JobLauncher는 Job과 JobParameters를 사용하여 Job을 실행하는 객체입니다.

**ItemReader**

- ItemReader는 Step에서 Item을 읽어오는 인터페이스입니다.
- ItemReader에 대한 다양한 인터페이스가 존재하며 다양한 방법으로 Item을 읽어올 수 있습니다.

**ItemWriter**

- ItemWriter는 처리 된 Data를 Writer 할 때 사용합니다.
- Writer는 처리 결과물에  따라 Insert가 될 수도 Update가 될 수도 Queue를 사용한다면 Send가 될 수도 있습니다.
- Writer 또한 Read와 동일하게 다양한 인터페이스가 존재합니다. Writer는 기본적으로 Item을 Chunk로 묶어 처리합니다.

**ItemProcessor**

- ItemProcessor는 Reader에서 읽어온 Item을 데이터를 처리하는 역할을 합니다.
- Processor는 배치를 처리하는데 필수 요소는 아니며 Reader, Writer, Processor 처리를 분리하여 각각의 역할을 명확하게 구분합니다.

## Spring Batch 사용

Spring Batch에서의 Job은 여러가지 Step의 모음으로 구성되어 있으며 Job은 순차적인 Step을 수행하며 Batch를 수행합니다. Step은 Tasklet 처리 방식과 Chunk 지향 처리 방식을 지원하고 있습니다.
![image](https://user-images.githubusercontent.com/74949294/174581063-8dafae80-3fd4-4427-a2fd-21b5ed23b4a2.png)

