## 레이아웃 추가
mustache 디렉토리 구조   
![1](https://raw.githubusercontent.com/smpark1020/tistory/master/View%20%26%20JavaScript/%5BMustache%5D%20%EA%B8%B0%EB%B3%B8%20%EB%AC%B8%EB%B2%95/1.PNG)

index.mustache
```
{{>layout/header}}

<body>
<div class="container">
    {{>layout/bodyHeader}}
    <div class="jumbotron">
        <h1>🚘중고차 관리 프로그램🚖</h1>
        <p class="lead">차량 매입 기능</p>
        <p>
            <a class="btn btn-lg btn-dark" href="/car/save">차량 매입</a>
            <a class="btn btn-lg btn-dark" href="/car/findAll">차량 목록</a>
        </p>
        <p class="lead">차량 출고 기능</p>
        <p>
            <a class="btn btn-lg btn-info" href="/car/findNormal">차량 출고</a>
            <a class="btn btn-lg btn-info" href="/release/findAll">출고 목록</a>
        </p>
    </div>

{{>layout/footer}}
```
* {{> }}: 현재 머스테치 파일을 기준으로 다른 파일을 가져온다.   

## 반복문
```
{{#cars}}
    <tr>
        <td>{{id}}</td>
        <td>{{model}}</td>
        <td>{{color}}</td>
        <td>{{productionYear}}</td>
        <td>{{staff}}</td>
        <td>
            <a href="javascript:void(0);" class="btn btn-success" onclick="normalRelease('{{id}}', '{{carNumber}}', '{{model}}', '{{color}}');">출고</a>
        </td>
    </tr>
{{/cars}}
```
* {{#cars}}: cars라는 List를 순회한다.
* {{변수명}}: List에서 뽑아낸 객체의 필드를 사용한다.   

## IF/ELSE
```
{{#isReleased}}
    <td class="font-red">{{status}}</td>
{{/isReleased}}
{{^isReleased}}
    <td class="font-green">{{status}}</td>
{{/isReleased}}
```
* {{#isReleased}}: isReleased가 True일 경우 실행된다.
* {{^isReleased}}: isReleased가 False일 경우 실행된다.   

> 관련해서 status == 'RELEASE'일 경우와 아닐 경우를 구분하려 했으나 mustache에는 특정 값을 비교하는 문법은 존재하지 않는다고 하여 응답에 boolean타입의 sReleased 필드를 추가하여 위와 같이 사용했다.
```
@Getter
public class CarFindAllResponseDto {

    private Long id;
    private String carNumber;
    private String vin;
    private Category category;
    private String model;
    private String color;
    private String productionYear;
    private String staff;
    private LocalDateTime purchaseDate;
    private String status;
    private boolean isReleased; //필드 추가
    private LocalDateTime releaseDate;

    ...

}
```

## 참고
* [이동욱님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스](https://jojoldu.tistory.com/463)