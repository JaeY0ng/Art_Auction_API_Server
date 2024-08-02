# Art_Auction_API_Server
미술 경매 API 서버 
## 데이터베이스 ERD

ERD 는 데이터베이스의 구조를 시각적으로 표현한 다이어그램.

### ERD의 주로 요소
  * 엔티티 (Entitiy) : 데이터베이스의 테이블을 나타냄
  * 속성 (Attribute) : 엔티티의 컬럼(필드)을 나타냄
  * 관계 (Relationship) : 엔티티 간의 연결을 나타냄

### 미술품 경매 시스템의 RED

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
 
  
