<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/ann-rest-exceptions.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>REST 서비스에 대한 일반적인 요구 사항은 오류 응답 본문에 세부 정보를 포함하는 것입니다. Spring Framework는 "HTTP API에 대한 문제 세부 정보" 사양인 <a href="https://datatracker.ietf.org/doc/html/rfc7807">RFC 7807</a>을 지원합니다.</p>
<p>다음은 이 지원에 대한 주요 추상화입니다.</p>
<ul>
<li>
<p><code>ProblemDetail</code>  -  RFC 7807 문제 세부정보에 대한 표현입니다. 사양에 정의된 표준 필드와 비표준 필드에 대한 간단한 컨테이너입니다.</p>
</li>
<li>
<p><code>ErrorResponse</code>  - HTTP 상태, 응답 헤더 및 본문을 포함한 HTTP 오류 응답 세부 정보를 RFC 7807 형식으로 노출하는 계약입니다. 이를 통해 예외가 HTTP 응답에 매핑되는 방법에 대한 세부 정보를 캡슐화하고 노출할 수 있습니다. 모든 Spring MVC 예외는 이를 구현합니다.</p>
</li>
<li>
<p><code>ErrorResponseException</code>  - 다른 사람들이 편리한 기본 클래스로 사용할 수 있는 기본 <code>ErrorResponse</code> 구현입니다.</p>
</li>
<li>
<p><code>ResponseEntityExceptionHandler </code> - 모든 Spring MVC 예외 및 <code>ErrorResponseException</code>을 처리하고 본문으로 오류 응답을 렌더링하는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html">@ControllerAdvice</a>에 대한 편리한 기본 클래스입니다.</p>
</li>
</ul>
<h1 id="render">Render</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/ann-rest-exceptions.html#webflux-ann-rest-exceptions-render">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@ExceptionHandler</code> 또는 <code>@RequestMapping</code> 메서드에서 <code>ProblemDetail</code> 또는 <code>ErrorResponse</code>를 반환하여 RFC 7807 응답을 렌더링할 수 있습니다. 이는 다음과 같이 처리됩니다.</p>
<ul>
<li>
<p><code>ProblemDetail</code>의 <code>status</code> 속성은 HTTP 상태를 결정합니다.</p>
</li>
<li>
<p><code>ProblemDetail</code>의 <code>instance</code> 속성은 아직 설정되지 않은 경우 현재 URL 경로에서 설정됩니다.</p>
</li>
<li>
<p>콘텐츠 협상의 경우 Jackson <code>HttpMessageConverter</code>는 <code>ProblemDetail</code>을 렌더링할 때 "application/json"보다 "application/problem+json"을 선호하고 호환되는 미디어 유형이 없으면 이를 대체합니다.</p>
</li>
</ul>
<p>Spring WebFlux 예외 및 <code>ErrorResponseException</code>에 대해 RFC 7807 응답을 활성화하려면 <code>ResponseEntityExceptionHandler</code>를 확장하고 Spring 구성에서 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html">@ControllerAdvice</a>로 선언합니다. 핸들러에는 모든 내장 웹 예외를 포함하는 <code>ErrorResponse</code> 예외를 처리하는 <code>@ExceptionHandler</code> 메소드가 있습니다. 더 많은 예외 처리 메서드를 추가하고 보호된 메서드를 사용하여 모든 예외를 <code>ProblemDetail</code>에 매핑할 수 있습니다.</p>
<h1 id="non-standard-fields">Non-Standard Fields</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/ann-rest-exceptions.html#webflux-ann-rest-exceptions-non-standard">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>두 가지 방법 중 하나로 비표준 필드를 사용하여 RFC 7807 응답을 확장할 수 있습니다.</p>
<p>첫째, <code>ProblemDetail</code>의 "속성(properties)" <code>Map</code>에 삽입합니다. Jackson 라이브러리를 사용할 때 Spring Framework는 이 "속성" <code>Map</code>이 래핑 해제되어 응답에서 최상위 JSON 속성으로 렌더링되도록 보장하는 <code>ProblemDetailJacksonMixin</code>을 등록하며 마찬가지로 역직렬화 중 알 수 없는 속성이 이 <code>Map</code>에 삽입됩니다.</p>
<p><code>ProblemDetail</code>을 확장하여 전용 비표준 속성을 추가할 수도 있습니다. <code>ProblemDetail</code>의 복사 생성자를 사용하면 기존 <code>ProblemDetail</code>에서 하위 클래스를 쉽게 만들 수 있습니다. 이는 중앙에서 수행될 수 있습니다. 추가 비표준 필드가 있는 하위 클래스로 예외의 <code>ProblemDetail</code>을 다시 생성하는 <code>ResponseEntityExceptionHandler</code>와 같은 <code>@ControllerAdvice</code>에서.</p>
<h1 id="customization-and-i18n">Customization and i18n</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/ann-rest-exceptions.html#webflux-ann-rest-exceptions-i18n">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>오류 응답 세부 사항을 사용자 정의하고 국제화하는 것은 일반적인 요구 사항입니다. 구현 세부사항이 공개되는 것을 피하기 위해 Spring MVC 예외에 대한 문제 세부사항을 사용자 정의하는 것도 좋은 습관입니다. 이 섹션에서는 이에 대한 지원을 설명합니다.</p>
<p><code>ErrorResponse</code>는 "type", "title" 및 "detail"에 대한 메시지 코드와 "detail" 필드에 대한 메시지 코드 인수를 노출합니다. <code>ResponseEntityExceptionHandler</code>는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-functionality-messagesource">MessageSource</a>를 통해 이를 해결하고 그에 따라 해당 <code>ProblemDetail</code> 필드를 업데이트합니다.</p>
<p>메시지 코드의 기본 전략은 다음 패턴을 따릅니다.</p>
<ul>
<li>
<p>"type": <code>problemDetail.type.[fully qualified exception class name]</code></p>
</li>
<li>
<p>"title": <code>problemDetail.title.[fully qualified exception class name]</code></p>
</li>
<li>
<p>"detail": <code>problemDetail.[fully qualified exception class name][suffix]</code></p>
</li>
</ul>
<p><code>ErrorResponse</code>는 둘 이상의 메시지 코드를 노출할 수 있으며 일반적으로 기본 메시지 코드에 접미사를 추가합니다. 아래 표에는 Spring MVC 예외에 대한 메시지 코드와 인수가 나열되어 있습니다.</p>
<p></p>
<table id="mvc-ann-rest-exceptions-codes" class="tableblock frame-all grid-all stripes-odd stretch">





<thead>
<tr>
<th class="tableblock halign-left valign-top">예외</th>
<th class="tableblock halign-left valign-top">메시지 코드</th>
<th class="tableblock halign-left valign-top">메시지 코드 인수</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>AsyncRequestTimeoutException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ConversionNotSupportedException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 속성 이름, <code>{1}</code> 속성 값</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HandlerMethodValidationException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 모든 유효성 검사 오류를 나열합니다.
각 오류에 대한 메시지 코드 및 인수도 <code>MessageSource</code>를 통해 해결됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMediaTypeNotAcceptableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 지원되는 미디어 유형 목록</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMediaTypeNotAcceptableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값) + ".parseError"</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMediaTypeNotSupportedException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 지원되지 않는 미디어 유형, <code>{1}</code> 지원되는 미디어 유형 목록</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMediaTypeNotSupportedException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값) + ".parseError"</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMessageNotReadableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMessageNotWritableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpRequestMethodNotSupportedException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 현재 HTTP 메서드, <code>{1}</code> 지원되는 HTTP 메서드 목록</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MethodArgumentNotValidException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 전역 오류 목록, <code>{1}</code> 필드 오류 목록입니다.
각 오류에 대한 메시지 코드 및 인수도 <code>MessageSource</code>를 통해 해결됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingRequestHeaderException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 헤더 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingServletRequestParameterException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 요청 매개 변수 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingMatrixVariableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code>
</p><p> 매트릭스 변수 이름</p></td></tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingPathVariableException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 경로 변수 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingRequestCookieException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 쿠키 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MissingServletRequestPartException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 부분 이름</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>NoHandlerFoundException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>NoResourceFoundException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>TypeMismatchException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 속성 이름, <code>{1}</code> 속성 값</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>UnsatisfiedServletRequestParameterException</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">(기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>{0}</code> 매개 변수 조건 목록</p></td>
</tr>
</tbody>
</table>
<h1 id="client-handling">Client Handling</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/ann-rest-exceptions.html#webflux-ann-rest-exceptions-client">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>클라이언트 애플리케이션은 <code>WebClient</code>를 사용할 때 <code>WebClientResponseException</code>을 포착하거나 <code>RestTemplate</code>을 사용할 때 <code>RestClientResponseException</code>을 포착할 수 있으며 <code>getResponseBodyAs</code> 메소드를 사용하여 오류 응답 본문을 <code>ProblemDetail</code> 또는 <code>ProblemDetail</code>의 하위 클래스와 같은 대상 유형으로 디코딩할 수 있습니다.</p>