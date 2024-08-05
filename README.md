# Art_Auction_API_Server
미술 경매 API 서버 
## 데이터베이스 ERD

ERD (Entity-Relationship Diagram) 는 데이터베이스의 구조를 시각적으로 표현한 다이어그램.

### ERD의 주로 요소
  * 엔티티 (Entitiy) : 데이터베이스의 테이블을 나타냄
  * 속성 (Attribute) : 엔티티의 컬럼(필드)을 나타냄
  * 관계 (Relationship) : 엔티티 간의 연결을 나타냄

### 디렉토리 파일 구조와 이유 ( 기본 패키지 제외 )

 * 'controller/' : REST API 요청을 처리하는 컨트롤러 클래스
   * 'AuctionController.java' : 경매 요청을 처리
   * 'BidController.java' : 입찰 요청을 처리
   * 'BidderController.java' : 입찰자 요청을 처리
   *  *이유 : 클라이언트의 요청을 처리, 응답을 반환* 
   
 
 * 'model/' : 데이터베이스 테이블 매핑
   * 'Auction.java' : 경매 엔티티
   * 'Bid.java' : 입찰 엔티티
   * 'Bidder.java' : 입찰자 엔티티
   * *이유 : 데이터베이스 테이블의 구조를 정의*
 
 * 'repository/' : 데이터베이스와 상호작용하는 레포지토리
   * 'AuctionRepository.java' : 경매 데이터 처리
   * 'BidRepository.java' : 입찰 데이터 처리
   * 'BidderRepository.java' : 입찰자 데이터 처리
   *  *이유 : 데이터베이스에 직접 접근하여 데이터를 저장, 조회*
 
 * 'service/' : 비즈니스 로직 처리
   * 'AuctionService.java' : 경매 비즈니스 로직 처리
   * 'BidService.java' : 입찰 비즈니스 로직
   * *이유 : 실제 비즈니스 로직 처리 (중요)*

   
### 미술품 경매 서버 의 RED

* Auction
   * 'id' : 경매 ID (Primary Key)
   * 'name' : 경매 이름
   * 'endDate' : 경매 종료 날짜
   * 'highestBid' : 가장 높은 입찰 금액
   * 'highestBidderId' : 가장 높은 입찰자 ID
   * 'ended' : 경매 종료 여부 (boolean)
 
* Bid
   * 'id' : 입찰 ID (Primary Key)
   * 'amount' : 입찰 금액
   * 'auction_id' : 경매 ID (Foreign Key)
   * 'bidder_id' : 입찰자 ID (Foreign Key)
 
* Bidder
   * 'id' : 입찰자 ID (Primary Key)
   * 'name' : 입찰자 이름
   * 'money' : 입찰자의 자금
 
 하나의 경매는 여러 입찰을 가질 수 있고, 하나의 입찰자는 여러 번 입찰 할 수 있는 One-to-Many 의 구조로 구성
 
 
 ## 경매 등록 API
 
 ### 경매 등록 API의 목적 
 새로운 경매를 데이터베이스에 등록하기 위해 생성. 클라이언트가 경매 정보를 서버에 요청하면, 그 정보들을 데이터베이스에 저장.

 코드
```
@RestController
@RequestMapping("/auction")
public class AuctionController {

    @Autowired
    private AuctionService auctionService;

    @PostMapping
    public Auction createAuction(@RequestBody Auction auction) {
        return auctionService.createAuction(auction.getName(), auction.getEndDate());
    }
}
```

### 작동 과정

1. 클라이언트 요청 : 클라이언트가 경매 이름과 종료 날짜를 입력하면 이 정보를 JSON 형식으로 서버에 전달. @PostMapping 은 이 메서드가 HTTP POST  요청을 처리한다는 것을 나타내는 어노테이션.  <br>*( JSON 형식을 사용 할 때 @PostMapping에 produces = {"application-json"} 을 입력해도 되지만, 기본적으로 JSON 형식을 사용 하기 때문에 필요 X )*

2. 컨트롤러 처리 : RESTful 웹 서비스의 엔드포인트(URL)를 처리하는 역할을 하는 @RestController 어노테이션을 사용. @RequestMapping("/auction")은 이 컨트롤러가 "/auction" 경로로 들어오는 요청을 처리할 것임을 의미. 'createAuction' 메서드는 '@RequestBody' 를 사용하여 JSON 요청 본문을 'Auction' 객체로 변환하고 'AuctionService' 로 전달.

3. 서비스 처리 : 'AuctionService' 클래스는 ' creatAuction' 메서드를 통해 새로운 경매 객체를 생성. 데이터베이스에 저장 하기 전, 경매 이름, 종료 날짜 등을 필드에 적절히 설정. ( 서비스 에서 처리되는 이유는 비즈니스 로직을 컨트롤러에서 분리 해 코드의 가독성을 높이고, 재사용성을 높이기 위함 )

4. 데이터베이스 저장 : 서비스에서 생성된 경매 객체는 'AuctionRepository' 를 통해 데이터 베이스에 저장. Spring Data JPA 의 'save' 메서드는 데이터베이스에 객체를 저장하고, 저장된 객체를 반환 ( 경매 ID는 자동으로 생성되며, 경매가 성공적으로 생성 되었음을 반환 )

### 경매 등록 API 과정에서 데이터베이스에 저장되는 정보
 
 * 경매 이름 ('name')
 * 경매 종료 날짜 ('endDate')
 * 최고 입찰가 ('highestBid', 기본값 0)
 * 최고 입찰자 ID ('hightestBidderId', 기본값 null)
 * 경매 종료 여부 ('ended', boolean 기본값 false)



## 입찰 API

### 입찰 API 의 목적
특정 경매에 대한 입찰을 수행하는 기능. 클라이언트가 입찰 금액과 입찰자 ID를 서버에 요청하면, 그 정보들을 데이터베이스에 저장, 경매의 최고 입찰가를 업데이트.

코드
```
@RestController
@RequestMapping("/bid")
public class BidController {

    @Autowired
    private BidService bidService;

    @PostMapping
    public String placeBid(@RequestBody BidRequest bidRequest) {
        return bidService.placeBid(bidRequest.getAuctionId(), bidRequest.getBidderId(), bidRequest.getAmount());
    }
}
```

### 작동 과정

1. 클라이언트 요청 : 클라이언트는 입찰 금액, 경매 ID, 입찰자 ID 의 JSON 데이터를 'POST/bid' 엔드포인트로 요청을 전송

2. 컨트롤러 처리 : 'BidController' 는 이 요청을 받고 'BidRequest' 객체로 변환. 이 객체는 입찰에 필요한 정보 (경매 ID, 입찰자 ID, 입찰 금액) 를 포함. 그리고 'BidService'의 'placeBid' 메서드를 호출하여 입찰 작업 수행

3. 서비스 처리 : 'BidService'는 주어진 경매 ID와 입찰자 ID로 데이터베이스에서 해당 경매와 입찰자를 조회. 입찰 금액이 유효하고, 경매가 아직 진행 중 이라면 입찰 정보를 데이터베이스에 저장하고, 경매의 최고 입찰가와 최고 입찰자 정보를 업데이트

4. 데이터베이스 저장 : 새로운 입찰 정보가 'Bid' 테이블에 저장되고, 'Auction' 테이블의 최고 입찰가와 최고 입찰자 ID가 업데이트 됨

### 입찰 등록 API 과정에서 데이터베이스에 저장되는 정보
 
 * 입찰 금액 ('amount')
 * 경매 ID ('auction_id')
 * 입찰자 ID ('bidder_id')


## 경매 종료 API

### 경매 종료 API 의 목적
데이터베이스와 상호작용 하여 경매 상태를 변경. 최고 입찰자의 자금을 차감하고, 그 결과를 클라이언트에게 전달

 코드
```
@PostMapping("/end")
public String endAuction(@RequestBody AuctionIdRequest auctionIdRequest) {
    return auctionService.endAuction(auctionIdRequest.getAuctionId());
}
```

### 작동 과정

1. 클라이언트 요청 : 경매 ID를 포함한 JSON 데이터를 서버로 보내는 POST 요청 ( 경매 등록, 입찰 API 와 동일한 구조)

2. 컨트롤러 처리 : 'AuctionController'는 이 요청을 받아 'AuctionIdRequest' 객체로 변환 후, 'AuctionService'의 'endAuction' 메서드로 전달

3. 서비스 처리 : 'AuctionService' 는 경매를 조회 후 , 경매가 종료 될 수 있는 상태인지 확인. 최고 입찰자와 최고 입찰 금액을 확인 한 후, 최고 입찰자에게 소유권을 넘기고, 그 입찰자의 자금에서 입찰 금액을 차감, 경매의 상태를 '종료됨 (true)' 로 업데이트

4. 데이터베이스 저장 : 경매의 종료 여부가 데이터베이스에 저장, 최고 입찰자의 자금이 차감.('Auction' 테이블의 'ended' 와 'Bidder' 테이블의 'money' 에 반영 )


### 입찰 등록 API 과정에서 데이터베이스에 저장되는 정보

 * 경매 종료 여부 (ended) 
 * 최고 입찰자의 자금 차감 (Bidder 테이블의 money 필드 업데이트)


## 개발한 API 들에 대한 설명 및 용도

### 경매 등록 API
 * 엔드포인트 : 'POST /auction'
 * 설명 : 새로운 경매 생성
 * 용도 : 경매 정보(이름, 종료 날짜) 를 받아 데이터 베이스에 저장

### 경매 조회 API
 * 엔드포인트 : 'GET /auction'
 * 설명 : 모든 경매를 조회
 * 용도 : 데이터베이스에 저장된 모든 경매 정보를 반환

### 경매 ID 조회 API
 * 엔드포인트 : 'GET /auction/{id}
 * 설명 : 특정 ID의 경매를 조회
 * 용도 : 특정 경매의 정보 반환

### 입찰 API
 * 엔드포인트 : 'POST /bid'
 * 설명 : 특정 경매에 대해 입찰을 수행
 * 용도 : 입찰 정보를 받아 데이터베이스에 저장, 경매의 최고 입찰가와 최고 입찰자 정보 업데이트

### 경매 종료 API
 * 엔드포인트 : 'POST /auction/end'
 * 설명 : 특정 경매를 종료하고 최고 입찰자에게 소유권을 넘김
 * 용도 : 경매 종료시 입찰자 정보를 업데이트 하고 경매를 종료

### 입찰자 등록 API
 * 엔드포인트 : 'POST /bidder'
 * 설명 : 새로운 입찰자를 생성
 * 용도 : 입찰자 정보를 받아 데이터베이스에 저장

### 입찰자 조회 API
 * 엔드포인트 : 'GET /bidder/{id}'
 * 설명 : 특정 입찰자의 정보를 조회
 * 용도 : 특정 입찰자의 상세정보를 반환
   



