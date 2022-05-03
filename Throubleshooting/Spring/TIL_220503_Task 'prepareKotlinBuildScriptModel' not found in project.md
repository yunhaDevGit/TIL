Gradle을 사용하여 multi module project를 생성하였는데 아래와 같은 에러메시지와 함께 build가 안됐다.

![image](https://user-images.githubusercontent.com/74949294/166398643-aaf16623-9a9c-4abb-9ca1-66775c03f381.png)


```
Task 'prepareKotlinBuildScriptModel' not found in project ':producer'.

* Try:
> Run gradle tasks to get a list of available tasks.
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.

```

왜 Kotlin 에러가 나오지...;;
찾아보니 Kotlin Plugin을 disable 하면 된다길래 plugin을 disable 하고 다시 실행하니 정상적으로 build가 됐다ㅎ

![image](https://user-images.githubusercontent.com/74949294/166399130-4bc565e0-4225-440d-8c17-146be524c564.png)

예전에도 동일한 문제가 있었던 적이 있었는데 그땐 이 방법으로 해결하지 않았던것 같지만,,,우선 disable 하고 나니 정상적으로 실행이 되었다~


