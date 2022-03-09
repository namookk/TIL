## Git-Flow 전략

저는 실무에서 형상관리를 위해 Git을 사용하고 또, 
동료 개발자들과 협업하면서 Git으로 인해 어려움을 겪었었습니다.
예를들어, 여러 개발자가 같은 branch를 바라보고 작업하여 충돌이 많이 발생하고 또, 충돌을 해결하는 과정에서 이전 소스가 날라간다던지 등등.. 수많은 경험을 하였습니다.

이러한 이슈를 해결하기위해 여러 git branch전략(Git-Flow, GitHub-Flow ...)들이 있으며 그중 이번에 사용하게 된 git-flow 전략에 대해 공부하려고 합니다.

### Git Flow란?
Git Flow는 대표적인 branching 전략 중 하나로 2010년에 Vicent Driessen이 Git작업 절차에 대해 소개로 탄생하였습니다.

Git Flow는 branch를 크게 5가지로 나누어서 개발합니다.

+ develop
+ master
+ feature
+ release
+ hotfix

위 와 같은 5가지 특징적인 브런치들이 있으며 어떤 역할을 하는지 알아봅시다.
아래 그림은 Git Flow를 한눈에 확인할 수 있는 이미지입니다.


### develop / master

GitFlow에서 develop / master branch를 메인 branch로 부르며 develop과 master를 나누는 아이디어가 GitFlow의 핵심입니다.

+ master는 현재 운영중인 제품의 소스와 일치하는 branch입니다.
+ develop은 현재 개발이 완료된 상태와 일치하는 branch입니다.
  + 개발이 완료된 상태는 언제든지 배포될 수 있는 상태를 말합니다.

### feature 

develop 에서 개발하고 배포 시 master에 merge하여 배포한다. 이렇게 생각될 수 있지만
develop branch에서 직접적으로 개발 시 혼자서 개발하는게 아니라면 다른 개발자의 commit과 충돌이 발생할 수 있습니다.
따라서 feature branch를 이용하여 conflict가 생기지 않도록 하는것이 목적입니다.

feature branch의 사용법은 아래와 같습니다.
1. develop에서 feature branch를 생성하여 작업을 시작합니다. ex) feature/user
2. feature branch에서 작업이 끝나면 feature branch를 최신 develop에 merge합니다. 이 과정에서 pull request를 통해 코드리뷰를 받을 수 있습니다.

단, feature branch에서 주기적으로 develop branch의 내용을 merge받아 와야 합니다.

### release

개발이 완료되어 배포를 준비하는 단계에서 버전업, QA를 진행하는 단계의 branch입니다.
배포되어선 안되는 다음 release의 feature가 의도하지 않게 함께 배포되는것을 막아줍니다.

release branch의 사용법은 아래와 같습니다.
1. develop에서 release branch를 생성하여 작업합니다. ex) release/1.0.1
2. release branch에서 해야할 작업(QA 등..)이 끝나면 release branch를 최신 develop과 master에 각각 merge합니다.

### hotfix

운영중인 소스에 문제가 발견되어서 수정해야합니다. 이때도 develop에서 수정하여 올리면 다음 release의 feature들이 같이 배포될 수 있으므로 hotfix branch를 사용합니다.

hotfix branch의 사용법은 아래와 같습니다.
1. master에서 hotfix branch를 생성하여 버그를 fix합니다.
2. hotfix의 작업이 끝나면 develop과 master에 각각 merge합니다.


지금까지 간단하게 Git-Flow전략에 대해 알아보았습니다. Git Branching 전략 중 Git-Flow밖에 사용을 안해봐서 추후 GitHub-Flow도 알아보면서 둘의 차이를 알아보면 좋을 것 같습니다.




