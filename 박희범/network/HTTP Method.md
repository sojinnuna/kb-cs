### 3. HTTP Method 에 대해 설명해 주세요.
- HTTP(HyperText Transfer Protocol)
	인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약입니다
	
> HTTP 1.1  
	HTTP Method는 HTTP 요청 메시지의 start-line에 정의되어 있습니다  
	start-line에는 HTTP 메서드, 요청대상, HTTP 버전이 정의되어 있습니다  
	형식 : <HTTP-메서드> <리소스 경로> <HTTP-버전>  
	ex)GET /product HTTP/1.1    
	 !주의! 공백과 줄바꿈 문자를 유지 시켜줘야됨   
	 클라이언트 어플리케이션과 서버 어플리케이션 단에서 평문(ASCII)을 파싱해서 사용합니다.  

- HTTP 메소드 :  
	GET은 리소스 조회,  
	POST는 서버로 데이터를 전송, 데이터 등록을 할 때 사용됨  
	PUT은 데이터 대체를 의미, 데이터가 없으면 새로운 리소스를 생성  
	PATCH은 리소스의 부분적인 수정을 할 때 사용  
	DELETE: 데이터를 삭제할 때 사용  
    이외에도 CONNECT, HEAD, OPTIONS 등이 있습니다.  

- HTTP Method 멱등성  
	멱등성 : 동일한 연산을 여러번 수행해도 결과가 달라지지 않는 성질입니다.  
	멱등성을 따질땐 외부 요인으로 인한 변경은 고려하지 않습니다. 서버의 상태 기준으로 판단합니다.  
	멱등성 O : GET, PUT, DELETE   
	멱등성 X : POST, PATCH   
	> 서버 개발자는 위의 명세에 따라 멱등성을 구현해야한다.
	- why?  
		멱등성 보장이 신뢰될 때, 클라이언트는  추가적인 로직을 구현할 필요가 없습니다.
	- POST 메서드는 멱등성을 가지면 안되나요?  
		토스의 경우 POST 메서드에 멱등성을 보장하기 위해 멱등키를 사용합니다.
		> 토스페이먼츠에서 제공하는 모든 POST 메서드 API는 요청에 멱등키 헤더를 추가해서 사용할 수 있습니다. 그 외 메서드는 [자체적으로 멱등성을 보장](https://developer.mozilla.org/ko/docs/Glossary/Idempotent)합니다. GET 요청에 추가하는 멱등키 헤더는 무시됩니다.
			[토스 페이먼츠 API](https://docs.tosspayments.com/reference/using-api/authorization)
			에러 시나리오 대응 : [IETF 명세](https://datatracker.ietf.org/doc/html/draft-idempotency-header-01#section-2.7)

    - 토스 페이먼츠 예시 코드
      <details>
        <summary>클라이언트 코드 //TIMEOUT의 경우 요청을 재귀적으로 호출하네요</summary>

        ```javascript
          let idempotentKey = generateUUIDv4()
          function async cancelPayment(idempotencyKey: string) {
          try {
              return await axios.post("https://myshop/cancel-payment",
              {
                  orderId: UINQUE_ORDER_ID
                  amount: 100,
              },
              {
                  headers: {
                  "Idempotency-Key": idempotentKey // 헤더에 멱등키를 추가합니다.
                  }
              }
              )
          } catch(e) {
              if (e.name === "TIMEOUT") { // 타임아웃이 일어났을 때 같은 요청을 보낼 수 있습니다.
                  return await cancelPayment(idempotencyKey)
              }
              console.error("ERROR")
          }
          }
          const response = await cancelPayment(idempotentKey);
        ```
      </details>
      
      <details>
        <summary>서버 코드</summary>
             
        ```javascript
        const idempotencyResponses = new Map();

          let cancelReq = {
            orderId: req.body.orderId
            amount: req.body.amount,
          };

          let idempotencyKey = req.headers.idempotencyKey || null // 요청 헤더에서 멱등키를 가져옵니다.

          // 멱등키가 있고 멱등 응답도 저장되어 있다면 실제 처리하지 않고 저장된 응답을 내보냅니다.
          if (idempotencyKey != null && idempotencyResponses.has(idempotencyKey)) {
            const response = idempotencyResponses.get(idempotencyKey);
            return res.status(response.status).json(response);
          };

          const result = cancelProcessor.cancel(cancelReq); // 실제로 취소를 처리합니다.

          // 멱등키가 있으면 멱등응답을 저장합니다.
          if (idempotencyKey != null) {
            idempotencyResponses.set(idempotencyKey, result);
          }

          const responseBody = {
            message: `결제 취소 성공`,
          };
        ```
      </details>

        
- GET과 POST의 차이  
	GET은 캐싱이 가능합니다.
    > 여기서 말하는 캐싱은 클라이언트에 저장된 자원을 사용한다는 뜻입니다. 때문에 서버에서 응답을 보낼 때, 다시 자원을 보내지 않습니다. 

	HTTP 헤더에 Last-Modified, Cache-Control, ETAG(엔티티 태그)를 정의하여 캐싱을 지원합니다.   
	Last-Modified와 ETAG는 자원의 버전을 정의합니다, 이를 활용하여 재검증 요청을 할 수 있습니다.   
	예시) ETAG가 일치 시 서버에서 응답 코드를 304로 응답(응답 바디에는 아무것도 없음)
	
- POST와 PUT, PATCH의 차이는 무엇인가요?
	POST는 정확한 리소스의 위치를 모릅니다  
	PUT, PATCH는 리소스의 위치를 압니다   
	PUT은 리소스의 모든것을 대체합니다   
	PATCH는 리소스의 부분만 변경합니다   
	
- HTTP 1.1 이후로, GET에도 Body에 데이터를 실을 수 있게 되었습니다. 그럼에도 불구하고 왜 아직도 이런 방식을 지양하는 것일까요?  
	GET의 Body를 어떤 브라우저, 프록시 서버, 서버에서 지원하지 않을 수 있습니다. 이런 호환성을 고려하여 해당 방식을 권장하지 않습니다   
    그러나 쿼리가 매우 긴 경우, 브라우저마다 URL의 길이 제한이 있어 GET Method의 body를 사용하여 쿼리를 처리할 수 있습니다.

