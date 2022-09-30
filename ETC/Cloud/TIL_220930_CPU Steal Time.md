# CPU Steal Time

## CPU Steal Time

- CPU Steal Time은 하이퍼바이저가 다른 가상 프로세서를 서비스 하는 동안 가상 CPU가 실제 CPU를 기다리는 시간을 백분율로 표시한 값입니다. 가상 환경에서 동작하는 VM(Virtual Maching)은 단일 호스트에 있는 다른 인스턴스와 리소스를 공유합니다. CPU Steal Time을 통해 VM에서 동작하는 CPU가 물리 머신으로부터 자원을 할당받기 위해 얼마나 대기하고 있는지 알 수 있습니다.

## CPU Steal Time은 어떻게 확인하나?

Linux에서 top 명령을 실행하면 주요 성능 메트릭의 실시간 보기를 볼 수 있습니다. 아래는 Top 명령어가 실행되었을 때 값입니다. 

```bash
Top - 10:00:00 up 120 days, 7:00, 3 users, load average: 1.15, 0.88, 0.86
Tasks: 122 total, 10 running, 112 sleeping, 0 stopped, 0 zombie
%Cpu(s): 40.0%us, 0.0%sy, 0.0%ni, 0.0%id, 0.0%wa, 0,0%hi, 0.1%si, 70.0%st
```

**CPU(s) 항목에 대한 설명은 아래와 같습니다.**

- **us(user) :** 사용자 애플리케이션(non-kernel code)에 의해 사용되는 CPU 시간 비율
- **sy(system) :** 시스템(kernel code)에 의해 사용되는 CPU 시간 비율
- **ni(nice) :** NI 값이 1에서 19사이의 사용자 프로세스에 의해 사용되는 CPU 시간 비율
- **id(idle) :** CPU 휴식 시간 비율
- **wa(wait) :** CPU 사용 대기 시간 비율
- **hi(hard interrupt) :** 바로 실행된 인터럽트 핸들러에서 사용한 시간 비율
- **si(soft interrupt) :** 대기 후 실행된 인터럽트 핸들러에서 사용한 시간 비율
- **st(steal time) :** 가상화 시스템에서 받지 못한(프로세스가 비자발적으로 대기하는 데에 사용한) 시간 비율

## CPU Steal Time이 높은 경우

CPU Steal이 발생하는 이유는 VM이 올라가 있는 물리 장비에 자원이 처음부터 부족하거나 물리 장비에 자원은 충분하지만 VM에 할당된 CPU 자원이 부족하기 때문입니다. VM을 너무 많이 올렸거나 관리자가 VM에서 사용할 수 있는 각각의 리소스 제한 설정을 잘못 한 경우 이런 문제가 발생할 수 있습니다.
