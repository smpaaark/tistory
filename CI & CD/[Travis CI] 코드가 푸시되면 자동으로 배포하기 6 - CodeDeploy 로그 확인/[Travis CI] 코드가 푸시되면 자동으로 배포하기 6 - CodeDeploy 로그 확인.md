CodeDeploy와 같이 AWS가 지원하는 서비스에서는 오류가 발생했을 때 로그 찾는 방법을 모르면 오류를 해결하기 어렵습니다.   
그래서 배포가 실패하면 어느 로그를 봐야 할지 간단하게 소개하겠습니다.   

CodeDeploy에 관한 대부분 내용은 /opt/codedeploy-agent/deployment-root에 있습니다.   
해당 디렉토리로 이동(cd /opt/codedeploy-agent/deployment-root)한 뒤 목록을 확인해 보면 (ll) 다음과 같은 내용을 확인할 수 있습니다.   
```
drwxr-xr-x 2 root root 247 Jul 18 23:21 deployment-instructions
# 2
drwxr-xr-x 2 root root  46 Jul 18 22:52 deployment-logs
# 1
drwxr-xr-x 7 root root 101 Jul 18 23:21 ebf058e5-99ae-4eba-8cc6-005b49a036bc
drwxr-xr-x 2 root root   6 Jul 18 23:21 ongoing-deployment
```
1. 영문과 대시(-)가 있는 디렉토리명은 CodeDeploy ID입니다.   
  * 사용자마다 고유한 ID가 생성되어 각자 다른 ID가 발급됩니다.
  * 해당 디렉토리로 들어가 보면 배포한 단위별로 배포 파일들이 있습니다.
  * 본인의 배포 파일이 정상적으로 왔는지 확인할 수 있습니다.
2. /opt/codedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployment.log
  * CodeDeploy 로그 파일입니다.
  * CodeDeploy로 이루어지는 배포 내용 중 표준 입/출력 내용은 모두 여기에 담겨 있습니다.   
  * 작성한 echo 내용도 모두 표기됩니다.

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)