
Intellij에서 gradle 빌드 도구를 사용하여 multi-module project를 생성 Git push 하기 위해 `VSC -> Enable Version Control Integration -> Git` 을 선택하여 git 기능들을 사용하겠다는 설정을 해주었습니다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2c9db494-f4fc-474f-bca1-64ab45bb444a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220120%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220120T055413Z&X-Amz-Expires=86400&X-Amz-Signature=049ca35865a61ef92a534d20004bbd521e473d1a5620c92fea2ba930b0978c51&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/1b18dcb5-590e-456f-88d3-e33d769d3e27/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220120%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220120T055424Z&X-Amz-Expires=86400&X-Amz-Signature=82cdfa60d2f545f999cc7973b21d69afd84744c4ea5c6b4457bbb74479a77c44&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

그 뒤엔 remote repository와 연동하기 위해 git repository url을 copy 한 뒤에 `Git -> Manage Remotes` 선택 후에 git 경로를 추가해주었다.

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e1445de6-e360-426c-94e7-c90caecc5b1a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220120%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220120T055439Z&X-Amz-Expires=86400&X-Amz-Signature=7333df6d8c9c95ff2044b6fd1cf986d50441873a5f072dd4c23716f9aa92e310&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

![Untitled.png (518×220)](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e22f8d5d-444c-4bdd-b954-79cb02b414ad/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220120%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220120T055448Z&X-Amz-Expires=86400&X-Amz-Signature=3e8836eaf026c2c8038724e5d1b2bc9bcf9897942c8d3fe5476ce045c26ea507&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

정상적으로 remote repository 경로를 추가해준 뒤 commit을 하고 push를 하려고 하는데 아래와 같이 repository가 여러 개 보였다.

게다가 push 버튼도 비활성화 되어 있구..ㅠ

![Untitled.png (809×526)](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/bf5a7276-c090-4066-a105-01476d62925e/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220120%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220120T055500Z&X-Amz-Expires=86400&X-Amz-Signature=0ef8ece742125831371ea2771822c0d7bb31a420ca9972b531bcc181e3b841d7&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

한참을 헤매다 .git을 확인해보니 root project와 sub project(module) 경로에 각각 하나씩 생성되었던 것이다. 분명 나는 git init을 `VSC -> Enable Version Control Integration -> Git` 과정으로 한 번만 했던 것 같은데 나도 모르는 사이에 또 했었나보다...

내가 겪은 상황 example

```java
DemoProject(root project) 
	ㄴ TestProject(sub project)

/root/IdeaProjects/DemoProject 하위에 .git 폴더 존재
/root/IdeaProjects/DemoProject/TestProject 하위에 .git 폴더 존재

```

모든 폴더에서 .git 폴더 삭제 후 command로 git init 실행

```bash
$ cd /root/IdeaProjects/DemoProject
$ git init
$ git clone {remote repostiory url}
$ git add .
$ git commit -m {commit message}

```

정상적으로 commit과 push가 됐다!

멀티 모듈 프로젝트에서 git init을 여러 번 하면 여러 repository가 생긴다!! 주의!
