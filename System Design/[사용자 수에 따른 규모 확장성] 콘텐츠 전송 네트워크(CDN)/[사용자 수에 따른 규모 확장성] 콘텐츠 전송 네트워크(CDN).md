CDN은 정적 컨텐츠를 전송하는 데 쓰이는, 지리적으로 분산된 서버의 네트워크입니다. 이미지, 비디오, CSS, JavaScript 파일 등을 캐시할 수 있습니다.

### 참고
> 동적 컨테츠 캐싱: 요청 경로(request path), 질의 문자열(query string), 쿠키(cookie), 요청 헤더(request header) 등의 정보에 기반하여 HTML 페이지를 캐시하는 것입니다.

## CDN 동작 원리
어떤 사용자가 웹사이트를 방문하면, 그 사용자에게 가장 가까운 CDN 서버가 정적 콘텐츠를 전달하게 됩니다. 사용자가 CDN 서버로부터 멀면 멀수록 웹사이트는 천천히 로드될 것입니다.

## 사이트 로딩 시간을 개선하기 위해 CDN이 사용되는 예시
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/System%20Design/%5B%EC%82%AC%EC%9A%A9%EC%9E%90%20%EC%88%98%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EA%B7%9C%EB%AA%A8%20%ED%99%95%EC%9E%A5%EC%84%B1%5D%20%EC%BD%98%ED%85%90%EC%B8%A0%20%EC%A0%84%EC%86%A1%20%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC(CDN)/1.jpg)   

## CDN 동작 설명
![2](https://raw.githubusercontent.com/smpark1020/tistory/master/System%20Design/%5B%EC%82%AC%EC%9A%A9%EC%9E%90%20%EC%88%98%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EA%B7%9C%EB%AA%A8%20%ED%99%95%EC%9E%A5%EC%84%B1%5D%20%EC%BD%98%ED%85%90%EC%B8%A0%20%EC%A0%84%EC%86%A1%20%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC(CDN)/2.jpg)   
1. 사용자 A가 이미지 URL을 이용해 image.png에 접근합니다. URL의 도메인은 CDN 서비스 사업자가 제공한 것입니다.
  * 예시
     * 클라우드 프론트(Cloudfront)
         * https://mysite.cloudfront.net/logo.jpg
     * 아카마이(Akamai)
         * https://mysite.akamai.com/image-manager/img/logo.jpg
2. CDN 서버의 캐시에 해당 이미지가 없는 경우, 서버는 원본(origin) 서버에 요청하여 파일을 가져옵니다. 원본 서버는 웹 서버일 수도 있고 아마존(Amazon) S3 같은 온라인 저장소일 수도 있습니다.
3. 원본 서버가 파일을 CDN 서버에 반환합니다. 응답의 HTTP 헤더에는 해당 파일이 얼마나 오래 캐시될 수 있는지를 설명하는 TTL(Time-To-Live) 값이 들어 있습니다.
4. CDN 서버는 파일을 캐시하고 사용자 A에게 반환합니다. 이미지는 TTL에 명시된 시간이 끝날 때까지 캐시됩니다.
5. 사용자 B가 같은 이미지에 대한 요청을 CDN 서버에 전송합니다.
6. 만료되지 않은 이미지에 대한 요청은 캐시를 통해 처리됩니다.

## CDN 사용 시 고려해야 할 사항
* 비용: CDN은 보통 제3 사업자(third-party providers)에 의해 운영되며, 여러분은 CDN으로 들어가고 나가는 데이터 전송 양에 따라 요금을 내게 됩니다. 자주 사용되지 않는 콘텐츠를 캐싱하는 것은 이득이 크지 않으므로, CDN에서 빼는 것을 고려해야 합니다.
* 적절한 만료 시한 설정: 시의성이 중요한(time-sensitive) 콘텐츠의 경우 만료 시점을 잘 정해야 합니다. 너무 길면 콘텐츠의 신선도는 떨어질 것이고, 너무 짧으면 원본 서버에 빈번히 접속하게 되어서 좋지 않습니다.
* CDN 장애에 대한 대처 방안: CDN 자체가 죽었을 경우 웹사이트/애플리케이션이 어떻게 동작해야 하는지 고려해야 합니다. 가령 일시적으로 CDN이 응답하지 않을 경우, 해당 문제를 감지하여 원본 서버로부터 직접 콘텐츠를 가져오도록 클라이언트를 구성하는 것이 필요할 수도 있습니다.
* 콘텐츠 무효화(invalidation) 방법: 아직 만료되지 않은 콘텐츠를 CDN에서 제거하는 방법
  * CDN 서비스 사업자가 제공하는 API를 이용하여 콘텐츠 무효화
  * 콘텐츠의 다른 버전을 서비스하도록 오브젝트 버저닝(object versioning)이용. 콘텐츠의 새로운 버전을 지정하기 위해서는 URL 마지막에 버전 번호를 인자로 주면 됩니다.
      * 예시) image.png?v=2

### CDN과 캐시가 추가된 설계
![3](https://raw.githubusercontent.com/smpark1020/tistory/master/System%20Design/%5B%EC%82%AC%EC%9A%A9%EC%9E%90%20%EC%88%98%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EA%B7%9C%EB%AA%A8%20%ED%99%95%EC%9E%A5%EC%84%B1%5D%20%EC%BD%98%ED%85%90%EC%B8%A0%20%EC%A0%84%EC%86%A1%20%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC(CDN)/3.jpg)   
1. 정적 콘텐츠(JS, CSS, 이미지 등)는 더 이상 웹 서버를 통해 서비스하지 않으며, CDN을 통해 제공하여 더 나은 성능을 보장합니다.
2. 캐시가 데이터베이스 부하를 줄여줍니다. 

## 참조
* [가상 면접 사례로 배우는 대규모 시스템 설계 기초](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966263158&orderClick=&Kc=)