인텔리제이 ToolBox에서 Update 하라고해서 업데이트를 했습니다. 그런데 잘 돌아가던 프로젝트가 갑자기 에러 투성이가 되었습니다.   

확인해보니 분명 아래처럼 의존성이 정상적으로 잘 추가되었는데도 불구하고 gradle에 추가한 의존성들을 찾지못하여 import가 되지 않고 있었습니다.

![1]()   

![2]()   

## 해결 방법
![3]()   
* [File] - [Invalidate Caches...] 클릭

이 문제로 1시간 넘게 고생ㅠㅠ

## 참조
* [gradle 에 추가한 의존성을 IntelliJ 에서 못 찾을 때](https://www.lesstif.com/spring/gradle-intellij-113345573.html)