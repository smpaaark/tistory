Jpa Auditing 기능을 사용하여 등록 일자, 수정 일자을 DB에 자동으로 저장할 수 있습니다.

등록 일자(yyyyMMdd), 등록 시간(HHmmss), 수정 일자(yyyyMMdd), 수정 시간(HHmmss)을 각각 괄호 안의 포맷으로 저장해야 했습니다. BaseTimeEntity를 아래와 같이 작성하면 이를 해결할 수 있습니다.

## BaseTimeEntity
```
import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTimeEntity {

    @CreatedDate
    private String regDate;

    @CreatedDate
    private String regTime;

    @LastModifiedDate
    private String updDate;

    @LastModifiedDate
    private String updTime;

    @PrePersist
    public void onPrePersist() {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter dateFormat = getDateFormat();
        DateTimeFormatter timeFormat = getTimeFormat();

        this.regDate = now.format(dateFormat);
        this.regTime = now.format(timeFormat);
        this.updDate = now.format(dateFormat);
        this.updTime = now.format(timeFormat);
    }

    @PreUpdate
    public void onPreUpdate() {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter dateFormat = getDateFormat();
        DateTimeFormatter timeFormat = getTimeFormat();

        this.updDate = now.format(dateFormat);
        this.updTime = now.format(timeFormat);
    }

    private DateTimeFormatter getDateFormat() {
        return DateTimeFormatter.ofPattern("yyyyMMdd");
    }

    private DateTimeFormatter getTimeFormat() {
        return DateTimeFormatter.ofPattern("HHmmss");
    }

}
```
(BaseTimeEntity.java)
* @PrePersist: 엔티티 insert 이전에 실행됩니다.
* @PreUpdate: 엔티티 update 이전에 실행됩니다.

## 테스트
```
package lotte.admin.domain;

import lotte.admin.domain.branch.BaeminBranchToken;
import lotte.admin.domain.branch.BaeminBranchTokenRepository;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import static org.assertj.core.api.Assertions.assertThat;

@ExtendWith(SpringExtension.class)
@DataJpaTest
class BaseTimeEntityTest {

    @Autowired
    BaeminBranchTokenRepository baeminBranchTokenRepository;

    @Test
    public void BaseTimeEntity_테스트() throws Exception {
        // given
        BaeminBranchToken baeminBranchToken = BaeminBranchToken.builder()
                .tokenType("lotteria")
                .token("token")
                .build();

        // when
        baeminBranchTokenRepository.save(baeminBranchToken);

        // then
        BaeminBranchToken findToken = baeminBranchTokenRepository.findAll().get(0);
        assertThat(findToken.getRegDate()).isNotEmpty();
        assertThat(findToken.getRegTime()).isNotEmpty();
        assertThat(findToken.getUpdDate()).isNotEmpty();
        assertThat(findToken.getUpdTime()).isNotEmpty();
    }

}
```
(BaseTimeEntityTest.java)

```
Hibernate: 
    insert 
    into
        tb_baemin_token_info
        (reg_date, reg_time, upd_date, upd_time, token, token_type, seq) 
    values
        (?, ?, ?, ?, ?, ?, ?)
2021-09-06 15:51:51.665 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [1] as [VARCHAR] - [20210906]
2021-09-06 15:51:51.665 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [2] as [VARCHAR] - [155151]
2021-09-06 15:51:51.665 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [3] as [VARCHAR] - [20210906]
2021-09-06 15:51:51.665 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [4] as [VARCHAR] - [155151]
2021-09-06 15:51:51.666 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [5] as [VARCHAR] - [token]
2021-09-06 15:51:51.666 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [6] as [VARCHAR] - [lotteria]
2021-09-06 15:51:51.668 TRACE 105148 --- [           main] o.h.type.descriptor.sql.BasicBinder      : binding parameter [7] as [BIGINT] - [1]
```
(출력 콘솔)
* 입력 파라미터를 보면 일자, 시간이 제대로 입력되는 것을 확인할 수 있습니다.