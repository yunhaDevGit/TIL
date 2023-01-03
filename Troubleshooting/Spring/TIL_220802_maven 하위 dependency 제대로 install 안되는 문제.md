# Maven dependency가 제대로 추가되지 않는 문제

maven 기반 project를 실행하면서 며칠 간 헤맸던 부분에 대해 작성하려고 한다.

maven 기반 모듈을 실행시키기 위해 `clean -> install` 작업을 진행 후 실행을 하였으나, 특정 작업에서 계속해서 ClassNotFoundException이 발생했다. openshift와 kubernetes 패키지의 클래스를 찾지 못하는 문제였다. 

그러나 기존에 해당 모듈을 실행했던 pc에선 제대로 동작하는 것을 확인 했고, 해당 pc에서 다시 `clean -> install`을 해보았지만 여전히 잘 됐다. 

![image](https://user-images.githubusercontent.com/74949294/182296430-426a2887-fc43-440a-87c0-633baaff5a5a.png)

```powershell
Caused by: java.lang.NoClassDefFoundError: io/fabric8/openshift/api/model/miscellaneous/cncf/cni/v1/NetworkAttachmentDefinition
	at io.fabric8.openshift.client.OpenShiftExtensionAdapter.<clinit>(OpenShiftExtensionAdapter.java:67)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at java.lang.Class.newInstance(Class.java:442)
	at java.util.ServiceLoader$LazyIterator.nextService(ServiceLoader.java:380)
	... 119 common frames omitted
Caused by: java.lang.ClassNotFoundException: io.fabric8.openshift.api.model.miscellaneous.cncf.cni.v1.NetworkAttachmentDefinition
	at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:418)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:352)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:351)
	... 126 common frames omitted
```

아무래도 maven dependency가 제대로 추가되지 않는 것 같은데 pom.xml이 변경 된 것도 없고 repo도 동일한 곳을 바라보고 있어 원인을 찾기 힘들었다. 

기존에 정상적으로 동작하던 pc에서 혹시나 하는 마음에 Local repository를 새로운 폴더를 바라보도록 설정 한 후, 다시 `clean -> install`을 한 후에 실행시켜 보았다. 

![image](https://user-images.githubusercontent.com/74949294/182296445-221d7c87-8e7a-4036-9171-2047c36bbe09.png)

위와 같이 Local repository 경로를 변경하니, 동일한 Exception이 발생했다. 

다시 기존 Local repository로 경로를 변경하니, 정상적으로 동작했다…

Intellij 이슈일 줄 알았는데…아니었다 🥴 

분명 pom.xml과 모든 설정이 다 똑같은데 새로 maven dependency를 처음부터 install 할 때에만 해당 이슈가 발생했다. 

새로 maven dependency를 추가한 모듈의 `Dependencies`를 확인해 보았는데 아래와 같이 dependency가 추가 되었고 `ClassNotFoundException`이 나는 클래스도 찾을 수 있었다. 

![image](https://user-images.githubusercontent.com/74949294/182296464-25018134-fb66-4bda-8d59-31cea4528c03.png)

제대로 동작하는 local repository를 바라보는 모듈의 `Dependencies`를 확인해보니 위와 같이 추가된 dependency에 하위 dependencies들이 있는 것을 확인할 수 있었다. 

![image](https://user-images.githubusercontent.com/74949294/182296497-9134749d-4c5e-4867-9b0f-8669f8b2aede.png)

이에 새로운 local repository에 정상 동작하는 local repository의 `openshift-client`, `kubernetes-client`폴더를 각각 *copy&paste* 했더니 제대로 동작하는 것을 확인할 수 있었다. 

Local repository의 해당 dependency가 있는 곳의 `패키지.pom` 파일을 확인해보니 Exception이 발생하는 repository(왼쪽 탭)의 `.pom` 파일에는 하위 dependency가 없는 것을 확인할 수 있었다. 

![image](https://user-images.githubusercontent.com/74949294/182296509-cea009fb-0bc4-4064-aa0f-9105df7cc8a5.png)

![image](https://user-images.githubusercontent.com/74949294/182296522-6a6336df-f27d-4d1c-9d68-a47f842c9226.png)

![image](https://user-images.githubusercontent.com/74949294/182296538-2ae10555-6408-4829-8de7-596a75353efc.png)

무슨 이유인지 정확히는 알 수 없으나 해당 패키지의 `.pom` 파일이 달라서 생기는 문제였다. 

혹시나 하위 dependency가 제대로 install 되지 않는다면 `.pom` 파일을 확인해보자!!
