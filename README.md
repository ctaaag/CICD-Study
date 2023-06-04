# CICD-Study

## Github Actions의 기본 구성
### 1. Workflow
- Workflow
여러 Job으로 구성되고, Event에 의해 트리거될 수 있는 자동화된 프로세스
최상위 개념
Workflow 파일은 YAML으로 작성되고, Github Repository의 .github/workflows 폴더 아래에 저장됨

- 생성방법
  - branch의 Actions에서 템플릿 활용한 yml 파일 작성
  - 혹은 별도의 커스터마이징한 workflow 폴더 내 yml 파일 생성 
<img width="602" alt="스크린샷 2023-06-04 오후 8 47 02" src="https://github.com/ctaaag/CICD-Study/assets/103479322/861f5edf-3684-4929-be1e-b55365752f3d">


### 2. Event
- 정의 : Workflow를 Trigger(실행)하는 특정 활동이나 규칙
- 예를 들어 다음과 같은 상황을 사용할 수 있음
  - 특정 브랜치로 Push하거나
  - 특정 브랜치로 Pull Request하거나
  - 특정 시간대에 반복(Cron)
  - Webhook을 사용해 외부 이벤트를 통해 실행

```
  on:
    push:
      branches: [ master ]
    pull_request:
      branches: [ master ]
```
```
  on: [push, pull_request]
```

### 3. Job
- Job은 여러 Step으로 구성되고, 가상 환경의 인스턴스에서 실행됨
- 다른 Job에 의존 관계를 가질 수 있고, 독립적으로 병렬 실행도 가능함

```
  jobs:
        build:
          runs-on: ubuntu-latest
		
          steps:
          - uses: actions/checkout@v2
          - name: Run a one-line script
            run: echo Hello, world!

          - name: Run a multi-line script
            run: |
              echo Add other actions to build,
              echo test, and deploy your project.
```

- Workflow는 다양한 Job으로 구성됨
- 여러 Job이 있을 경우, Default로 병렬 실행
- build라는 job을 생성하고, 그 아래에 2개의 step이 존재하는 구조
- runs-on은 어떤 OS에서 실행될지 지정
- strategy - matrix 인자를 사용하면 어떤 파이썬 버전에서 테스트할지 확인할 수 있음. 자세한 내용은 밑 예제를 통해 확인할 수 있음
- 위 예제에선 run할 때 env(환경 변수)를 설정하는 예제가 없는데, 밑에서 환경 변수를 등록하는 방법에 대해 안내할 예정
- steps의 uses는 어떤 액션을 사용할지 지정함. 이미 만들어진 액션을 사용할 때 지정

- <job이름>.strategy.marix 하위에 job을 수행하고자 하는 matrix를 정의하면 정의된 변수들의 가능한 모든 조합의 수만큼 Job을 실행한다. 
- step에서 matrix 변수를 사용할 때는 ${{ matrix.변수명 }} 의 형태로 사용해주면 된다. 
- 아래 예시는 node-ver이 14, 16, 18인 경우에 대해서 node-version을 설정하여 돌리는 Job들을 수행한다.

```
    strategy:
      matrix:
        node-ver: [14, 16, 18]
```


### 4. Step
- Task들의 집합으로, 커맨드를 날리거나 action을 실행할 수 있음

자바스크립트 프로젝트에서 테스트를 돌리려면 CI 서버로 코드를 내려 받고, npm 패키지를 설치한 후, 테스트를 실행해야함.
해당 3단계의 작업은 아래와 같이 steps 속성을 통해서 명시할 수 있습니다.
```
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test
```

### 5. Action
- Workflow의 가장 작은 블럭(smallest portable building block)
- Job을 만들기 위해 Step들을 연결할 수 있음
- 재사용이 가능한 컴포넌트
- 개인적으로 만든 Action을 사용할 수도 있고, Marketplace에 있는 공용 Action을 사용할 수도 있음
- Github Marketplace와 Github Actions Repository에서 확인 가능

