### 많이 사용하는 리눅스 명령어

#### 디렉토리 관련 명령어

+ pwd
```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % pwd
/Users/namookk
```
현재 경로의 절대경로를 출력합니다.

+ cd [이동할 디렉토리]
```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % cd Desktop
namookk@hwangnam-ug-ui-MacBookAir Desktop % 
```
디렉토리를 이동할때 쓰이는 명령어입니다. 이동할 디렉토리는 절대경로, 상대경로 모두 가능합니다.
이동할 디렉토리를 입력할때 Tab키를 누르면 디렉토리명이 자동완성 됩니다.

+ ls
```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % ls
Applications			PycharmProjects
Desktop				data
Documents			devoooops.pem
Downloads			getting-started
IdeaProjects			javascript
Library				mogakko-react
Movies				node-js
Music				profile.jpeg
OAuth2				react-practice
Pictures			react-yotube-clone
Postman				todo.mv.db
Public				무제 폴더
```

현재 디렉토리에 있는 내용을 보는 명령어 입니다. 
ls명령어에서 사용할 수 있는 옵션은 아래와 같습니다.
```
ls -a : 숨김파일을 포함하여 모든 파일의 목록을 출력합니다.
ls -d : 현재 디렉토리의 정보를 출력합니다.
ls -l : 파일의 상세정보를 출력합니다.
ls -i : 첫번째 행의 inode 번호를 출력합니다.
ls -A : (.)와 (..)를 제외한 모든 파일을 출력합니다.
ls -F : 파일의 종류를 표시합니다.
ls -L : 심벌릭 링크 파일의 경우 원본 파일의 정보를 출력합니다.
ls -R : 하위 디렉토리의 목록까지 모두 출력합니다.
```

+ mkdir [디렉토림 명]

```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % mkdir test
namookk@hwangnam-ug-ui-MacBookAir ~ % ls
Applications			data
Desktop				devoooops.pem
Documents			getting-started
Downloads			javascript
IdeaProjects			mogakko-react
Library				node-js
Movies				profile.jpeg
Music				react-practice
OAuth2				react-yotube-clone
Pictures			test
Postman				todo.mv.db
Public				무제 폴더
PycharmProjects
```
디렉토리를 생성하는 명령어입니다. 단, 생성하기 위해서는 쓰기권한이 있어야합니다.
mkdir test test1 test2와 같이 공백을 구분자로 주어서 여러개의 디렉토리를 한번에 생성할 수 있습니다.

`mkdir -p test1/test2/test3`과 같이 -p 옵션을 주어서 생성하게되면 하위 디렉토리까지 함께 생성 가능합니다.

+ rmdir [대상 디렉토리]

```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % rmdir test
namookk@hwangnam-ug-ui-MacBookAir ~ % ls
Applications			PycharmProjects
Desktop				data
Documents			devoooops.pem
Downloads			getting-started
IdeaProjects			javascript
Library				mogakko-react
Movies				node-js
Music				profile.jpeg
OAuth2				react-practice
Pictures			react-yotube-clone
Postman				todo.mv.db
Public				무제 폴더
```

대상 디렉토리를 삭제하는 명령어입니다. mkdir과 같이 공백을 구분자로 주어서 여러 디렉토리를 삭제할 수 있습니다.
또, -p옵션을 통해서 상위 디렉토리가 비어있다면 상위 디렉토리까지 함께 삭제합니다.

+ cp [옵션] [복사할 디렉토리] [대상 디렉토리]

```shell
namookk@hwangnam-ug-ui-MacBookAir ~ % cp -r ./test ./test-copy
namookk@hwangnam-ug-ui-MacBookAir ~ % ls
Applications			data
Desktop				devoooops.pem
Documents			getting-started
Downloads			javascript
IdeaProjects			mogakko-react
Library				node-js
Movies				profile.jpeg
Music				react-practice
OAuth2				react-yotube-clone
Pictures			test
Postman				test-copy
Public				todo.mv.db
PycharmProjects			무제 폴더
```

디렉토리나 파일을 복사하는 명령어입니다. -r 옵션을 통해 디렉토리 하위까지 전체가 복사됩니다.

아래는 cp명령어에서 사용할 수 있는 옵션입니다.
```
cp -a : 원본 파일의 속성, 링크 정보들을 그대로 유지하면서 복사합니다.
cp -b : 복사할 대상이 이미 있을 경우 기존 파일을 백업하고 복사합니다.
cp -d : 만약 복사할 원본이 심볼릭 링크일 때 심볼릭 자체를 복사합니다.
cp -f : 만약 복사할 대상이 이미 있으면 강제로 지우고 복사합니다.
cp -i : 만약 복사할 대상이 이미 있으면 사용자에게 물어봅니다.
cp -l : 하드링크 형식으로 복사합니다.
cp -P : 원본 파일 지정을 경로와 같이했을 경우 그 경로 그대로 복사됩니다.
cp -p : 파일의 소유자, 그룹, 권한, 시간 정보들이 그대로 보존되어 복사됩니다.
cp -r : 원본이 파일이면 그냥 복사되고 디렉터리라면 디렉터리 전체가 복사됩니다.
cp -s : 파일을 심볼릭 링크 형식으로 복사합니다. 원본 파일이름을 절대 경로로 지정해야 합니다.
cp -u : 복사할 대상이 있을 때 이 파일의 변경 날짜가 같거나 더 최근의 것이면 복사하지 않습니다.
cp -v : 복사 상태를 보여줍니다.
cp -x : 원본과 대상 파일의 파일시스템이 다를 경우에는 복사하지 않습니다.
cp -R : 디렉터리를 복사할 경우 그 안에 포함된 모든 하위경로와 파일들을 모두 복사합니다.
```

+ mv [이동할 디렉토리] [대상 디렉토리]

```shell
namookk@hwangnam-ug-ui-MacBookAir test % mv ../test-copy ./ 
namookk@hwangnam-ug-ui-MacBookAir test % ls
test-copy
```

디렉토리를 이동시키는 명령어 입니다.
mv 명령어에서 사용할 수 있는 옵션은 아래와 같습니다.

```
mv -b : 목적 경로에 같은 이름의 파일(혹은 디렉토리)가 존재하면, 기존 파일을 백업 후 이동시킵니다.
mv -f : 목적 경로에 같은 이름의 파일(혹은 디렉토리)가 존재하면, 덮어쓸 것인지 묻지 않고 바로 덮어씁니다. 
mv -i : 목적 경로에 같은 이름의 파일(혹은 디렉토리)가 존재하면, 덮얼쓸 것인지 물어봅니다.
mv -v : 이동중인 상태를 표시합니다.
```
