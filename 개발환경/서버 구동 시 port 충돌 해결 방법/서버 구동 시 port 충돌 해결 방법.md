개발하다가 서버 구동 시 아래와 같이 port 충돌이 발생할 경우가 자주 있다.
```
Description:

Web server failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

이럴 경우 아래처럼 하면 된다.   
(8080포트가 충돌났을 경우 예시입니다.)
1. 윈도우 cmd 창을 연다.
2. netstat -a -o 실행 후 0.0.0.0:8080의 PID를 찾는다.
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD/%EC%84%9C%EB%B2%84%20%EA%B5%AC%EB%8F%99%20%EC%8B%9C%20port%20%EC%B6%A9%EB%8F%8C%20%ED%95%B4%EA%B2%B0%20%EB%B0%A9%EB%B2%95/1.PNG)
4. taskkill /f /pid PID   
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD/%EC%84%9C%EB%B2%84%20%EA%B5%AC%EB%8F%99%20%EC%8B%9C%20port%20%EC%B6%A9%EB%8F%8C%20%ED%95%B4%EA%B2%B0%20%EB%B0%A9%EB%B2%95/2.PNG)
5. 8080포트 서버 구동 성공   
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD/%EC%84%9C%EB%B2%84%20%EA%B5%AC%EB%8F%99%20%EC%8B%9C%20port%20%EC%B6%A9%EB%8F%8C%20%ED%95%B4%EA%B2%B0%20%EB%B0%A9%EB%B2%95/3.PNG)