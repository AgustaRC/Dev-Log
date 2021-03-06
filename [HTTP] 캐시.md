# 웹 캐시가 주는 혜택
  * 불필요한 데이터 전송을 줄여 네트워크 비용을 줄인다.
  * 네트워크 병목을 줄여준다. 
  * 원 서버에 대한 요청을 줄여주기 때문에 서버에 대한 부하를 줄일 수 있다.
  * 거리로 인한 지연을 줄여준다.
</br>  
  
# 불필요한 데이터 전송
  * 복수의 클라이언트가 자주 쓰이는 원 서버 페이지에 접근할 때, 서버는 같은 문서를 클라이언트에게 각각 한 번씩 전송하게 된다.
  * **이러한 불필요한 데이터 전송은**
    * 네트워크 대역폭을 잡아먹고
    * 전송을 느리게 만들고
    * 웹 서버에 부하를 준다.
  * 캐시를 이용하면, 첫 번째 서버 응답은 캐시에 보관된다.
  * 캐시 된 사본이 뒤이은 요청들에 대한 응답으로 사용될 수 있기 때문에, 원 서버가 중복해서 트래픽을 주고받는 낭비가 줄어들게 된다.
</br>

# 대역폭 병목
  * 캐시는 네트워크 병목을 줄여준다.
  * 많은 네트워크가 원격 서버보다 로컬 네트워크 클라이언트에 더 넓은 대역폭을 제공한다.
  * 클라이언트들이 서버에 접근할 때의 속도는, 그 경로에 있는 가장 느린 네트워크의 속도와 같다. 
  * 만약 클라이언트가 빠른 LAN에 있는 캐시로부터 사본을 가져온다면, 캐싱은 성능을 대폭 개선할 수 있을 것이다.
</br>

# 거리로 인한 지연
  * 대역폭이 문제가 되지 않더라도, 거리가 문제가 될 수 있다.
  * 모든 네트워크 라우터는 제각각 인터넷 트래픽을 지연시킨다.
  * 빛의 속도(30만 킬로미터/초)로 인한 지연이 발생할 수 있다.
    * 병렬 커넥션이라 할지라도 왕복으로 주고받는 횟수에 따라 지연이 커질 것이다.
  * 기계실 근처에 캐시를 설치해서 문서가 전송되는 거리를 수천 킬로미터에서 수십 미터로 줄일 수 있다.
</br>

# 적중(Cache hit)과 부적중(Cache miss)
  * 캐시가 세상 모든 문서의 사본을 저장하지는 않는다.
  * 캐시에 요청이 도착했을 때, 대응하는 사본이 있다면 Cache hit, 대응하는 사본이 없다면 Cache miss
  * Cache miss 발생 시 원 서버로 요청이 전달된다.
  * **재검사(Revalidation)**
    * 원 서버 콘텐츠가 변경될 수 있기 때문에, 캐시는 갖고 있는 사본이 최신인지 서버를 통해 점검해야 한다.
    * 캐시는 스스로 원한다면 언제든지 사본을 재검사할 수 있다.
      * 하지만 캐시가 문서를 수백만 개씩 갖고 있는 경우가 흔한데 비해 네트워크 대역폭은 부족하다.
      * 그러므로 대부분의 캐시는 그 사본이 검사를 할 필요가 있을 정도로 충분히 오래된 경우에만 재검사를 한다.
    * 재검사를 위해 원 서버에게 보내는 ```GET``` 요청에 ```If-Modified-Since``` 헤더를 추가
    * ```GET If-Modified-Since``` 요청은 캐시 된 시간 이후에 변경된 경우에만 사본을 보내달라는 의미
    </br>  
    
    * 재검사 적중 (느린 적중)  
      * 캐시가 사본의 재검사가 필요할 때, 원 서버에 작은 재검사 요청을 보낸다.
      * 콘텐츠가 변경되지 않았다면, 서버는 ```304 Not Modified``` 응답을 전달
      * 사본이 아직 유효함을 알게 된 캐시는 해당 사본을 클라이언트에게 제공한다. 
      * 순수 캐시 적중보다 느린데, 원 서버와 검사를 할 필요가 있기 때문이다.
      * 하지만 캐시 부적중보다는 빠르다. 서버로부터 객체 데이터를 받아올 필요가 없기 때문에
    </br>
    
    * 재검사 부적중
      * 만약 서버 객체가 캐시 된 사본과 다르다면, 서버는 콘텐츠 전체와 함께 평범한 ```HTTP 200 OK``` 응답을 클라이언트에게 보낸다.
    </br>
    
    * 객체 삭제
      * 만약 서버 객체가 삭제되었다면, 서버는 ```404 Not Found``` 응답을 돌려보내며, 캐시는 사본을 삭제한다.
    </br>
    
  * **적중률**
    * 캐시가 요청을 처리하는 비율
    * 적중률은 예측하기 어렵지만 오늘날 적중률 40% 면 웹 캐시로 괜찮은 편이다.
  </br>
  
  * **바이트 적중률**
    * 캐시를 통해 제공된 모든 바이트의 비율
    * 트래픽이 절감된 정도를 포착해낸다.
    * 문서 적중률을 개선하면 전체 대기시간(지연)이 줄어들고
    * 바이트 적중률의 개선은 대역폭 절약을 최적화한다.
  </br>
  
  * **적중과 부적중의 구별**
    * HTTP는 클라이언트에게 응답이 캐시 적중인지 원 서버 접근인지 말해줄 수 있는 방법을 제공하지 않는다.
    * 클라이언트가 응답이 캐시에서 왔는지 알아내는 방법
      * 응답의 ```Date``` 헤더 값을 현재 시각과 비교하여, 응답의 생성일이 더 오래되었다면 응답이 캐시 된 것
      * 응답이 얼마나 오래되었는지 말해주는 ```Age``` 헤더를 이용
</br>

# 캐시 토폴로지
  * **개인 전용 캐시(Private cache)**
    * 작고 저렴하다.
    * 웹브라우저는 개인 전용 캐시를 내장하고 있다.
    * 사용자가 캐시 사이즈와 설정을 수정할 수 있도록 허용한다.
  </br>
  
  * **공용 캐시(Public cache)**
    * 프락시 서버 혹은 프락시 캐시라고 불리는 특별한 종류의 공유된 프락시 서버
    * 불필요한 트래픽을 줄일 수 있다. 
    * 자주 찾는 객체를 단 한 번만 가져와 모든 요청에 대해 공유된 사본을 제공함으로써 네트워크 트래픽을 줄인다.
    * 사용자 집단의 다양한 관심사들을 캐시 하기 때문에, 캐시가 보관하는 자주 쓰이는 문서들이 특정 개인의 관심사에 의해 지워지는 일이 없도록 충분히 커야 한다. 
  </br>
 
  * **프락시 캐시 계층**
    * 작은 캐시에서 캐시 부적중이 발생했을 때 더 큰 부모 캐시가 트래픽을 처리하도록 하는 계층을 만드는 방식이 합리적인 경우가 많다.
      * 클라이언트 주위에는 작고 저렴한 캐시를 사용하고, 부모 캐시는 많은 사용자들에 의해 공유되는 문서를 유지하기 위해 더 크고 강력한 캐시를 사용하자는 것
      * 부모 캐시는 관심사가 제각각인 많은 자식 캐시들로부터의 트래픽 모두를 감당해야 하므로, 더 크고 더 고성능이어야 한다.
      </br>
    * 캐시 계층이 깊다면 요청은 캐시의 긴 연쇄를 따라가게 된다. 
  </br>
  
  * **캐시망, 콘텐츠 라우팅, 피어링**
    * 캐시망 안에서 콘텐츠 라우팅을 위해 설계된 캐시들은 다음에 나열된 일들을 모두 할 수 있을 것이다.
      * URL에 근거하여, 부모 캐시와 원 서버 중 하나를 동적으로 선택
      * URL에 근거하여 특정 부모 캐시를 동적으로 선택
      * 부모 캐시에게 가기 전에, 캐시된 사본을 로컬에서 찾아본다
      * 다른 캐시들이 그들의 캐시된 콘텐츠에 부분적으로 접근할 수 있도록 허용하되, 그들의 캐시를 통한 Internet trasit은 허용하지 않는다.
      </br>
    * 형제 캐시
      * 선택적인 피어링을 지원하는 캐시
      * HTTP는 형제 캐시를 지원하지 않는다.
      * 인터넷 캐시 프로토콜(ICP)이나 하이퍼텍스트 캐시 프로토콜(HTCP) 같은 프로토콜을 이용해 HTTP를 확장
  </br>
 
# 캐시 처리 단계
  * **1. 요청받기**
    * 캐시는 네트워크로부터 도착한 요청 메시지를 읽는다.
    * 고성능 캐시는 여러 개의 들어오는 커넥션들로부터 데이터를 동시에 읽어들이고 메시지 전체가 도착하기 전에 트랜잭션 처리를 시작한다.
    </br>
    
  * **2. 파싱**
    * 캐시는 요청 메시지를 여러 부분으로 파싱 하여 헤더 부분을 조작하기 쉬운 자료 구조에 담는다. 
    * 헤더 부분에 대해 소문자나 날짜 형식의 차이와 같은 중요하지 않은 차이점이 모두 무시되도록 정규화할 책임을 가족 있다.
    </br>
    
  * **3. 검색**
    * 캐시는 로컬 복사본이 있는지 검사하고, 사본이 없다면 사본을 받아와서 로컬에 저장한다.
    * 로컬 복사본은 메모리나 디스크 혹은 다른 컴퓨터에 있을 수도 있다.
    * 만약 문서를 로컬에서 가져올 수 없다면, 캐시는 상황이나 설정에 따라서 그것을 원 서버나 부모 프락시에서 가져오거나 혹은 실패를 반환한다.
    * 캐시 된 객체는 서버 응답 본문과 원 서버 응답 헤더를 포함하고 있다.
    * 캐시 된 객체는 객체가 얼마나 오랫동안 캐시에 머무르고 있었는지를 알려주는 기록이나 얼마나 자주 사용되었는지 등에 대한 메타데이터를 포함한다.
    </br>
    
  * **4. 신선도 검사**
    * 캐시는 캐시 된 사본이 충분히 신선한지 검사하고, 신선하지 않다면 변경사항이 있는지 서버에게 물어본다.
    * HTTP는 캐시가 일정 기간 동안 서버 문서의 사본을 보유할 수 있도록 해준다.
    * 캐시는 그 문서를 제공하기 전에 문서에 어떤 변경이 있었는지 검사하기 위해 서버와 재검사를 해야 한다. 
    </br>
    
  * **5. 응답 생성**
    * 캐시는 새로운 헤더와 캐시된 본문으로 응답 메시지를 만든다.
    * 캐시는 캐시 된 서버 응답 헤더를 토대로 응답 헤더를 생성한다.
    * 캐시는 캐시 신선도 정보를 삽입하며(```Cache-Control```, ```Age```, ```Expires``` 헤더), 또 요청이 프락시 캐시를 거쳐갔음을 알려주기 위해 종종 Via 헤더를 포함시킨다.
    * ```Date``` 헤더를 조정해서되는 안 된다. ```Date``` 헤더는 그 객체가 원 서버에서 최초로 생겨난 일시를 표현하는 것
    </br>
    
  * **6. 전송**
    * 캐시는 네트워크를 통해 응답을 클라이언트에게 돌려준다.
    * 모든 프락시 서버들과 마찬가지로, 프락시 캐시는 클라이언트와의 커넥션을 유지해야 한다. 
    * 고성능 캐시는 종종 로컬 저장 장치와 네트워크 I/O 버퍼 사이에서 문서의 콘텐츠 복사를 피함으로써 데이터를 효과적으로 전송하기 위해 노력한다.
    </br>
    
  * **7. 로깅**
    * 선택적으로, 캐시는 로그파일에 트랜잭션에 대해 서술한 로그 하나를 남긴다. 
    * 대부분의 캐시는 로그 파일과 캐시 사용에 대한 통계를 유지한다.
    * 각 캐시 트랜잭션이 완료된 후, 캐시는 통계 캐시 적중과 부적중 횟수에 대한 통계를 갱신하고 로그 파일에 요청 종류, URL 그리고 무엇이 일어났는지 알려주는 항목을 추가한다.
    </br>
    
  * **캐시 요청 플로우**
    </br>
    </br>
    ![Alt text](/images/cache-processing-steps.png)
    </br>
    </br>

# 사본을 신선하게 유지하기
  * **문서 만료**
    * HTTP는 ```Cache-Control```과 ```Expires```라는 특별한 헤더들을 이용해서 원 서버가 각 문서에 유효기간을 붙일 수 있게 해준다
    * 캐시 문서가 만료되기 전에, 캐시는 필요하다면 서버와의 접촉 없이 사본을 제공할 수 있다.
    * 캐시 문서가 만료되면, 캐시는 반드시 서버와 문서에 변경된 것이 있는 검사해야 하며, 변경된 내용이 있다면 새 유효기간과 함께 사본을 얻어와야 한다.
    </br>
    
  * **유효기간과 나이**
    * ```HTTP/1.0 Expires```나 ```HTTP/1.1 Cache-Control:max-age``` 응답 헤더를 이용해서 유효기간을 명시한다.
    * ```Expires```와 ```Cache-Control:max-age```는 기본적으로 같은 일을 하지만, 절대 시간은 컴퓨터의 시계가 올바르게 맞추어져 있을 것을 요구한다. 
    * ```Cache-Control:max-age```
      * 문서의 최대 나이를 정의한다.
      * 문서가 처음 생성된 이후부터, 제공하기엔 더 이상 신선하지 않다고 간주될 때까지 경과한 시간의 합법적인 최댓값(초 단위)이다. 
      * ```Cache-Control:max-age=484200```
      </br>
    * ```Expires```
      * 절대 유효기간을 명시한다. 
      * 만약 유효기간이 경과했다면, 그 문서는 더 이상 신선하지 않다.
      * ```Expires: Sun, 24 Mar 2019, 11:00:00 GMT```
      </br>
      
  * **서버 재검사**
    * 캐시 된 문서가 만료되었다는 것은, 그 문서가 원 서버에 현재 존재하는 것과 실제로 다르다는 것은 아니다.
    * 단지 이제 검사할 시간이 되었음을 뜻한다.
    * 재검사 결과 콘텐츠가 변경되었다면, 캐시는 그 문서의 새로운 사본을 가져와 오래된 데이터 대신 저장한 뒤 클라이언트에게도 보내준다.
    * 재검사 결과 콘텐츠가 변경되지 않았다면, 캐시는 새 만료일을 포함한 새 헤더들만 가져와서 캐시 안의 헤더들을 갱신한다.
    * 이점
      * 문서의 신서도를 매 요청마다 검증할 필요가 없다.
      * 문서가 만료되었을 때 한 번만 서버와 재검사하면 된다.
      * 서버 트래픽을 절약하고 사용자 응답 시간을 개선한다.
    </br>
    
  * **조건부 메서드와의 재검사**
    * HTTP는 캐시가 서버에게 ```조건부 GET```이라는 요청을 보낼 수 있도록 해준다.
    * 서버가 갖고 있는 문서가 캐시가 갖고 있는 것과 다른 경우에만 객체 본문을 보내달라고 하는 것
    * HTTP는 다섯 가지 조건부 요청 헤더를 정의한다. 그 중 캐시 재검사를 할 때 유용한 것은 ```If-Modified-Since```와 ```If-None-Match```이다.
    * ```If-Modified-Since: <date>```
      * 만약 문서가 주어진 날짜 이후로 수정되었다면 요청 메서드를 처리한다.
      * 캐시 된 버전으로부터 콘텐츠가 변경된 경우에만 콘텐츠를 가져오기 ```Last-Modified``` 서버 응답 헤더와 함께 사용된다.
      </br>
    * ```If-None-Match: <tags>```
      * 마지막 변경된 날짜를 맞춰보는 대신, 서버는 문서에 대한 일련번호와 같이 동작하는 특별한 태그를 제공할 수 있다.
      * ```If-None-Match``` 헤더는 캐시 된 태그가 서버에 있는 문서의 태그와 다를 때만 요청을 처리한다.
      </br>
    * ```If-Unmodified-Since```
      * 부분 문서 전송을 하면서, 전에 일부분을 가져왔던 문서가 지금도 여전히 변함이 없음을 확인하려고 할 때 유용
      </br>
    * ```If-Range```
      * 불완전한 문서의 캐싱을 지원하기 위해
      </br>
    * ```If-Match```
      * 웹 서버에 대한 동시성 제어를 할 때 유용
      </br>

  * **If-Modified-Since: 날짜 재검사**
    * 흔히 'IMS' 요청으로 불림
    * IMS 요청은 서버에게 리소스가 특정 날짜 이후로 변경된 경우에만 요청한 본문을 보내달라고 한다.
      * 만약 문서가 주어진 날짜 이후에 변경되었다면, ```If-Modified-Since``` 조건은 참이고, GET 요청은 평범하게 성공한다. 새 문서가, 새로운 만료 날짜와 그 외 다른 정보들이 담긴 헤더들과 함께 캐시에 반환된다.
      * 만약 문서가 주어진 날짜 이후에 변경되지 않았다면 조건은 거짓이고, 서버는 작은 ```304 Not Modified``` 응답 메시지를 클라이언트에게 돌려준다. 효율을 위해 본문은 보내지 않는다. 응답은 헤더들을 포함하지만, 원래 돌려줘야 할 것에서 갱신이 필요한 것만을 보내준다. 
      </br>
    * ```If-Modified-Since``` 헤더는 서버 응답 헤더의 ```Last-Modified``` 헤더와 함께 동작한다.
    * 원 서버는 제공하는 문서에 최근 변경 일시를 붙인다.
    * 캐시가 캐시 된 문서를 재검사하려고 할 때, 캐시 된 사본이 마지막으로 수정된 날짜가 담긴 ```If-Modified-Since``` 헤더를 포함한다.
      * ```If-Modified-Since: <캐시된 마지막 수정일>```
      </br>

  * **If-None-Match: 엔터티 태그 재검사**
    * 최근 변경 일시 재검사가 적절히 행해지기 어려운 상황
      * 어떤 문서는 일정 시간 간격으로 다시 쓰이지만 실제로는 같은 데이터를 포함하고 있다. 내용에는 아무런 변화가 없더라도 변경 시각은 바뀔 수 있다.
      * 어떤 문서들의 변경은 전 세계의 캐시들이 그 데이터를 다시 읽어들이기엔 사소한 것일 수도 있다. (예: 철자나 주석의 변경)
      * 어떤 서버들은 그들이 갖고 있는 페이지에 대한 최근 변경 일시를 정확하게 판별할 수 없다.
      * 1초보다 작은 간격으로 갱신되는 문서를 제공하는 서버들에게는, 변경일에 대한 1초의 정밀도는 충분하지 않을 수 있다.
      </br>
    * 문서를 변경했을 때, 문서의 엔터티 태그를 새로운 버전으로 표현할 수 있다.
    * 캐시가 엔터티 태그 ```"v2.6"```인 문서를 갖고 있다고 했을 때 태그가 ```"v2.6"```이 아닌 경우에만 새 객체를 달라고 요청하는 방법으로 유효한지를 재검사할 수 있다. 
    * 캐시가 객체에 대한 여러 개의 사본을 갖고 있는 경우, 그 사실을 서버에게 알리기 위해 하나의 ```If-None-Match``` 헤더에 여러 개의 엔터티 태그를 포함시킬 수 있다. 
      * ```If-None-Match: "v2.6", "v2.7"```
      </br>
      
  * **약한 검사기와 강한 검사기**
    * 서버는 때때로 모든 캐시 된 사본을 무효화시키지 않고 문서를 살짝 고칠 수 있도록 허용하고 싶은 경우가 있다.
    * HTTP/1.1은 콘텐츠가 조금 변경되었더라도 "그 정도면 같은 것"이라고 서버가 주장할 수 있도록 해주는 ```약한 검사기(Weak validator)```를 지원한다.
    * ```강한 검사기(Strong validator)```는 콘텐츠가 바뀔 때마다 바뀐다.
    * 서버는 ```W/``` 접두사로 약한 검사기를 구분한다.
      * ```ETag: W/"v2.6"```
      * ```If-None-Match: W/"v2.6"```
      </br>
    * 강한 엔터티 태그는 대응하는 엔터티 값이 어떻게 바뀌든 매번 반드시 같이 바뀌어야 하고, 약한 엔터티 태그는 대응하는 엔터티에 유의미한 변경이 있을 때마다 변경되어야 한다. 
    </br>
    
  * **언제 엔터티 태그를 사용하고 언제 ```Last-Modified``` 일시를 사용하는가**
    * HTTP/1.1 클라이언트는 만약 서버가 엔터티 태그를 반환했다면, 반드시 엔터티 태그 검사기를 사용해야 한다.
    * 만약 서버가 ```Last-Modified``` 값만을 반환했다면, 클라이언트는 ```If-Modified-Since``` 검사를 사용할 수 있다. 
    * 만약 엔터티 태그와 최근 변경 일시가 모두 사용 가능하다면, 클라이언트는 각각을 위해 두 가지의 재검사 정책을 모두 사용해야 한다. 
    * 만약 HTTP/1.1 캐시나 서버가 ```If-Modified-Since```와 엔터티 태그 조건부 헤더를 모두 받았다면, 요청의 모든 조건부 헤더 필드의 조건에 부합되지 않는 한 ```304 Not Modified``` 응답을 반환해서는 안 된다.
    </br>
    
# 캐시 제어
  * **HTTP는 문서가 만료되기 전까지 얼마나 오랫동안 캐시 될 수 있게 할 것인지 서버가 설정할 수 있는 여러 가지 방법을 정의한다.**
    * ```Cache-Control: no-store``` 헤더를 응답에 첨부할 수 있다.
    * ```Cache-Control: no-cache``` 헤더를 응답에 첨부할 수 있다.
    * ```Cache-Control: must-revalidate``` 헤더를 응답에 첨부할 수 있다.
    * ```Cache-Control: max-age``` 헤더를 응답에 첨부할 수 있다.
    * ```Expires``` 날짜 헤더를 응답에 첨부할 수 있다.
    * 아무 만료 정보도 주지 않고, 캐시가 스스로 ```휴리스틱 만료 알고리즘```으로 결정하게 할 수 있다.
    </br>

  * **no-cache와 no-store 응답 헤더**
    * 캐시가 검증되지 않은 캐시 된 객체로 응답하는 것을 막는다.
      * ```Cache-Control: no-store```
      * ```Cache-Control: no-cache```
      * ```Pragma: no-cache```
      </br>
    * ```no-store```가 표시된 응답은 캐시가 그 응답의 사본을 만드는 것을 금지한다. 
      * 프락시 서버가 그러는 것처럼, 클라이언트에게 ```no-store``` 응답을 전달하고 나면 객체를 삭제할 것이다.
      </br>
    * ```no-cache```로 표시된 응답은 로컬 캐시 저장소에 저장될 수 있다. 
      * 다만 먼저 서버와 재검사를 하지 않고서는 캐시에서 클라이언트로 제공될 수 없을 뿐이다. 
      * 이 헤더의 더 나은 이름은 ```Do-Not-Serve-From-Cache-Without-Revalidation```일 것이다.
      </br>
    * ```Pragma: no-cache``` 헤더는 HTTP/1.0+와 하위호환성을 위해 HTTP/1.1에 포함되어 있다.
      * HTTP/1.1 애플리케이션은 ```Pragma: no-cache```만 이해할 수 있는 HTTP/1.0 애플리케이션에 대응해야 하는 경우가 아니라면 ```Cache-Control: no-cache```를 사용해야 한다.
      </br>
      
  * **Max-Age 응답 헤더**
    * 신선하다고 간주되었던 문서가 서버로부터 온 이후로 흐른 시간이고, 초로 나타낸다.
    * ```s-maxage``` 헤더는 ```max-age```와 동일하게 행동하지만 공유 캐시에만 적용된다.
      * ```Cache-Control: max-age=3600```
      * ```Cache-Control: s-maxage=3600```
      </br>
    * 서버는 ```max-age```를 0으로 설정함으로써, 캐시가 매 접근마다 문서를 캐시 하거나 리프레시하지 않도록 요청할 수 있다.
    </br>
    
  * **Expires 응답 헤더**
    * 더 이상 사용하지 않기를 원하는 ```Expires``` 헤더는 초 단위의 시간 대신 실제 만료 날짜를 명시한다.
    * HTTP를 설계한 사람들은, 많은 서버가 동기화되어 있지 않거나 부정확한 시계를 갖고 있기 때문에, 만료를 절대시각 대신 경과된 시간으로 표현하는 것이 낫다고 판단햇다. 
    </br>
    
  * **Must-Revalidate 응답 헤더**  
    * 캐시는 성능을 위해 신선하지 않은(만료된) 객체를 제공하도록 설정될 수 있다.
    * 만약 캐시가 만료 정보를 엄격하게 따르길 원한다면 ```must-revalidate``` 헤더를 사용한다. 
      * ```Cache-Control: must-revalidate```
      </br>
    * 캐시가 이 객체의 선신하지 않은 사본을 원 서버와의 최초의 재검사 없이는 제공해서는 안 됨을 의미한다.
    * 만약 캐시가 ```must-revalidate``` 신선도 검사를 시도했을 때 원 서버가 사용할 수 없는 상태라면, 캐시는 반드시 ```504 Gateway Timeout error```를 반환해야 한다.
    </br>
    
  * **휴리스틱 만료**
    * 만약 응답이 ```Cache-Control:max-age``` 헤더나 ```Expires``` 헤더 중 어느 것도 포함하지 않고 있다면, 캐시는 경험적인 방법으로(Heuristic) 최대 나이를 계산할 것이다.
    * 어떤 알고리즘이든 사용될 수 있지만, 계산 결과 얻은 최대 나잇값이 24시간보다 크다면, ```Heuristic Expiration``` 경고 헤더가 응답 헤더에 추가되어야 한다. 
    * ```LM 인자 알고리즘```
      * 문서가 최근 변경 일시를 포함하고 있다면 사용할 수 있다.
      * 최근 변경 일시를 문서가 얼마나 자주 바뀌는지에 대한 추정에 사용한다. 
        * 만약 캐시 된 문서가 마지막으로 변경된 것이 상당히 예전이라면, 그것은 아마 안정적인 문서일 것이고 갑자기 바뀔 가능성은 별로 크지 않을 것이므로, 캐시에 더 오래 보관하고 있어도 안전하다.
        * 만약 캐시 된 문서가 최근에 변경되었다면, 그것은 아마 자주 변경될 것이고, 따라서 우리는 그것을 서버와 재검사하기 전까지 짧은 기간 동안만 캐시해야 한다. 
        </br>
      * 실제 LM 인자 알고리즘은 캐시가 서버와 대화했을 때와 서버가 문서의 최근 변경 일시를 말해줬을 때의 시간차를 계산하고, 이 차의 일부분을 취하여, 이 일부분을 캐시의 신선도 지속기간으로 사용한다.
        * ```마지막_수정_이후로_경과한 시간 = max(0, 서버의_Date - 서버의_Last_modified)```
        * ```서버_신선도_한계 = int(마지막_수정_이후로_경과한_시간 * lm_인자)```
        </br>
      * 보통 휴리스틱 신선도 유지 기간의 상한은 1주일로 하지만, 보수적인 사이트는 하루로 설정한다.
      * 캐시는 일반적으로 신선도에 대한 아무런 단서가 없는 문서에 대해 기본 신선도 유지 기간을 설정한다(보통 한 시간이나 하루로).
      * 더 보수적인 캐시는 보통 이 휴리스틱 문서들에 대해 0의 신선도 수명을 설정하여, 캐시가 클라이언트에게 데이터를 제공할 때마다 아직 신선한지 검사하도록 강제한다. 
      </br>
      
  * **클라이언트 신선도 제약**
    * 웹 브라우저는 브라우저나 프락시 캐시의 신선하지 않은 콘텐츠를 강제로 갱신시켜주는 리프레시나 리로드 버튼을 갖고 있다.
    * 이 리프레시 버튼은 ```Cache-Control``` 요청 헤더가 추가된 ```GET``` 요청을 발생시켜서, 강제로 재검사하거나 서버로부터 콘텐츠를 무조건 가져온다.
    * 정확한 리프레시 동작은 각 브라우저나 문서, 중간 캐시 설정에 달려잇다.
    * 클라이언트는 성능, 신뢰성, 비용 개선을 위한 절충안으로 신선도 요구 사항을 느슨하게 하고자 할 수도 있다.
    * ```Cache-Control``` 요청 지시어
      * ```Cache-Control:max-stale, Cache-Control: max-stale = <s>```
        * 캐시는 신선하지 않은 문서라도 자유롭게 제공할 수 있다.
        * 만약 ```<s>``` 매개변수가 지정되면, 클라이언트는 만료시간이 그 매개변수의 값만큼 지난 문서도 받아들인다.
        * 이는 캐싱 규칙을 느슨하게 한다.
        </br>
      * ```Cache-Control: min-fresh = <s>```
        * 클라이언트는 지금으로부터 적어도 ```<s>```초 후까지 신선한 문서만을 받아들인다. 이것은 캐싱 규칙을 엄격하게 한다.
        </br>
      * ```Cache-Control: max-age = <s>```
        * 캐시는 ```<s>```초보다 오랫동안 캐시 된 문서를 반환할 수 없다. 
        * 나이가 유효기간을 넘어서게 되는 ```max-stale``` 지시어가 함께 설정되지 않는 이상, 이 지시어는 캐싱 규칙을 더 엄격하게 만든다.
        </br>
      * ```Cache-Control: no-cache, Pragma: no-cache```
        * 이 클라이언트는 캐시 된 리소스는 재검사하기 전에는 받아들이지 않을 것이다.
        </br>
      * ```Cache-Control: no-store```
        * 이 캐시는 저장소에서 문서의 흔적을 최대한 빨리 삭제해야 한다.
        * 그 문서에는 민감한 정보가 포함되어 있기 때문이다.
        </br>
      * ```Cache-Control: only-if-cached```
        * 클라이언트는 캐시에 들어있는 사본만을 원한다.
   
      
      


