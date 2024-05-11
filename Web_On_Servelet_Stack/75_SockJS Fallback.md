<p>public 인터넷을 통해 통제할 수 없는 제한적인 프록시는 <code>UPgrade</code> 헤더를 전달하도록 구성되지 않았거나 유휴 상태로 보이는 장기 연결을 닫기 때문에 WebSocket 상호 작용을 방해할 수 있습니다.</p>
<p>이 문제에 대한 해결책은 WebSocket 에뮬레이션입니다. 즉, WebSocket을 먼저 사용하려고 시도한 다음 WebSocket 상호 작용을 에뮬레이트하고 동일한 애플리케이션 수준 API를 노출하는 HTTP 기반 기술을 사용하는 것입니다.</p>
<p>Servlet 스택에서 Spring Framework는 SockJS 프로토콜에 대한 서버(및 클라이언트) 지원을 모두 제공합니다.</p>
<h1 id="overview">Overview</h1>
<p>SockJS의 목표는 애플리케이션이 WebSocket API를 사용하도록 허용하지만 애플리케이션 코드를 변경할 필요 없이 런타임에 필요할 때 WebSocket이 아닌 대안으로 대체하는 것입니다.</p>
<p>SockJS는 다음으로 구성됩니다.</p>
<ul>
<li>
<p>실행 가능한 <a href="https://sockjs.github.io/sockjs-protocol/sockjs-protocol-0.3.3.html">설명 테스트</a> 형식으로 정의된 <a href="https://github.com/sockjs/sockjs-protocol">SockJS 프로토콜</a>입니다.</p>
</li>
<li>
<p><a href="https://github.com/sockjs/sockjs-client">SockJS JavaScript 클라이언트</a> — 브라우저에서 사용하기 위한 클라이언트 라이브러리입니다.</p>
</li>
<li>
<p>Spring Framework <code>spring-websocket</code> 모듈의 구현을 포함한 SockJS 서버 구현.</p>
</li>
<li>
<p><code>spring-websocket</code> 모듈의 SockJS Java 클라이언트(버전 4.1부터).</p>
</li>
</ul>
<p>SockJS는 브라우저에서 사용하도록 설계되었습니다. 다양한 기술을 사용하여 다양한 브라우저 버전을 지원합니다. SockJS 전송 유형 및 브라우저의 전체 목록은 <a href="https://github.com/sockjs/sockjs-client">SockJS 클라이언트</a> 페이지를 참조하세요. 전송은 WebSocket, HTTP 스트리밍 및 HTTP 긴 폴링의 세 가지 일반 범주로 분류됩니다. 이러한 카테고리에 대한 개요는 <a href="https://spring.io/blog/2012/05/08/spring-mvc-3-2-preview-techniques-for-real-time-updates/">이 블로그 게시물</a>을 참조하세요.</p>
<p>SockJS 클라이언트는 서버에서 기본 정보를 얻기 위해 <code>GET /info</code>를 보내는 것으로 시작합니다. 그 후에는 어떤 전송 수단을 사용할지 결정해야 합니다. 가능하다면 WebSocket이 사용됩니다. 그렇지 않은 경우 대부분의 브라우저에는 하나 이상의 HTTP 스트리밍 옵션이 있습니다. 그렇지 않은 경우 HTTP(긴) 폴링이 사용됩니다.</p>
<p>모든 전송 요청에는 다음과 같은 URL 구조가 있습니다.</p>
<pre><code class="language-null">https://host:port/myApp/myEndpoint/{server-id}/{session-id}/{transport}</code></pre>
<p>어디:</p>
<ul>
<li>
<p><code>{server-id}</code>는 클러스터에서 요청을 라우팅하는 데 유용하지만 그 외에는 사용되지 않습니다.</p>
</li>
<li>
<p><code>{session-id}</code>는 SockJS 세션에 속하는 HTTP 요청을 연관시킵니다.</p>
</li>
<li>
<p><code>{transport}</code>는 전송 유형(예: <code>websocket</code>, <code>xhr-streaming</code> 등)을 나타냅니다.</p>
</li>
</ul>
<p>WebSocket 전송에서는 WebSocket 핸드셰이크를 수행하기 위해 단일 HTTP 요청만 필요합니다. 이후의 모든 메시지는 해당 소켓에서 교환됩니다.</p>
<p>HTTP 전송에는 더 많은 요청이 필요합니다. 예를 들어 Ajax/XHR 스트리밍은 서버-클라이언트 메시지에 대한 하나의 장기 실행 요청과 클라이언트-서버 메시지에 대한 추가 HTTP POST 요청에 의존합니다. 긴 폴링은 각 서버에서 클라이언트로 전송한 후 현재 요청을 종료한다는 점을 제외하면 비슷합니다.</p>
<p>SockJS는 최소한의 메시지 프레이밍을 추가합니다. 예를 들어, 서버는 처음에 문자 <code>o</code>(“open” 프레임)를 보내고, 메시지는 <code>a["message1","message2"]</code>(JSON 인코딩 배열)로 전송되며, 메시지가 없으면 문자 <code>h</code>("heartbeat" 프레임)로 전송됩니다. 25초(기본값) 동안 흐르고 문자 <code>c</code>("닫기" 프레임)는 세션을 닫습니다.</p>
<p>자세히 알아보려면 브라우저에서 예제를 실행하고 HTTP 요청을 살펴보세요. SockJS 클라이언트를 사용하면 전송 목록을 수정할 수 있으므로 각 전송을 한 번에 하나씩 볼 수 있습니다. SockJS 클라이언트는 브라우저 콘솔에서 유용한 메시지를 활성화하는 디버그 플래그도 제공합니다. 서버 측에서는 <code>org.springframework.web.socket</code>에 대한 <code>TRACE</code> 로깅을 활성화할 수 있습니다. 더 자세한 내용은 SockJS 프로토콜 <a href="https://sockjs.github.io/sockjs-protocol/sockjs-protocol-0.3.3.html">설명 테스트</a>를 참조하세요.</p>
<h1 id="enabling-sock-js">Enabling Sock JS</h1>
<p>다음 예제와 같이 Java 구성을 통해 SockJS를 활성화할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocket</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerWebSocketHandlers</span><span class="token punctuation">(</span><span class="token class-name">WebSocketHandlerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addHandler</span><span class="token punctuation">(</span><span class="token function">myHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"/myHandler"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withSockJS</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">WebSocketHandler</span> <span class="token function">myHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MyHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 앞의 예와 동일한 XML 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>websocket</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/websocket<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/websocket
		https://www.springframework.org/schema/websocket/spring-websocket.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>handlers</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>mapping</span> <span class="token attr-name">path</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">handler</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>sockjs</span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">websocket:</span>handlers</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.MyHandler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예제는 Spring MVC 애플리케이션에서 사용하기 위한 것이며 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet.html"><code>DispatcherServlet</code></a> 구성에 포함되어야 합니다. 그러나 Spring의 WebSocket 및 SockJS 지원은 Spring MVC에 의존하지 않습니다. <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/socket/sockjs/support/SockJsHttpRequestHandler.html"><code>SockJsHttpRequestHandler</code></a>의 도움으로 다른 HTTP 서비스 환경에 통합하는 것은 비교적 간단합니다.</p>
<p>브라우저 측에서 애플리케이션은 <a href="https://github.com/sockjs/sockjs-client"><code>sockjs-client</code></a>(버전 1.0.x)를 사용할 수 있습니다. W3C WebSocket API를 에뮬레이션하고 서버와 통신하여 실행되는 브라우저에 따라 최상의 전송 옵션을 선택합니다. <a href="https://github.com/sockjs/sockjs-client">sockjs-client</a> 페이지와 브라우저에서 지원하는 전송 유형 목록을 참조하세요. 클라이언트는 또한 포함할 전송을 지정하는 등 여러 구성 옵션을 제공합니다.</p>
<h1 id="ie-8-and-9">IE 8 and 9</h1>
<p>Internet Explorer 8과 9는 계속 사용됩니다. 이것이 SockJS를 사용하는 주요 이유입니다. 이 섹션에서는 해당 브라우저에서 실행하는 데 대한 중요한 고려 사항을 다룹니다.</p>
<p>SockJS 클라이언트는 Microsoft의 <a href="https://web.archive.org/web/20160219230343/https://blogs.msdn.com/b/ieinternals/archive/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds.aspx"><code>XDomainRequest</code></a>를 사용하여 IE 8 및 9에서 Ajax/XHR 스트리밍을 지원합니다. 이는 도메인 전체에서 작동하지만 쿠키 전송을 지원하지 않습니다. 쿠키는 Java 애플리케이션에 필수적인 경우가 많습니다. 그러나 SockJS 클라이언트는 Java 서버뿐만 아니라 다양한 서버 유형과 함께 사용할 수 있으므로 쿠키가 중요한지 여부를 알아야 합니다. 그렇다면 SockJS 클라이언트는 스트리밍을 위해 Ajax/XHR을 선호합니다. 그렇지 않으면 iframe 기반 기술을 사용합니다.</p>
<p>SockJS 클라이언트의 첫 번째 <code>/info</code> 요청은 클라이언트의 전송 선택에 영향을 미칠 수 있는 정보에 대한 요청입니다. 이러한 세부 정보 중 하나는 서버 애플리케이션이 쿠키에 의존하는지 여부입니다(예: 인증 목적 또는 고정 세션과의 클러스터링). Spring의 SockJS 지원에는 <code>sessionCookieNeeded</code>라는 속성이 포함되어 있습니다. 대부분의 Java 애플리케이션이 <code>JSESSIONID</code> 쿠키에 의존하기 때문에 기본적으로 활성화되어 있습니다. 애플리케이션에 필요하지 않은 경우 이 옵션을 끌 수 있으며 SockJS 클라이언트는 IE 8 및 9에서 <code>xdr-streaming</code>을 선택해야 합니다.</p>
<p>iframe 기반 전송을 사용하는 경우 HTTP 응답 헤더 <code>X-Frame-Options</code>를 <code>DENY</code>, <code>SAMEORIGIN</code> 또는 <code>ALLOW-FROM &lt;origin&gt;</code>으로 설정하여 특정 페이지에서 IFrame 사용을 차단하도록 브라우저에 지시할 수 있다는 점에 유의하세요. 이는 <a href="https://www.owasp.org/index.php/Clickjacking">클릭재킹</a>을 방지하는 데 사용됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Security 3.2+는 모든 응답에서 <code>X-Frame-Options</code> 설정을 지원합니다. 기본적으로 Spring Security Java 구성은 이를 <code>DENY</code>로 설정합니다. 3.2에서는 Spring Security XML 네임스페이스가 기본적으로 해당 헤더를 설정하지 않지만 그렇게 하도록 구성할 수 있습니다. 앞으로는 기본적으로 설정될 수도 있습니다.</p>
<p><code>X-Frame-Options</code> 헤더 설정을 구성하는 방법에 대한 자세한 내용은 Spring Security 설명서의 <a href="https://docs.spring.io/spring-security/reference/features/exploits/headers.html#headers-default">기본 보안 헤더</a>를 참조하세요. 추가 배경 정보는 <a href="https://github.com/spring-projects/spring-security/issues/2718">gh-2718</a>을 참조하세요.</p>
</blockquote>
<p>애플리케이션이 <code>X-Frame-Options</code> 응답 헤더를 추가하고(필요한 대로!) iframe 기반 전송을 사용하는 경우 헤더 값을 <code>SAMEORIGIN</code> 또는 <code>ALLOW-FROM &lt;origin&gt;</code>으로 설정해야 합니다. Spring SockJS 지원은 iframe에서 로드되기 때문에 SockJS 클라이언트의 위치도 알아야 합니다. 기본적으로 iframe은 CDN 위치에서 SockJS 클라이언트를 다운로드하도록 설정되어 있습니다. 애플리케이션과 동일한 출처의 URL을 사용하도록 이 옵션을 구성하는 것이 좋습니다.</p>
<p>다음 예에서는 Java 구성에서 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java">  <span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocketMessageBroker</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketMessageBrokerConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerStompEndpoints</span><span class="token punctuation">(</span><span class="token class-name">StompEndpointRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addEndpoint</span><span class="token punctuation">(</span><span class="token string">"/portfolio"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withSockJS</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">setClientLibraryUrl</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080/myapp/js/sockjs-client.js"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>
<p>XML 네임스페이스는 <code>&lt;websocket:sockjs&gt;</code> 요소를 통해 유사한 옵션을 제공합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
초기 개발 중에 브라우저가 캐시될 SockJS 요청(예: iframe)을 캐시하지 못하도록 방지하는 SockJS 클라이언트 <code>devel</code> 모드를 활성화하세요. 활성화 방법에 대한 자세한 내용은 <a href="https://github.com/sockjs/sockjs-client">SockJS 클라이언트 페이지</a>를 참조하세요.</p>
</blockquote>
<h1 id="heartbeats">Heartbeats</h1>
<p>SockJS 프로토콜에서는 프록시가 연결이 중단되었다는 결론을 내리지 못하도록 서버가 하트비트 메시지를 보내도록 요구합니다. Spring SockJS 구성에는 빈도를 사용자 정의하는 데 사용할 수 있는 <code>heartbeatTime</code>이라는 속성이 있습니다. 기본적으로 해당 연결에서 다른 메시지가 전송되지 않았다고 가정하면 하트비트는 25초 후에 전송됩니다. 이 25초 값은 공용 인터넷 애플리케이션에 대한 다음 <a href="https://datatracker.ietf.org/doc/html/rfc6202">IETF 권장 사항</a>을 따릅니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
WebSocket 및 SockJS를 통해 STOMP를 사용할 때 STOMP 클라이언트와 서버가 교환할 하트비트를 협상하면 SockJS 하트비트가 비활성화됩니다.</p>
</blockquote>
<p>Spring SockJS 지원을 통해 <code>TaskScheduler</code>를 구성하여 하트비트 작업을 예약할 수도 있습니다. 작업 스케줄러는 사용 가능한 프로세서 수에 따른 기본 설정을 사용하여 스레드 풀로 지원됩니다. 특정 요구 사항에 따라 설정을 사용자 정의하는 것을 고려해야 합니다.</p>
<h1 id="client-disconnects">Client Disconnects</h1>
<p>HTTP 스트리밍 및 HTTP 긴 폴링 SockJS 전송에는 평소보다 오랫동안 열려 있는 연결이 필요합니다. 이러한 기술에 대한 개요는 <a href="https://spring.io/blog/2012/05/08/spring-mvc-3-2-preview-techniques-for-real-time-updates/">이 블로그 게시물</a>을 참조하세요.</p>
<p>서블릿 컨테이너에서 이는 서블릿 컨테이너 스레드 종료, 요청 처리 및 다른 스레드의 응답에 계속 쓰기를 허용하는 Servlet 3 비동기 지원을 통해 수행됩니다.</p>
<p>특정 문제는 Servlet API가 사라진 클라이언트에 대한 알림을 제공하지 않는다는 것입니다. <a href="https://github.com/eclipse-ee4j/servlet-api/issues/44">eclipse-ee4j/servlet-api#44</a>를 참조하세요. 그러나 서블릿 컨테이너는 응답에 쓰려는 후속 시도에서 예외를 발생시킵니다. Spring의 SockJS 서비스는 서버에서 보내는 하트비트(기본적으로 25초마다)를 지원하므로 클라이언트 연결 끊김이 일반적으로 해당 기간(또는 메시지가 더 자주 전송되는 경우 더 일찍) 내에 감지된다는 의미입니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
결과적으로 클라이언트 연결이 끊어져 네트워크 I/O 오류가 발생할 수 있으며 이로 인해 로그가 불필요한 스택 추적으로 채워질 수 있습니다. Spring은 클라이언트 연결 끊김(각 서버에 특정)을 나타내는 네트워크 오류를 식별하고 전용 로그 카테고리 <code>DISCONNECTED_CLIENT_LOG_CATEGORY</code>(<code>AbstractSockJsSession</code>에 정의됨)를 사용하여 최소한의 메시지를 기록하기 위해 최선의 노력을 다합니다. 스택 추적을 확인해야 하는 경우 해당 로그 범주를 TRACE로 설정할 수 있습니다.</p>
</blockquote>
<h1 id="sockjs-and-cors">SockJS and CORS</h1>
<p>원본 간 요청을 허용하는 경우(<a href="https://docs.spring.io/spring-framework/reference/web/websocket/server.html#websocket-server-allowed-origins">허용된 origin</a> 참조) SockJS 프로토콜은 XHR 스트리밍 및 폴링 전송에서 도메인 간 지원을 위해 CORS를 사용합니다. 따라서 응답에서 CORS 헤더가 감지되지 않는 한 CORS 헤더가 자동으로 추가됩니다. 따라서 애플리케이션이 이미 CORS 지원(예: 서블릿 필터를 통해)을 제공하도록 구성된 경우 Spring의 <code>SockJsService</code>는 이 부분을 건너뜁니다.</p>
<p>Spring의 SockJsService에서 <code>supressCors</code> 속성을 설정하여 이러한 CORS 헤더 추가를 비활성화하는 것도 가능합니다.</p>
<p>SockJS에는 다음 헤더와 값이 필요합니다.</p>
<ul>
<li>
<p><code>Access-Control-Allow-Origin</code>: <code>Origin</code> 요청 헤더 값에서 초기화됩니다.</p>
</li>
<li>
<p><code>Access-Control-Allow-Credentials</code>: 항상 <code>true</code>로 설정됩니다.</p>
</li>
<li>
<p><code>Access-Control-Request-Headers</code>: 동등한 요청 헤더의 값에서 초기화됩니다.</p>
</li>
<li>
<p><code>Access-Control-Allow-Methods</code>: 전송이 지원하는 HTTP 메소드입니다(<code>TransportType</code> 열거형 참조).</p>
</li>
<li>
<p><code>Access-Control-Max-Age</code>: 31536000(1년)으로 설정합니다.</p>
</li>
</ul>
<p>정확한 구현을 보려면 <code>AbstractSockJsService</code>의 <code>addCorsHeaders</code>와 소스 코드의 <code>TransportType</code> 열거형을 참조하세요.</p>
<p>또는 CORS 구성이 허용하는 경우 SockJS 끝점 접두사가 있는 URL을 제외하여 Spring의 <code>SockJsService</code>가 이를 처리하도록 하는 것을 고려하세요.</p>
<h1 id="sockjsclient"><code>SockJsClient</code></h1>
<p>Spring은 브라우저를 사용하지 않고 원격 SockJS 끝점에 연결할 수 있는 SockJS Java 클라이언트를 제공합니다. 이는 공용 네트워크를 통해 두 서버 간에 양방향 통신이 필요한 경우(즉, 네트워크 프록시가 WebSocket 프로토콜 사용을 배제할 수 있는 경우) 특히 유용할 수 있습니다. SockJS Java 클라이언트는 테스트 목적(예: 다수의 동시 사용자 시뮬레이션)에도 매우 유용합니다.</p>
<p>SockJS Java 클라이언트는 <code>websocket</code>, <code>xhr-streaming</code> 및 <code>xhr-polling</code> 전송을 지원합니다. 나머지 것들은 브라우저에서만 사용할 수 있습니다.</p>
<p>다음을 사용하여 <code>WebSocketTransport</code>를 구성할 수 있습니다.</p>
<ul>
<li>
<p>JSR-356 런타임의 <code>StandardWebSocketClient</code>.</p>
</li>
<li>
<p>Jetty 9+ 기본 WebSocket API를 사용하는 <code>JettyWebSocketClient</code>.</p>
</li>
<li>
<p>Spring의 <code>WebSocketClient</code> 구현.</p>
</li>
</ul>
<p><code>XhrTransport</code>는 정의상 <code>xhr-streaming</code>과 <code>xhr-polling</code>을 모두 지원합니다. 클라이언트 관점에서 볼 때 서버에 연결하는 데 사용되는 URL 외에는 차이가 없기 때문입니다. 현재 두 가지 구현이 있습니다.</p>
<ul>
<li>
<p><code>RestTemplateXhrTransport</code>는 HTTP 요청에 Spring의 <code>RestTemplate</code>을 사용합니다.</p>
</li>
<li>
<p><code>JettyXhrTransport</code>는 HTTP 요청에 Jetty의 <code>HttpClient</code>를 사용합니다.</p>
</li>
</ul>
<p>다음 예제에서는 SockJS 클라이언트를 생성하고 SockJS 엔드포인트에 연결하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Transport</span><span class="token punctuation">&gt;</span></span> transports <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transports<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">WebSocketTransport</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">StandardWebSocketClient</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
transports<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">RestTemplateXhrTransport</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">SockJsClient</span> sockJsClient <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SockJsClient</span><span class="token punctuation">(</span>transports<span class="token punctuation">)</span><span class="token punctuation">;</span>
sockJsClient<span class="token punctuation">.</span><span class="token function">doHandshake</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyWebSocketHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"ws://example.com:8080/sockjs"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
SockJS는 메시지에 JSON 형식의 배열을 사용합니다. 기본적으로 Jackson 2가 사용되며 클래스 경로에 있어야 합니다. 또는 <code>SockJsMessageCodec</code>의 사용자 정의 구현을 구성하고 이를 <code>SockJsClient</code>에서 구성할 수 있습니다.</p>
</blockquote>
<p><code>SockJsClient</code>를 사용하여 다수의 동시 사용자를 시뮬레이션하려면 충분한 수의 연결 및 스레드를 허용하도록 기본 HTTP 클라이언트(XHR 전송용)를 구성해야 합니다. 다음 예에서는 Jetty를 사용하여 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">HttpClient</span> jettyHttpClient <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HttpClient</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
jettyHttpClient<span class="token punctuation">.</span><span class="token function">setMaxConnectionsPerDestination</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
jettyHttpClient<span class="token punctuation">.</span><span class="token function">setExecutor</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">QueuedThreadPool</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 사용자 정의도 고려해야 하는 서버측 SockJS 관련 속성(자세한 내용은 javadoc 참조)을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">extends</span> <span class="token class-name">WebSocketMessageBrokerConfigurationSupport</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerStompEndpoints</span><span class="token punctuation">(</span><span class="token class-name">StompEndpointRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addEndpoint</span><span class="token punctuation">(</span><span class="token string">"/sockjs"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">withSockJS</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">setStreamBytesLimit</span><span class="token punctuation">(</span><span class="token number">512</span> <span class="token operator">*</span> <span class="token number">1024</span><span class="token punctuation">)</span> <span class="token comment">// (1) </span>
			<span class="token punctuation">.</span><span class="token function">setHttpMessageCacheSize</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span> <span class="token comment">// (2)</span>
			<span class="token punctuation">.</span><span class="token function">setDisconnectDelay</span><span class="token punctuation">(</span><span class="token number">30</span> <span class="token operator">*</span> <span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// (3)</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>streamBytesLimit</code> 속성을 512KB로 설정합니다(기본값은 128KB — 128 <em> 1024).<br>
(2) <code>httpMessageCacheSize</code> 속성을 1,000(기본값은 100)으로 설정합니다.<br>
(3) <code>DisconnectDelay</code> 속성을 30 속성 초로 설정합니다(기본값은 5초 — 5 </em> 1000).</p>