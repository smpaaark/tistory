## 파일 인코딩 UTF-8
Preferences > Editor > File Encodings

![1](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/1.png)

## 새줄 문자 LF
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/2.png)

## import 구문
클래스를 import할때는 와일드카드(*) 없이 모든 클래스 명을 다 씁니다.   
File > Settings > Editors > Code Style > Java > Imports > Import Layout
* General
  * Use single class import: 체크
  * Class count to use import with '*': 99
  * Names count to use static import with '*': 99

![3](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/3.png)

## 연산자 전에 줄바꿈
가독성을 위해 연산자 전에 줄바꿈을 합니다.   
File > Settings > Editors > Code Style > Java > Wrapping and Braces   
* Binary expressions 아래의 Operation sign on next line 항목을 선택합니다.

![4](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/4.png)

## 파일의 마지막에 새줄 문자가 없는 경우 추가
파일의 마지막은 새줄 문자 LF로 끝나야 합니다.   
File > Settings > Editor > General 메뉴에서 Ensure line feed at file end on Save 를 선택합니다.

![5](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/5.png)

## 코드를 변경할 때마다 Optimize imports 자동 적용
Editor > General > Auto Import > Optimize imports on the fly를 선택합니다.

![6](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/6.png)

## 코드를 저장할 때마다 Optimize imports 자동 적용
Tools > Actions on Save > Optimize imports를 선택합니다.

![7](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/7.png)

## 공백 문자 보이게 설정
탭과 스페이스가 섞여 있는 프로젝트의 코드를 정리할 때는 탭과 스페이스를 눈에 보이게 표시합니다.   
Editor > General > Appearance에서 `Show whitespaces`를 선택합니다.   
하위 분류에서 Leading, Inner, Trailing을 선택합니다.

![8](https://raw.githubusercontent.com/smpark1020/tistory/master/IDE/%5B%EC%9D%B8%ED%85%94%EB%A6%ACJ%5D%20%EA%B0%9C%EB%B0%9C%20%ED%99%98%EA%B2%BD%20%EC%84%B8%ED%8C%85/8.png)

## Reference
* [캠퍼스 핵데이 Java 코딩 컨벤션](https://naver.github.io/hackday-conventions-java/)
