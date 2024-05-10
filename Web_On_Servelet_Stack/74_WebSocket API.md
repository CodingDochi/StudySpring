<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-websocket.html#webflux-websocket-server">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring Framework는 WebSocket 메시지를 처리하는 클라이언트 측 및 서버 측 애플리케이션을 작성하는 데 사용할 수 있는 WebSocket API를 제공합니다.</p>
<h1 id="websockethandler"><code>WebSocketHandler</code></h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-websocket.html#webflux-websocket-server-handler">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p>WebSocket 서버를 생성하는 것은 <code>WebSocketHandler</code>를 구현하거나 <code>TextWebSocketHandler</code> 또는 <code>BinaryWebSocketHandler</code>를 확장하는 것만큼 간단합니다. 다음 예제에서는 <code>TextWebSocketHandler</code>를 사용합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyHandler</span> <span class="token keyword">extends</span> <span class="token class-name">TextWebSocketHandler</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">handleTextMessage</span><span class="token punctuation">(</span><span class="token class-name">WebSocketSession</span> session<span class="token punctuation">,</span> <span class="token class-name">TextMessage</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 이전 WebSocket 핸들러를 특정 URL에 매핑하기 위한 전용 WebSocket Java 구성 및 XML 네임스페이스 지원이 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocket</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerWebSocketHandlers</span><span class="token punctuation">(</span><span class="token class-name">WebSocketHandlerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addHandler</span><span class="token punctuation">(</span><span class="token function">myHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"/myHandler"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">WebSocketHandler</span> <span class="token function">myHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MyHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
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
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">websocket:</span>handlers</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.MyHandler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예제는 Spring MVC 애플리케이션에서 사용하기 위한 것이며 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet.html"><code>DispatcherServlet</code></a> 구성에 포함되어야 합니다. 그러나 Spring의 WebSocket 지원은 Spring MVC에 의존하지 않습니다. <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/socket/server/support/WebSocketHttpRequestHandler.html"><code>WebSocketHttpRequestHandler</code></a>의 도움으로 <code>WebSocketHandler</code>를 다른 HTTP 제공 환경에 통합하는 것은 비교적 간단합니다.</p>
<p><code>WebSocketHandler</code> API를 직접 사용하거나 간접적으로 사용하는 경우. 기본 표준 WebSocket 세션(JSR-356)은 동시 전송을 허용하지 않으므로 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/stomp.html">STOMP</a> 메시징을 통해 애플리케이션은 메시지 전송을 동기화해야 합니다. 한 가지 옵션은 <code>WebSocketSession</code>을 <code>ConcurrentWebSocketSessionDecorator</code>로 래핑하는 것입니다.</p>
<h1 id="websocket-handshake">WebSocket Handshake</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-websocket.html#webflux-websocket-server-handshake">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>초기 HTTP WebSocket 핸드셰이크 요청을 사용자 정의하는 가장 쉬운 방법은 핸드셰이크 "이전"과 "이후"에 대한 메서드를 노출하는 <code>HandshakeInterceptor</code>를 사용하는 것입니다. 이러한 인터셉터를 사용하여 핸드셰이크를 방지하거나 <code>WebSocketSession</code>에서 모든 속성을 사용할 수 있도록 할 수 있습니다. 다음 예에서는 내장 인터셉터를 사용하여 HTTP 세션 속성을 WebSocket 세션에 전달합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocket</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerWebSocketHandlers</span><span class="token punctuation">(</span><span class="token class-name">WebSocketHandlerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addHandler</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"/myHandler"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addInterceptors</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">HttpSessionHandshakeInterceptor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
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
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>handshake-interceptors</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.socket.server.support.HttpSessionHandshakeInterceptor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">websocket:</span>handshake-interceptors</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">websocket:</span>handlers</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.MyHandler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>더 고급 옵션은 클라이언트 원본 유효성 검사, 하위 프로토콜 협상 및 기타 세부 정보를 포함하여 WebSocket 핸드셰이크 단계를 수행하는 <code>DefaultHandshakeHandler</code>를 확장하는 것입니다. 아직 지원되지 않는 WebSocket 서버 엔진 및 버전에 적응하기 위해 사용자 정의 <code>RequestUpgradeStrategy</code>를 구성해야 하는 경우 애플리케이션은 이 옵션을 사용해야 할 수도 있습니다(이 주제에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/server.html#websocket-server-deployment">배포</a> 참조). Java 구성과 XML 네임스페이스 모두를 통해 사용자 정의 <code>HandshakeHandler</code>를 구성할 수 있습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
Spring은 추가 동작으로 <code>WebSocketHandler</code>를 장식하는 데 사용할 수 있는 <code>WebSocketHandlerDecorator</code> 기본 클래스를 제공합니다. WebSocket Java 구성 또는 XML 네임스페이스를 사용할 때 기본적으로 로깅 및 예외 처리 구현이 제공되고 추가됩니다. <code>ExceptionWebSocketHandlerDecorator</code>는 <code>WebSocketHandler</code> 메서드에서 발생하는 포착되지 않은 모든 예외를 포착하고 서버 오류를 나타내는 상태 <code>1011</code>로 WebSocket 세션을 닫습니다.</p>
</blockquote>
<h1 id="deployment">Deployment</h1>
<p>Spring WebSocket API는 <code>DispatcherServlet</code>이 HTTP WebSocket 핸드셰이크와 기타 HTTP 요청을 모두 제공하는 Spring MVC 애플리케이션에 쉽게 통합됩니다. <code>WebSocketHttpRequestHandler</code>를 호출하여 다른 HTTP 처리 시나리오에 쉽게 통합할 수도 있습니다. 이것은 편리하고 이해하기 쉽습니다. 그러나 JSR-356 런타임과 관련하여 특별한 고려 사항이 적용됩니다.</p>
<p>Jakarta WebSocket API(JSR-356)는 두 가지 배포 메커니즘을 제공합니다. 첫 번째는 시작 시 Servlet 컨테이너 클래스 경로 검색(Servlet 3 기능)과 관련됩니다. 다른 하나는 서블릿 컨테이너 초기화 시 사용하는 등록 API입니다. 이러한 메커니즘 중 어느 것도 모든 HTTP 처리(WebSocket 핸드셰이크 및 기타 모든 HTTP 요청 포함)(예: Spring MVC의 <code>DispatcherServlet</code>)에 대해 단일 "프론트 컨트롤러"를 사용할 수 없습니다.</p>
<p>이는 JSR-356 런타임에서 실행될 때에도 Spring의 WebSocket 지원이 서버별 <code>RequestUpgradeStrategy</code> 구현으로 주소를 지원하는 JSR-356의 중요한 제한 사항입니다. 이러한 전략은 현재 Tomcat, Jetty, GlassFish, WebLogic, WebSphere 및 Undertow(및 WildFly)에 존재합니다. Jakarta WebSocket 2.1부터는 Tomcat 10.1 및 Jetty 12와 같은 Jakarta EE 10 기반 웹 컨테이너에서 Spring이 선택하는 표준 요청 업그레이드 전략을 사용할 수 있습니다.</p>
<p>두 번째 고려 사항은 JSR-356을 지원하는 서블릿 컨테이너가 애플리케이션 시작 속도를 크게 늦출 수 있는 SCI(<code>ServletContainerInitializer</code>) 스캔을 수행해야 한다는 것입니다. JSR-356을 지원하는 서블릿 컨테이너 버전으로 업그레이드한 후 상당한 영향이 관찰되면 <code>web.xml</code>의 <code>&lt;absolute-ordering /&gt;</code> 요소를 사용하여 웹 fragment(및 SCI 스캐닝)을 선택적으로 활성화 또는 비활성화할 수 있어야 합니다. (다음 예제와 같이):</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://jakarta.ee/xml/ns/jakartaee<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		https://jakarta.ee/xml/ns/jakartaee
		https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd<span class="token punctuation">"</span></span>
	<span class="token attr-name">version</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>5.0<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>absolute-ordering</span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>그런 다음 Servlet 3 Java 초기화 API에 대한 지원을 제공하는 Spring 자체 <code>SpringServletContainerInitializer</code>와 같이 이름별로 웹 조각을 선택적으로 활성화할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://jakarta.ee/xml/ns/jakartaee<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		https://jakarta.ee/xml/ns/jakartaee
		https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd<span class="token punctuation">"</span></span>
	<span class="token attr-name">version</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>5.0<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>absolute-ordering</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>spring_web<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>absolute-ordering</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<h1 id="configuring-the-server">Configuring the Server</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-websocket.html#webflux-websocket-server-config">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>입력 메시지 버퍼 크기, 유휴 시간 제한 등과 같은 기본 WebSocket 서버를 구성할 수 있습니다.</p>
<p>Jakarta WebSocket 서버의 경우 Java 구성에 <code>ServletServerContainerFactoryBean</code>을 추가할 수 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">ServletServerContainerFactoryBean</span> <span class="token function">createWebSocketContainer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">ServletServerContainerFactoryBean</span> container <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ServletServerContainerFactoryBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    container<span class="token punctuation">.</span><span class="token function">setMaxTextMessageBufferSize</span><span class="token punctuation">(</span><span class="token number">8192</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    container<span class="token punctuation">.</span><span class="token function">setMaxBinaryMessageBufferSize</span><span class="token punctuation">(</span><span class="token number">8192</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> container<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>또는 XML 구성에:</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>websocket</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/websocket<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/websocket
		https://www.springframework.org/schema/websocket/spring-websocket.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework...ServletServerContainerFactoryBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>maxTextMessageBufferSize<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>8192<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>maxBinaryMessageBufferSize<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>8192<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
클라이언트 Jakarta WebSocket 구성의 경우 Java 구성에서 ContainerProvider.getWebSocketContainer()를 사용하거나 XML에서 <code>WebSocketContainerFactoryBean</code>을 사용하십시오. </p>
</blockquote>
<p>Jetty의 경우 <code>Consumer</code> 콜백을 제공하여 WebSocket 서버를 구성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocket</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerWebSocketHandlers</span><span class="token punctuation">(</span><span class="token class-name">WebSocketHandlerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addHandler</span><span class="token punctuation">(</span><span class="token function">echoWebSocketHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"/echo"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setHandshakeHandler</span><span class="token punctuation">(</span><span class="token function">handshakeHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DefaultHandshakeHandler</span> <span class="token function">handshakeHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">JettyRequestUpgradeStrategy</span> strategy <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JettyRequestUpgradeStrategy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		strategy<span class="token punctuation">.</span><span class="token function">addWebSocketConfigurer</span><span class="token punctuation">(</span>configurable <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
				policy<span class="token punctuation">.</span><span class="token function">setInputBufferSize</span><span class="token punctuation">(</span><span class="token number">8192</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
				policy<span class="token punctuation">.</span><span class="token function">setIdleTimeout</span><span class="token punctuation">(</span><span class="token number">600000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">DefaultHandshakeHandler</span><span class="token punctuation">(</span>strategy<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
WebSocket을 통해 STOMP를 사용하는 경우 <a href="https://docs.spring.io/spring-framework/reference/web/websocket/stomp/server-config.html">STOMP WebSocket 전송</a> 속성도 구성해야 합니다.</p>
</blockquote>
<h1 id="allowed-origins">Allowed Origins</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-websocket.html#webflux-websocket-server-cors">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring Framework 4.1.5부터 WebSocket 및 SockJS의 기본 동작은 동일한 출처 요청만 허용하는 것입니다. 원본 목록 전체 또는 특정 목록을 허용하는 것도 가능합니다. 이 검사는 주로 브라우저 클라이언트용으로 설계되었습니다. 다른 유형의 클라이언트가 <code>Origin</code> 헤더 값을 수정하는 것을 막을 수 있는 방법은 없습니다(자세한 내용은 <a href="https://datatracker.ietf.org/doc/html/rfc6454">RFC 6454: The Web Origin Concept</a> 참조).</p>
<p>가능한 세 가지 동작은 다음과 같습니다.</p>
<ul>
<li>
<p>same-origin 요청만 허용(기본값): 이 모드에서 SockJS가 활성화되면, Iframe HTTP 응답 헤더 <code>X-Frame-Options</code>가 <code>SAMEORIGIN</code>으로 설정되고, request의 origin을 확인(check)하는 것을 허용하지 않으므로 JSONP 전송이 비활성화됩니다. 결과적으로 이 모드가 활성화되면 IE6 및 IE7은 지원되지 않습니다.</p>
</li>
<li>
<p>지정된 origin 목록 허용: 허용되는 각 origin은 <code>http://</code> 또는 <code>https://</code>로 시작해야 합니다. 이 모드에서는 SockJS가 활성화되면 IFrame 전송이 비활성화됩니다. 결과적으로 이 모드가 활성화되면 IE6부터 IE9까지 지원되지 않습니다.</p>
</li>
<li>
<p>모든 origin 허용: 이 모드를 활성화하려면 허용된 origin 값으로 <code>*</code>를 제공해야 합니다. 이 모드에서는 모든 운송 수단을 사용할 수 있습니다.</p>
</li>
</ul>
<p>다음 예제와 같이 WebSocket 및 SockJS 허용 origin을 구성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocket</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerWebSocketHandlers</span><span class="token punctuation">(</span><span class="token class-name">WebSocketHandlerRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">addHandler</span><span class="token punctuation">(</span><span class="token function">myHandler</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">"/myHandler"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setAllowedOrigins</span><span class="token punctuation">(</span><span class="token string">"https://mydomain.com"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
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

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>handlers</span> <span class="token attr-name">allowed-origins</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://mydomain.com<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">websocket:</span>mapping</span> <span class="token attr-name">path</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">handler</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">websocket:</span>handlers</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myHandler<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.samples.MyHandler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>