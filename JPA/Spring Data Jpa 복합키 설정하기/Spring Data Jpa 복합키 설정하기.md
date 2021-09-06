실무에서 관리자 시스템을 개발하는 중인데 테이블의 키가 복합키로 이루어져 있는 경우가 있었습니다.

복합키를 설정하는 방법은 두 가지가 있습니다.
1. @Embeddable을 이용하는 방법
2. @IdClass를 이용하는 방법

이 글에서는 @IdClass를 이용하는 방법을 소개하겠습니다.   

## 복합 Id 클래스 
```
package lotte.admin.domain.branch;

import lombok.NoArgsConstructor;

import javax.persistence.Id;
import java.io.Serializable;

@NoArgsConstructor
public class BranchChannelId implements Serializable {

    @Id
    private String brandCode;

    @Id
    private String branchId;

    @Id
    private int channelSeq;

    @Id
    private String channelCode;

}
```
(BranchChannelId.java)
* 복합키로 사용될 필드들을 모아놓은 복합키 설정용 클래스입니다.
* Serializable을 구현해야 합니다.

## 엔티티 클래스
```
package lotte.admin.domain.branch;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lotte.admin.domain.BaseTimeEntity;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.IdClass;
import javax.persistence.Table;

@Getter
@NoArgsConstructor
@IdClass(BranchChannelId.class)
@Table(name = "TB_BRANCH_CHANNEL")
@Entity
public class BranchChannel extends BaseTimeEntity {

    @Id
    private String brandCode;

    @Id
    private String branchId;

    @Id
    private int channelSeq;

    @Id
    private String channelCode;

    private String channelState;
    private int deliveryTime;
    private String useFlag;
    private String applyDate;
    private String applyTime;
    private String regId;
    private String updId;

    @Builder
    public BranchChannel(String brandCode, String branchId, int channelSeq, String channelCode, String channelState, int deliveryTime, String useFlag, String applyDate, String applyTime, String regId, String updId) {
        this.brandCode = brandCode;
        this.branchId = branchId;
        this.channelSeq = channelSeq;
        this.channelCode = channelCode;
        this.channelState = channelState;
        this.deliveryTime = deliveryTime;
        this.useFlag = useFlag;
        this.applyDate = applyDate;
        this.applyTime = applyTime;
        this.regId = regId;
        this.updId = updId;
    }

}
```
(BranchChannel.java)
* @IdClass(BranchChannelId.class)를 클래스 위에 선언해야 합니다.
* IdClass에 선언했던 복합키 필드들에 @Id를 선언합니다.

## Repository
```
package lotte.admin.domain.branch;

import org.springframework.data.jpa.repository.JpaRepository;

public interface BranchChannelRepository extends JpaRepository<BranchChannel, BranchChannelId> {

}
```
(BranchChannelRepository.java)
* JpaRepository를 구현할 때 Id 타입 위치에 복합 Id 클래스인 BranchChannelId를 입력합니다.

## 테스트
```
package lotte.admin.domain.branch;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import static org.assertj.core.api.Assertions.assertThat;

@ExtendWith(SpringExtension.class)
@DataJpaTest
class BranchChannelTest {

    @Autowired
    private BranchChannelRepository branchChannelRepository;

    @Test
    public void 복합키_테스트() throws Exception {
        // given
        BranchChannel branchChannel = BranchChannel.builder()
                .brandCode("10")
                .branchId("19998")
                .channelSeq(1)
                .channelCode("40")
                .channelState("01")
                .deliveryTime(30)
                .useFlag("Y")
                .regId("system")
                .updId("system")
                .build();

        // when
        branchChannelRepository.save(branchChannel);

        // then
        BranchChannelId branchChannelId = BranchChannelId.builder()
                .brandCode("10")
                .branchId("19998")
                .channelSeq(1)
                .channelCode("40")
                .build();
        BranchChannel findBranchChannel = branchChannelRepository.findById(branchChannelId).get();
        assertThat(findBranchChannel).isNotNull();
    }

}
```
(BranchChannelTest.java)
* findById를 호출할때 BranchChannelId를 파라미터로 전달합니다.

```
Hibernate: 
    
create table tb_branch_channel (
    channel_seq int not null,
    channel_code varchar(255) not null,
    brand_code varchar(255) not null,
    branch_id varchar(255) not null,
    reg_date varchar(255),
    reg_time varchar(255),
    upd_date varchar(255),
    upd_time varchar(255),
    apply_date varchar(255),
    apply_time varchar(255),
    channel_state varchar(255),
    delivery_time int not null,
    reg_id varchar(255),
    upd_id varchar(255),
    use_flag varchar(255),
    primary key (channel_seq, channel_code, brand_code, branch_id)
)
```
(테이블 생성 콘솔 출력)
* 정상적으로 복합키가 primary key로 설정되는 것을 확인할 수 있습니다.

```
Hibernate: 
select
    branchchan0_.channel_seq as channel_1_1_0_,
    branchchan0_.channel_code as channel_2_1_0_,
    branchchan0_.brand_code as brand_co3_1_0_,
    branchchan0_.branch_id as branch_i4_1_0_,
    branchchan0_.reg_date as reg_date5_1_0_,
    branchchan0_.reg_time as reg_time6_1_0_,
    branchchan0_.upd_date as upd_date7_1_0_,
    branchchan0_.upd_time as upd_time8_1_0_,
    branchchan0_.apply_date as apply_da9_1_0_,
    branchchan0_.apply_time as apply_t10_1_0_,
    branchchan0_.channel_state as channel11_1_0_,
    branchchan0_.delivery_time as deliver12_1_0_,
    branchchan0_.reg_id as reg_id13_1_0_,
    branchchan0_.upd_id as upd_id14_1_0_,
    branchchan0_.use_flag as use_fla15_1_0_ 
from
    tb_branch_channel branchchan0_ 
where
    branchchan0_.channel_seq=? 
    and branchchan0_.channel_code=? 
    and branchchan0_.brand_code=? 
    and branchchan0_.branch_id=?
```
(findById 쿼리)
* 정상적으로 복합키를 이용하여 select 문이 실행됩니다.

## 참조
* [Legacy DB의 JPA Entity Mapping (복합키 매핑 편) - 우아한 ...](https://techblog.woowahan.com/2595/)