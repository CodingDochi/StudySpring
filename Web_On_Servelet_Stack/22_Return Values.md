<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/return-types.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p>다음 표에서는 지원되는 컨트롤러 메서드 반환 값을 설명합니다. 모든 반환 값에 대해 반응형 유형이 지원됩니다.</p>
<table class="tableblock frame-all grid-all stripes-odd stretch">




<thead>
<tr>
<th class="tableblock halign-left valign-top">컨트롤러 메소드 반환 값</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ResponseBody</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">반환 값이 <code>HttpMessageConverter</code> 구현을 통해 변환되고 응답으로 작성됩니다. <a href="responsebody.html" class="xref page"><code>@ResponseBody</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpEntity&lt;B&gt;</code>, <code>ResponseEntity&lt;B&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">전체 응답(포함된 HTTP 헤더 및 본문)을 지정하는 반환 값이 <code>HttpMessageConverter</code> 구현을 통해 변환되어 응답으로 작성됩니다. <a href="responseentity.html" class="xref page">ResponseEntity</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpHeaders</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">헤더와 본문이 없는 응답을 반환합니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ErrorResponse</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">본문에 세부 내용이 포함된 RFC 7807 오류 응답을 렌더링합니다. <a href="../../mvc-ann-rest-exceptions.html" class="xref page">오류 응답</a> 참조</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ProblemDetail</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">본문에 세부 내용이 포함된 RFC 7807 오류 응답을 렌더링합니다. <a href="../../mvc-ann-rest-exceptions.html" class="xref page">오류 응답</a> 참조</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>String</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ViewResolver</code> 구현을 통해 해결할 뷰 이름 및 암시적 모델과 함께 사용됩니다. 명령 객체 및 <code>@ModelAttribute</code> 메소드를 통해 결정됩니다. 핸들러 메소드는 또한 <code>Model</code> 인수를 선언하여 모델을 프로그래밍 방식으로 보강할 수 있습니다. (<a href="../ann-requestmapping.html#mvc-ann-requestmapping-registration" class="xref page">명시적 등록</a> 참조).</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>View</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>View</code> 인스턴스를 사용하여 암시적 모델과 함께 렌더링합니다. 명령 객체 및 <code>@ModelAttribute</code> 메소드를 통해 결정됩니다. 핸들러 메소드는 또한 <code>Model</code> 인수를 선언하여 모델을 프로그래밍 방식으로 보강할 수 있습니다. (<a href="../ann-requestmapping.html#mvc-ann-requestmapping-registration" class="xref page">명시적 등록</a> 참조).</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>java.util.Map</code>, <code>org.springframework.ui.Model</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">암시적 모델에 추가할 속성으로, 뷰 이름은 <code>RequestToViewNameTranslator</code>를 통해 암시적으로 결정됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>@ModelAttribute</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">모델에 추가할 속성으로, 뷰 이름은 <code>RequestToViewNameTranslator</code>를 통해 암시적으로 결정됩니다.</p>
<p class="tableblock">  참고: <code>@ModelAttribute</code>는 선택 사항입니다. 이 표의 맨 아래의 "기타 반환 값"을 참조하십시오.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ModelAndView</code> 객체</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">사용할 뷰 및 모델 속성 및 필요한 경우 응답 상태입니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>void</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">void 반환 유형(또는 <code>null</code> 반환 값)의 메소드는 <code>ServletResponse</code>, <code>OutputStream</code> 인수 또는 <code>@ResponseStatus</code> 주석이 있으면 응답을 완전히 처리한 것으로 간주
</p><p>됩니다. 컨트롤러가 긍정적인 <code>ETag</code> 또는 <code>lastModified</code> 타임스탬프 확인을 수행한 경우도 마찬가지입니다. (<a href="../../mvc-caching.html#mvc-caching-etag-lastmodified" class="xref page">컨트롤러</a> 참조).</p><p></p>
<p class="tableblock">  위의 조건 중 하나도 해당되지 않으면 void 반환 유형은 REST 컨트롤러의 "응답 본문 없음"이나 HTML 컨트롤러의 기본 뷰 이름 선택을 나타낼 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>DeferredResult&lt;V&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">임의의 스레드에서 <code>DeferredResult</code>를 통해 이전된 반환 값을 비동기적으로 생성합니다. 예를 들어 어떤 이벤트 또는 콜백의 결과로 생성됩니다. <a href="../../mvc-ann-async.html" class="xref page">비동기 요청</a> 및 <a href="../../mvc-ann-async.html#mvc-ann-async-deferredresult" class="xref page"><code>DeferredResult</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>Callable&lt;V&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">Spring MVC 관리 스레드에서 <code>Callable</code>을 통해 위의 반환 값을 비동기적으로 생성합니다. <a href="../../mvc-ann-async.html" class="xref page">비동기 요청</a> 및 <a href="../../mvc-ann-async.html#mvc-ann-async-callable" class="xref page"><code>Callable</code></a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ListenableFuture&lt;V&gt;</code>,
  <code>java.util.concurrent.CompletionStage&lt;V&gt;</code>,
  <code>java.util.concurrent.CompletableFuture&lt;V&gt;</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">기본 서비스가 해당 값을 반환하는 경우와 같이 편리성을 위해 <code>DeferredResult</code> 대체로 사용될 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ResponseBodyEmitter</code>, <code>SseEmitter</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>HttpMessageConverter</code> 구현을 통해 응답으로 작성될 객체 스트림을 비동기적으로 전송합니다. <code>ResponseEntity</code>의 본문으로도 지원됩니다. <a href="../../mvc-ann-async.html" class="xref page">비동기 요청</a> 및 <a href="../../mvc-ann-async.html#mvc-ann-async-http-streaming" class="xref page">HTTP 스트리밍</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>StreamingResponseBody</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">응답 <code>OutputStream</code>에 비동기적으로 작성합니다. <code>ResponseEntity</code>의 본문으로도 지원됩니다. <a href="../../mvc-ann-async.html" class="xref page">비동기 요청</a> 및 <a href="../../mvc-ann-async.html#mvc-ann-async-http-streaming" class="xref page">HTTP 스트리밍</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">리액터 및 다른 반응형 유형 (<code>ReactiveAdapterRegistry</code>를 통해 등록)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">단일 값 유형인 경우, 예를 들어 <code>Mono</code>는 <code>DeferredResult</code>를 반환하는 것과 유사합니다. 다중 값 유형인 경우, 예를 들어 <code>Flux</code>는 요청된 미디어 유형에 따라 스트림으로 처리되거나, "text/event-stream", "application/json+stream" 또는 기타로 수집되어 단일 값으로 렌더링됩니다. <a href="../../mvc-ann-async.html" class="xref page">비동기 요청</a> 및 <a href="../../mvc-ann-async.html#mvc-ann-async-reactive-types" class="xref page">반응형 유형</a> 참조.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">기타 반환 값</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">기타 모든 방법으로 해결되지 않은 반환 값은 모델 속성으로 처리됩니다. 그러나 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-">BeanUtils#isSimpleProperty</a>에 의해 간단한 유형으로 결정되는 경우에는 그렇지 않습니다.</p></td>
</tr>
</tbody>
</table>