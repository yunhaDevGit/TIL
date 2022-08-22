# React 작업 환경

### Node.js

Webpack과 Babel과 같은 도구들이 자바스크립트 런타임인 Node.js를 기반으로 만들어져 있습니다.

그러므로 해당 도구들을 사용하기 위해 Node.js를 설치해야 합니다.

```bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
$ nvm install --lts
```

### Yarn

Yarn은 조금 개선된 버전의 npm. npm은 Node.js를 설치하게 될 때 같이 딸려오는 **패키지 매니저 도구**입니다. 프로젝트에서 사용되는 라이브러리를 설치하고 해당 라이브러리들의 버전 관리를 하게 될 때 사용합니다.

Yarn을 사용하는 이유는 더 나은 속도, 더 나은 캐싱 시스템을 사용하기 위함입니다. 

```bash
$ npm install --global yarn

# version check
$ yarn --version
```

### Webpack, Babel?

리액트 프로젝트를 만들면서 컴포넌트를 여러가지 파일로 분리해서 저장하고, 이 컴포넌트는 일반 자바스크립트가 아닌 JSX라는 문법으로 작성하게 됩니다. 

***여러가지의 파일을 한 개로 결합하기 위해*** **Webpack**이라는 도구를 사용하고, ***JSX를 비롯한 새로운 자바스크립트 문법들을 사용하기 위해*** **Babel**이라는 도구를 사용합니다. 

### React Project 생성

```bash
$ npx create-react-app begin-react
```

begin-react라는 디렉터리가 생기고 그 안에 리액트 프로젝트가 생성됩니다. 

해당 명령어를 실행하고 나면 브라우저에 *[http://localhost:3000/](http://localhost:3000/)*  이 열리고, 리액트 프로젝트를 확인할 수 있습니다.
