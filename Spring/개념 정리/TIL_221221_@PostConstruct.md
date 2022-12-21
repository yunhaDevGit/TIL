# @PostConstruct

### @PostContruct란?

@PostConstruct는 의존성 주입이 이루어진 후 초기화를 수행하는 메서드입니다.  @PostConstruct가 붙은 메서드는 클래스가 service를 수행하기 전 발생합니다.

이 메서드는 다른 리소스에서 호출되지 않는다 해도 수행 됩니다.
