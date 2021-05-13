테스트 중에 응답 내용에 한글이 깨지는 현상이 발생했다.
아래 로그를 보면 header에 utf-8 설정이 되어있지 않은 것을 확인할 수 있다.
```
MockHttpServletResponse:
           Status = 400
    Error message = null
          Headers = [Content-Type:"application/json"]
     Content type = application/json
             Body = {"message":"ì´ë¯¸ ë§¤ìëì´ìë ì°¨ëìëë¤. ì°¨ëë²í¸: 04êµ¬4716"}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```

## 해결 방법
### application.properties에 설정 추가
```
# UTF-8 세팅
server.servlet.encoding.charset=UTF-8
server.servlet.encoding.force=true
```

### 결과
```
MockHttpServletResponse:
           Status = 400
    Error message = null
          Headers = [Content-Type:"application/json;charset=UTF-8"]
     Content type = application/json;charset=UTF-8
             Body = {"message":"이미 매입되어있는 차량입니다. 차량번호: 04구4716"}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```
