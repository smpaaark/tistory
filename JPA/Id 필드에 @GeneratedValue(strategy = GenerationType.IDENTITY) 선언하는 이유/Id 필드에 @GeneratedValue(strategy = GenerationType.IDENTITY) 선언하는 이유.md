* Spring Boot는 Hibernate의 id 생성 전략을 그대로 따라갈지 말지를 결정하는 useNewIdGeneratorMappings 설정이 있다.
* 1.5에선 기본값이 false, 2.0부터는 true
* Hibernate 5.0부터 MySQL의 AUTO는 IDENTITY가 아닌 TABLE을 기본 시퀀스 전략으로 선택된다.
* 즉, 1.5에선 Hibernate 5를 쓰더라도 AUTO를 따라가지 않기 때문에 IDENTITY가 선택
* 2.0에선 true이므로 Hibernate 5를 그대로 따라가기 때문에 TABLE이 선택

자세한 내용은 이동욱님의 블로그에 아주 잘 정리가 되어 있으니 참고 링크로 대체한다.

## 참고
* [Spring Boot Data JPA 2.0 에서 id Auto_increment 문제 해결](https://jojoldu.tistory.com/295)