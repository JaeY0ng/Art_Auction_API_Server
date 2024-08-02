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
   * *이유 : 클라이언트의 요청을 처리, 응답을 반환* 
   
 
 * 'model/' : 데이터베이스 테이블 매핑
   * 'Auction.java' : 경매 엔티티
   * 'Bid.java' : 입찰 엔티티
   * 'Bidder.java' : 입찰자 엔티티
   *  *이유 : 데이터베이스 테이블의 구조를 정의*
 
 * 'repository/' : 데이터베이스와 상호작용하는 레포지토리
   * 'AuctionRepository.java' : 경매 데이터 처리
   * 'BidRepository.java' : 입찰 데이터 처리
   * 'BidderRepository.java' : 입찰자 데이터 처리
   *  *이유 : 데이터베이스 작업을 처리*
 
 * 'service/' : 비즈니스 로직 처리
   * 'AuctionService.java' : 경매 비즈니스 로직 처리
   * 'BidService.java' : 입찰 비즈니스 로직
   * *이유 : 비즈니스 로직 처리
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
 
 ## 경매 등록 API 작동 과정
 1. 경매를 생성 할 때 경매 이름과 종료 날짜 등 을 JSON 데이터로 POST 요청을 할 수 있게 @PostMapping 어노테이션을 이용 함. <br>*(( JSON 형식을 사용 할 때 @PostMapping에 produces = {"application-json"} 을 입력해도 되지만, 기본적으로 JSON 형식을 사용 하기 때문에 필요 X ))*

 2. 요청 받은 JSON 데이터를 매핑 하기 위해 @RequestBody 어노테이션을 이용

 3. 'createAuction' 메서드를 통해 'AuctionService' 로 데이터 전달

 4. 'AuctionService' 로 전달 받은 데이터를 저장 하기 위해 'AuctionRepository' 를 이용 ('name'과 'endDate' 등 저장)

코드
```
@PostMapping
public Auction createAuction(@RequestBody Auction auction) {
    return auctionService.createAuction(auction.getName(), auction.getEndDate());
}
```

## 입찰 API 작동 과정
1. 입찰 금액, 경매 ID, 입찰자 ID 의 JSON 데이터를 POST 요청으로 전송
 
