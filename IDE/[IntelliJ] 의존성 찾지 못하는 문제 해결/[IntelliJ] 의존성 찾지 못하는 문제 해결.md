인텔리제이 ToolBox에서 Update 하라고해서 업데이트를 했습니다. 그런데 잘 돌아가던 프로젝트가 갑자기 에러 투성이가 되었습니다.   

확인해보니 분명 아래처럼 의존성이 정상적으로 잘 추가되었는데도 불구하고 gradle에 추가한 의존성들을 찾지못하여 import가 되지 않고 있었습니다.

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5BIntelliJ%5D%20%EC%9D%98%EC%A1%B4%EC%84%B1%20%EC%B0%BE%EC%A7%80%20%EB%AA%BB%ED%95%98%EB%8A%94%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0/1.PNG)   

![2](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5BIntelliJ%5D%20%EC%9D%98%EC%A1%B4%EC%84%B1%20%EC%B0%BE%EC%A7%80%20%EB%AA%BB%ED%95%98%EB%8A%94%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0/2.PNG)   

## 해결 방법
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5BIntelliJ%5D%20%EC%9D%98%EC%A1%B4%EC%84%B1%20%EC%B0%BE%EC%A7%80%20%EB%AA%BB%ED%95%98%EB%8A%94%20%EB%AC%B8%EC%A0%9C%20%ED%95%B4%EA%B2%B0/3.PNG)   
* [File] - [Invalidate Caches...] 클릭

이 문제로 1시간 넘게 고생ㅠㅠ

## 참조
* [gradle 에 추가한 의존성을 IntelliJ 에서 못 찾을 때](https://www.lesstif.com/spring/gradle-intellij-113345573.html)