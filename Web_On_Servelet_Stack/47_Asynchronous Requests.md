<p>Spring MVC는 Servlet 비동기 요청 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-processing">처리</a>와 광범위하게 통합되어 있습니다.</p>
<ul>
<li>
<p>컨트롤러 메서드의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-deferredresult"><code>DeferredResult</code></a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-callable"><code>Callable</code></a> 반환 값은 단일 비동기 반환 값에 대한 기본 지원을 제공합니다.</p>
</li>
<li>
<p>컨트롤러는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-sse">SSE</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-output-stream">원시 데이터</a>를 포함한 여러 값을 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-http-streaming">스트리밍</a>할 수 있습니다.</p>
</li>
<li>
<p>컨트롤러는 반응 클라이언트를 사용하고 응답 처리를 위해 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응 type</a>을 반환할 수 있습니다.</p>
</li>
</ul>
<p>이것이 Spring WebFlux와 어떻게 다른지에 대한 개요는 아래의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-vs-webflux">WebFlux와 비교한 Async Spring MVC 섹션</a>을 참조하세요.</p>
<h1 id="deferredresult"><code>DeferredResult</code></h1>
<p>서블릿 컨테이너에서 비동기 요청 처리 기능이 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-configuration">활성화되면</a> 컨트롤러 메서드는 다음 예제와 같이 지원되는 컨트롤러 메서드 반환 값을 <code>DeferredResult</code>로 래핑할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/quotes"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@ResponseBody</span>
<span class="token keyword">public</span> <span class="token class-name">DeferredResult</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">quotes</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">DeferredResult</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> deferredResult <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DeferredResult</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// Save the deferredResult somewhere..</span>
	<span class="token keyword">return</span> deferredResult<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token comment">// From some other thread...</span>
deferredResult<span class="token punctuation">.</span><span class="token function">setResult</span><span class="token punctuation">(</span>result<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>컨트롤러는 예를 들어 외부 이벤트(JMS 메시지), 예약된 작업 또는 기타 이벤트에 대한 응답으로 다른 스레드에서 비동기적으로 반환 값을 생성할 수 있습니다.</p>
<h1 id="callable"><code>Callable</code></h1>
<p>컨트롤러는 다음 예제와 같이 <code>java.util.concurrent.Callable</code>을 사용하여 지원되는 반환 값을 래핑할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span>
<span class="token keyword">public</span> <span class="token class-name">Callable</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">processUpload</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">MultipartFile</span> file<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token string">"someView"</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>그러면 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-configuration-spring-mvc">구성된</a> <code>AsyncTaskExecutor</code>를 통해 지정된 작업을 실행하여 반환 값을 얻을 수 있습니다.</p>
<h1 id="processing">Processing</h1>
<p>다음은 서블릿 비동기 요청 처리에 대한 매우 간결한 개요입니다.</p>
<ul>
<li>
<p><code>ServletRequest</code>는 <code>request.startAsync()</code>를 호출하여 비동기 모드로 전환할 수 있습니다. 이렇게 하면 주요 효과는 서블릿(및 모든 필터)이 종료될 수 있지만 나중에 처리가 완료될 수 있도록 응답이 열린 상태로 유지된다는 것입니다.</p>
</li>
<li>
<p><code>request.startAsync()</code>에 대한 호출은 비동기 처리를 추가로 제어하는 데 사용할 수 있는 <code>AsyncContext</code>를 반환합니다. 예를 들어, 애플리케이션이 서블릿 컨테이너 스레드에서 요청 처리를 재개할 수 있다는 점을 제외하면 서블릿 API의 전달과 유사한 <code>dispatch</code> 메소드를 제공합니다.</p>
</li>
<li>
<p><code>ServletRequest</code>는 초기 요청 처리, 비동기 디스패치, 전달 및 기타 디스패처 유형을 구별하는 데 사용할 수 있는 현재 <code>DispatcherType</code>에 대한 액세스를 제공합니다.</p>
</li>
</ul>
<p><code>DeferredResult</code> 처리는 다음과 같이 작동합니다.</p>
<ul>
<li>
<p>컨트롤러는 <code>DeferredResult</code>를 반환하고 이를 액세스할 수 있는 일부 메모리 내 대기열이나 목록에 저장합니다.</p>
</li>
<li>
<p>Spring MVC는 <code>request.startAsync()</code>를 호출합니다.</p>
</li>
<li>
<p>그 사이에 <code>DispatcherServlet</code>과 구성된 모든 필터는 요청 처리 스레드를 종료하지만 응답은 열린 상태로 유지됩니다.</p>
</li>
<li>
<p>애플리케이션은 일부 스레드에서 <code>DeferredResult</code>를 설정하고 Spring MVC는 요청을 다시 서블릿 컨테이너로 전달합니다.</p>
</li>
<li>
<p><code>DispatcherServlet</code>이 다시 호출되고 비동기적으로 생성된 반환 값으로 처리가 재개됩니다.</p>
</li>
</ul>
<p><code>Callable</code> 처리는 다음과 같이 작동합니다.</p>
<ul>
<li>
<p>컨트롤러는 <code>Callable</code>을 반환합니다.</p>
</li>
<li>
<p>Spring MVC는 <code>request.startAsync()</code>를 호출하고 별도의 스레드에서 처리하기 위해 <code>Callable</code>을 <code>AsyncTaskExecutor</code>에 제출합니다.</p>
</li>
<li>
<p>그 사이에 <code>DispatcherServlet</code>과 모든 필터는 서블릿 컨테이너 스레드를 종료하지만 응답은 열린 상태로 유지됩니다.</p>
</li>
<li>
<p>결국 <code>Callable</code>은 결과를 생성하고 Spring MVC는 처리를 완료하기 위해 요청을 Servlet 컨테이너로 다시 전달합니다.</p>
</li>
<li>
<p><code>DispatcherServlet</code>이 다시 호출되고 <code>Callable</code>에서 비동기적으로 생성된 반환 값으로 처리가 재개됩니다.</p>
</li>
</ul>
<p>추가 배경 및 컨텍스트를 보려면 Spring MVC 3.2에서 비동기 요청 처리 지원을 소개한 <a href="https://spring.io/blog/2012/05/07/spring-mvc-3-2-preview-introducing-servlet-3-async-support">블로그 게시물</a>을 읽어보세요.</p>
<h2 id="exception-handling">Exception Handling</h2>
<p><code>DeferredResult</code>를 사용할 때 예외를 포함하여 <code>setResult</code> 또는 <code>setErrorResult</code>를 호출할지 여부를 선택할 수 있습니다. 두 경우 모두 Spring MVC는 처리를 완료하기 위해 요청을 서블릿 컨테이너로 다시 전달합니다. 그런 다음 컨트롤러 메서드가 지정된 값을 반환한 것처럼 처리되거나 지정된 예외를 생성한 것처럼 처리됩니다. 그런 다음 예외는 일반적인 예외 처리 메커니즘(예: <code>@ExceptionHandler</code> 메서드 호출)을 거칩니다.</p>
<p><code>Callable</code>을 사용하면 유사한 처리 논리가 발생합니다. 주요 차이점은 결과가 <code>Callable</code>에서 반환되거나 예외가 발생한다는 점입니다.</p>
<h2 id="interception">Interception</h2>
<p><code>HandlerInterceptor</code> 인스턴스는 비동기 처리를 시작하는 초기 요청에서 <code>afterConcurrentHandlingStarted</code> 콜백을 수신하기 위해 <code>AsyncHandlerInterceptor</code> 유형일 수 있습니다(<code>postHandle</code> 및 <code>afterCompletion</code> 대신).</p>
<p><code>HandlerInterceptor</code> 구현은 <code>CallableProcessingInterceptor</code> 또는 <code>DeferredResultProcessingInterceptor</code>를 등록하여 비동기 요청의 수명 주기와 더 깊이 통합할 수도 있습니다(예: 시간 초과 이벤트 처리). 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/AsyncHandlerInterceptor.html"><code>AsyncHandlerInterceptor</code></a>를 참조하세요.</p>
<p><code>DeferredResult</code>는 <code>onTimeout(Runnable)</code> 및 <code>onCompletion(Runnable)</code> 콜백을 제공합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/context/request/async/DeferredResult.html"><code>DeferredResult</code>의 javadoc</a>을 참조하세요. 제한 시간 및 완료 콜백에 대한 추가 메서드를 노출하는 <code>WebAsyncTask</code>를 <code>Callable</code>로 대체할 수 있습니다.</p>
<h2 id="async-spring-mvc-compared-to-webflux">Async Spring MVC compared to WebFlux</h2>
<p>Servlet API는 원래 필터-서블릿 체인을 통해 단일 패스를 만들기 위해 구축되었습니다. 비동기식 요청 처리를 통해 애플리케이션은 필터-서블릿 체인을 종료하지만 추가 처리를 위해 응답을 열어 둡니다. Spring MVC 비동기 지원은 해당 메커니즘을 기반으로 구축되었습니다. 컨트롤러가 <code>DeferredResult</code>를 반환하면 필터-서블릿 체인이 종료되고 서블릿 컨테이너 스레드가 해제됩니다. 나중에 <code>DeferredResult</code>가 설정되면 <code>ASYNC</code> 디스패치(동일한 URL로)가 이루어지며, 그 동안 컨트롤러는 다시 매핑되지만 이를 호출하는 대신 <code>DeferredResult</code> 값을 사용하여(컨트롤러가 이를 반환한 것처럼) 처리를 재개합니다. .</p>
<p>이와 대조적으로 Spring WebFlux는 Servlet API를 기반으로 구축되지도 않았고 비동기식으로 설계되었기 때문에 그러한 비동기 요청 처리 기능도 필요하지 않습니다. 비동기 처리는 모든 프레임워크 계약에 내장되어 있으며 요청 처리의 모든 단계를 통해 본질적으로 지원됩니다.</p>
<p>프로그래밍 모델 관점에서 Spring MVC와 Spring WebFlux는 모두 컨트롤러 메서드의 반환 값으로 비동기 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응형 type</a>을 지원합니다. Spring MVC는 반응성 역압을 포함한 스트리밍도 지원합니다. 그러나 비차단 I/O에 의존하고 각 쓰기에 추가 스레드가 필요하지 않은 WebFlux와는 달리 응답에 대한 개별 쓰기는 차단 상태로 유지되며 별도의 스레드에서 수행됩니다.</p>
<p>또 다른 근본적인 차이점은 Spring MVC가 컨트롤러 메서드 인수(예: <code>@RequestBody</code>, <code>@RequestPart</code> 등)에서 비동기식 또는 반응성 유형을 지원하지 않으며 모델 속성으로 비동기식 및 반응성 유형에 대한 명시적인 지원도 없다는 점입니다. Spring WebFlux는 이 모든 것을 지원합니다.</p>
<p>마지막으로 구성 관점에서 비동기 요청 처리 기능은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-configuration">서블릿 컨테이너 수준에서 활성화되어야 합니다.</a></p>
<h1 id="http-streaming">HTTP Streaming</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-codecs-streaming">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>단일 비동기 반환 값에 대해 <code>DeferredResult</code> 및 <code>Callable</code>을 사용할 수 있습니다. 여러 비동기 값을 생성하고 이를 응답에 기록하려면 어떻게 해야 합니까? 이 섹션에서는 이를 수행하는 방법을 설명합니다.</p>
<h2 id="objects">Objects</h2>
<p><code>ResponseBodyEmitter</code> 반환 값을 사용하여 개체 스트림을 생성할 수 있습니다. 여기서 각 개체는 다음 예제와 같이 <a href="https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-message-conversion"><code>HttpMessageConverter</code></a>로 직렬화되고 응답에 기록됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/events"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">ResponseBodyEmitter</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ResponseBodyEmitter</span> emitter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ResponseBodyEmitter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// Save the emitter somewhere..</span>
	<span class="token keyword">return</span> emitter<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token comment">// In some other thread</span>
emitter<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"Hello once"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// and again later on</span>
emitter<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"Hello again"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// and done at some point</span>
emitter<span class="token punctuation">.</span><span class="token function">complete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>ResponseBodyEmitter</code>를 <code>ResponseEntity</code>의 본문으로 사용하여 응답의 상태와 헤더를 사용자 정의할 수도 있습니다.</p>
<p><code>emitter</code>가 <code>IOException</code>을 발생시키는 경우(예: 원격 클라이언트가 사라진 경우) 애플리케이션은 연결 정리를 담당하지 않으며 <code>emitter.complete</code> 또는 <code>emitter.completeWithError</code>를 호출해서는 안 됩니다. 대신 서블릿 컨테이너는 자동으로 <code>AsyncListener</code> 오류 알림을 시작하는데, 여기서 Spring MVC는 <code>completeWithError</code> 호출을 생성합니다. 이 호출은 차례로 애플리케이션에 대한 최종 <code>ASYNC</code> 디스패치를 수행하며, 그 동안 Spring MVC는 구성된 예외 해결 프로그램을 호출하고 요청을 완료합니다.</p>
<h2 id="sse">SSE</h2>
<p><code>SseEmitter</code>(<code>ResponseBodyEmitter</code>의 하위 클래스)는 <a href="https://www.w3.org/TR/eventsource/">서버에서 전송된 이벤트</a>가 W3C SSE 사양에 따라 형식화되는 서버 전송 이벤트에 대한 지원을 제공합니다. 컨트롤러에서 SSE 스트림을 생성하려면 다음 예제와 같이 <code>SseEmitter</code>를 반환합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span>path<span class="token operator">=</span><span class="token string">"/events"</span><span class="token punctuation">,</span> produces<span class="token operator">=</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>TEXT_EVENT_STREAM_VALUE<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">SseEmitter</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">SseEmitter</span> emitter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SseEmitter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// Save the emitter somewhere..</span>
	<span class="token keyword">return</span> emitter<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token comment">// In some other thread</span>
emitter<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"Hello once"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// and again later on</span>
emitter<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"Hello again"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// and done at some point</span>
emitter<span class="token punctuation">.</span><span class="token function">complete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>SSE는 브라우저로 스트리밍하기 위한 기본 옵션이지만 Internet Explorer는 서버에서 보낸 이벤트를 지원하지 않습니다. 다양한 브라우저를 대상으로 하는 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/fallback.html">SockJS fallback</a> 전송(SSE 포함)과 함께 Spring의 <a href="https://docs.spring.io/spring-framework/reference/web/websocket.html">WebSocket 메시징</a>을 사용하는 것을 고려해보세요.</p>
<p>예외 처리에 대한 참고 사항은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-objects">이전 섹션</a>을 참조하세요.</p>
<h2 id="raw-data">Raw Data</h2>
<p>때로는 메시지 변환을 우회하고 응답 <code>OutputStream</code>으로 직접 스트리밍하는 것이 유용합니다(예: 파일 다운로드의 경우). 다음 예제와 같이 <code>StreamingResponseBody</code> 반환 값 유형을 사용하여 이를 수행할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/download"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">StreamingResponseBody</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">StreamingResponseBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token annotation punctuation">@Override</span>
		<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">writeTo</span><span class="token punctuation">(</span><span class="token class-name">OutputStream</span> outputStream<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">IOException</span> <span class="token punctuation">{</span>
			<span class="token comment">// write...</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>StreamingResponseBody</code>를 <code>ResponseEntity</code>의 본문으로 사용하여 응답의 상태와 헤더를 사용자 지정할 수 있습니다.</p>
<h1 id="reactive-types">Reactive Types</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-codecs-streaming">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 컨트롤러에서 반응형 클라이언트 라이브러리의 사용을 지원합니다(WebFlux 섹션의 <a href="https://docs.spring.io/spring-framework/reference/web-reactive.html#webflux-reactive-libraries">반응형 라이브러리</a>도 읽어보세요). 여기에는 <code>spring-webflux</code>의 <code>WebClient</code>와 Spring Data 반응형 데이터 저장소와 같은 기타 항목이 포함됩니다. 이러한 시나리오에서는 컨트롤러 메서드에서 반응형 형식을 반환할 수 있는 것이 편리합니다.</p>
<p>반응형 반환 값은 다음과 같이 처리됩니다.</p>
<ul>
<li>
<p><code>DeferredResult</code>를 사용하는 것과 비슷하게 단일 값 Promise가 적용됩니다. 예로는 <code>Mono</code>(Reactor) 또는 <code>Single</code>(RxJava)이 있습니다.</p>
</li>
<li>
<p>스트리밍 미디어 유형(예: <code>application/x-ndjson</code> 또는 <code>text/event-stream</code>)이 있는 다중 값 스트림은 <code>ResponseBodyEmitter</code> 또는 <code>SseEmitter</code>를 사용하는 것과 유사하게 조정됩니다. 예로는 <code>Flux</code>(Reactor) 또는 <code>Observable</code>(RxJava)이 있습니다. 애플리케이션은 <code>Flux&lt;ServerSentEvent&gt;</code> 또는 <code>Observable&lt;ServerSentEvent&gt;</code>를 반환할 수도 있습니다.</p>
</li>
<li>
<p><code>DeferredResult&lt;List&lt;?&gt;&gt;</code>를 사용하는 것과 유사하게 다른 미디어 유형(예: <code>application/json</code>)이 포함된 다중 값 스트림이 적용됩니다.</p>
</li>
</ul>
<blockquote>
<p><strong>[Tip]</strong><br>
Spring MVC는 <code>spring-core</code>의 <code>ReactiveAdapterRegistry</code>를 통해 Reactor와 RxJava를 지원하므로 여러 반응성 라이브러리에서 적응할 수 있습니다.</p>
</blockquote>
<p>응답으로 스트리밍하는 경우 반응적 역압력이 지원되지만 응답에 대한 쓰기는 여전히 차단되고 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-configuration-spring-mvc">구성된</a> <code>AsyncTaskExecutor</code>를 통해 별도의 스레드에서 실행되어 <code>WebClient</code>에서 반환된 <code>Flux</code>와 같은 업스트림 소스를 차단하지 않습니다.</p>
<h1 id="context-propagation">Context Propagation</h1>
<p><code>java.lang.ThreadLocal</code>을 통해 컨텍스트를 전파하는 것이 일반적입니다. 이는 동일한 스레드에서 처리할 때 투명하게 작동하지만 여러 스레드에서 비동기 처리를 수행하려면 추가 작업이 필요합니다. Micrometer <a href="https://github.com/micrometer-metrics/context-propagation#context-propagation-library">컨텍스트 전파</a> 라이브러리는 스레드 전체와 <code>ThreadLocal</code> 값, Reactor <a href="https://projectreactor.io/docs/core/release/reference/#context">컨텍스트</a>, GraphQL Java <a href="https://www.graphql-java.com/documentation/concerns/#context-objects">컨텍스트</a> 등과 같은 컨텍스트 메커니즘 전체에서 컨텍스트 전파를 단순화합니다.</p>
<p>클래스 경로에 Micrometer Context Propagation이 있는 경우 컨트롤러 메서드가 <code>Flux</code> 또는 <code>Mono</code>와 같은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응형 type</a>을 반환하면 등록된 <code>io.micrometer.ThreadLocalAccessor</code>가 있는 모든 <code>ThreadLocal</code> 값이 Reactor <code>Context</code>에 키-값으로 기록됩니다. <code>ThreadLocalAccessor</code>에서 할당한 키를 사용하여 쌍을 이룹니다.</p>
<p>다른 비동기 처리 시나리오의 경우 컨텍스트 전파 라이브러리를 직접 사용할 수 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token comment">// Capture ThreadLocal values from the main thread ...</span>
<span class="token class-name">ContextSnapshot</span> snapshot <span class="token operator">=</span> <span class="token class-name">ContextSnapshot</span><span class="token punctuation">.</span><span class="token function">captureAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// On a different thread: restore ThreadLocal values</span>
<span class="token keyword">try</span> <span class="token punctuation">(</span><span class="token class-name">ContextSnapshot</span><span class="token punctuation">.</span><span class="token class-name">Scope</span> scope <span class="token operator">=</span> snapshot<span class="token punctuation">.</span><span class="token function">setThreadLocals</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>자세한 내용은 Micrometer Context Propagation 라이브러리의 <a href="https://micrometer.io/docs/contextPropagation">설명서</a>를 참조하세요.</p>
<h1 id="disconnects">Disconnects</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-codecs-streaming">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Servlet API는 원격 클라이언트가 사라질 때 알림을 제공하지 않습니다. 따라서 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-sse">SseEmitter</a> 또는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">reactive types</a>을 통해 응답으로 스트리밍하는 동안 클라이언트 연결이 끊어지면 쓰기가 실패하므로 주기적으로 데이터를 보내는 것이 중요합니다. 전송은 빈(주석 전용) SSE 이벤트 또는 상대방이 하트비트로 해석하고 무시해야 하는 기타 데이터의 형태를 취할 수 있습니다.</p>
<p>또는 하트비트 메커니즘이 내장된 웹 메시징 솔루션(예: <a href="https://docs.spring.io/spring-framework/reference/web/websocket/stomp.html">WebSocket을 통한 STOMP</a> 또는 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/fallback.html">SockJS</a>를 통한 WebSocket) 사용을 고려해보세요.</p>
<h1 id="configuration">Configuration</h1>
<p>비동기 요청 처리 기능은 서블릿 컨테이너 수준에서 활성화되어야 합니다. MVC 구성은 비동기 요청에 대한 여러 옵션도 제공합니다.</p>
<h2 id="servlet-container">Servlet Container</h2>
<p>필터 및 서블릿 선언에는 비동기 요청 처리를 활성화하려면 <code>true</code>로 설정해야 하는 <code>asyncSupported</code> 플래그가 있습니다. 또한 <code>ASYNC</code> <code>jakarta.servlet.DispatchType</code>을 처리하려면 필터 매핑을 선언해야 합니다.</p>
<p>Java 구성에서 <code>AbstractAnnotationConfigDispatcherServletInitializer</code>를 사용하여 서블릿 컨테이너를 초기화하면 이 작업이 자동으로 수행됩니다.</p>
<p><code>web.xml</code> 구성에서 <code>DispatcherServlet</code> 및 필터 선언에 <code>&lt;async-supported&gt;true&lt;/async-supported&gt;</code>를 추가하고 필터 매핑에 <code>&lt;dispatcher&gt;ASYNC&lt;/dispatcher&gt;</code>를 추가할 수 있습니다.</p>
<h2 id="spring-mvc">Spring MVC</h2>
<p>MVC 구성은 비동기 요청 처리를 위해 다음 옵션을 공개합니다.</p>
<ul>
<li>
<p>Java 구성: <code>WebMvcConfigurer</code>에서<code>configureAsyncSupport</code> 콜백을 사용합니다.</p>
</li>
<li>
<p>XML 네임스페이스: <code>&lt;mvc:annotation-driven&gt;</code> 아래의 <code>&lt;async-support&gt;</code> 요소를 사용합니다.</p>
</li>
</ul>
<p>다음을 구성할 수 있습니다.</p>
<ul>
<li>
<p>설정되지 않은 경우 기본 서블릿 컨테이너에 따라 달라지는 비동기 요청의 기본 시간 초과 값입니다.</p>
</li>
<li>
<p><code>AsyncTaskExecutor</code>는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응형 type</a>으로 스트리밍할 때 쓰기를 차단하고 컨트롤러 메서드에서 반환된 <code>Callable</code> 인스턴스를 실행하는 데 사용됩니다. 기본적으로 사용되는 것은 부하가 걸리는 생산에는 적합하지 않습니다.</p>
</li>
<li>
<p><code>DeferredResultProcessingInterceptor</code> 구현 및 <code>CallableProcessingInterceptor</code> 구현.</p>
</li>
</ul>
<p><code>DeferredResult</code>, <code>ResponseBodyEmitter</code> 및 <code>SseEmitter</code>에 기본 시간 초과 값을 설정할 수도 있습니다. <code>Callable</code>의 경우 <code>WebAsyncTask</code>를 사용하여 시간 초과 값을 제공할 수 있습니다.</p>