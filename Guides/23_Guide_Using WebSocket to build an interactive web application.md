<p>이 가이드에서는 브라우저와 서버 간에 메시지를 주고받는 "Hello, world" 애플리케이션을 만드는 과정을 안내합니다. WebSocket은 TCP 위의 얇고 가벼운 계층입니다. 이는 메시지를 삽입하기 위해 "하위 프로토콜"을 사용하는 데 적합합니다. 이 가이드에서는 Spring과 함께 STOMP 메시징을 사용하여 대화형 웹 애플리케이션을 만듭니다. STOMP는 하위 수준 WebSocket 위에서 작동하는 하위 프로토콜입니다.</p>
<h2 id="rest-http-websocket">REST, HTTP, WebSocket</h2>
<p>REST는 Representational State Transfer의 약자로, HTTP 프로토콜을 기반으로 하는 아키텍처 스타일입니다. 이는 클라이언트와 서버 간의 통신을 위한 설계 원칙을 정의합니다. </p>
<p>웹소켓(WebSocket)은 TCP 기반의 양방향 통신 프로토콜로, HTTP와는 다르게 단일 연결을 통해 서버와 클라이언트 간에 양방향 통신이 가능합니다. 클라이언트와 서버 간의 실시간 양방향 통신을 지원하며, 지속적인 연결을 통해 데이터를 주고받을 수 있습니다.</p>
<p>HTTP는 클라이언트가 서버에 요청을 보내고, 서버가 그에 따른 응답을 보내는 요청-응답(request-response) 기반의 통신 프로토콜입니다. 각 요청마다 새로운 연결을 맺고 해제하는 방식으로 동작하며, 상태를 유지하지 않는(stateless) 특성을 가지고 있습니다.</p>
<p>간단히 말해, REST는 주로 요청-응답 방식의 통신을 위한 HTTP 기반의 아키텍처 스타일이고, 웹소켓은 실시간 양방향 통신을 위한 프로토콜입니다. REST는 주로 클라이언트가 서버에 요청을 보내고 응답을 받는 형태로 동작하며, 웹소켓은 서버와 클라이언트가 연결된 채로 실시간으로 데이터를 주고받는 것을 지원합니다.</p>
<h2 id="stomp">STOMP</h2>
<p>STOMP(스트리밍 텍스트 지향 메시지 프로토콜: Streaming Text Oriented Messaging Protocol)은 메시지 기반의 통신 프로토콜 중 하나입니다. 주로 웹 애플리케이션과 서버 간의 상호작용을 위해 사용됩니다. </p>
<p>STOMP는 메시지 브로커와 클라이언트 간의 표준화된 메시징을 위한 간단하고 유연한 프로토콜로, 특히 웹소켓(WebSocket)과 함께 사용되어 실시간 웹 애플리케이션에 적합합니다. 주로 웹소켓을 통해 양방향 통신을 활성화하고, STOMP는 웹소켓을 통해 메시지를 보내고 받는데 사용됩니다.</p>
<p>STOMP는 다양한 언어와 플랫폼 간에 상호 운용성을 제공하며, 메시지 브로커와 클라이언트 간의 효율적인 통신을 위한 간단한 프로토콜을 제공합니다. 이를 통해 구독 및 발행(Publish-Subscribe) 메커니즘을 지원하여, 여러 클라이언트가 특정 토픽이나 큐에 메시지를 구독하고 발행할 수 있게 합니다.</p>
<p>일반적으로 STOMP는 메시징 시나리오에서 간단하고 유연한 프로토콜로 사용되며, 웹 애플리케이션에서의 실시간 통신에 유용하게 활용됩니다.</p>
<h2 id="websocket과-rsocket">WebSocket과 RSocket</h2>
<p>RSocket과 WebSocket은 모두 양방향 통신을 지원하는 프로토콜이지만, 몇 가지 중요한 차이점이 있습니다.</p>
<ol>
<li>
<p><strong>메시지 패턴:</strong></p>
<ul>
<li><strong>WebSocket:</strong> 주로 양방향 텍스트 기반의 데이터 전송을 위해 설계되었습니다. 일반적으로 웹 브라우저와 웹 서버 사이에서 실시간 양방향 통신을 위해 사용됩니다.</li>
<li><strong>RSocket:</strong> 다양한 메시지 패턴과 다양한 데이터 전송 방식을 지원합니다. Request-Response, Fire-and-Forget, Request-Stream, Channel 등 다양한 메시지 패턴을 제공하여 효율적이고 유연한 통신을 가능하게 합니다.</li>
</ul>
</li>
<li>
<p><strong>바이너리 프로토콜:</strong></p>
<ul>
<li><strong>WebSocket:</strong> 주로 텍스트 기반의 프로토콜로, 메시지 전송 시에는 텍스트 형식으로 인코딩되어 전송됩니다.</li>
<li><strong>RSocket:</strong> 이진(binary) 프로토콜로 설계되어 있어 데이터를 직렬화하여 전송합니다. 이진 프로토콜을 사용하므로 텍스트 데이터뿐만 아니라 바이너리 데이터도 효율적으로 전송할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>리액티브 스트림 처리:</strong></p>
<ul>
<li><strong>WebSocket:</strong> 특별한 리액티브 처리를 위한 기능을 갖고 있지 않습니다. 데이터를 주고 받는 것이 주 목적입니다.</li>
<li><strong>RSocket:</strong> 리액티브 프로그래밍을 위한 기능을 내장하고 있어, 요청-응답, 스트리밍과 같은 다양한 패턴을 쉽게 구현할 수 있으며, 백프레셔(Backpressure)를 통해 흐름 제어도 가능합니다.</li>
</ul>
</li>
<li>
<p><strong>연결 지속성:</strong></p>
<ul>
<li><strong>WebSocket:</strong> 클라이언트와 서버 간의 연결은 지속적으로 유지됩니다. 이를 통해 실시간 통신을 구현할 수 있습니다.</li>
<li><strong>RSocket:</strong> 연결은 양방향이며, 연결을 유지하거나 끊는 것과 관계 없이 다양한 메시지 패턴을 사용하여 데이터를 교환할 수 있습니다.</li>
</ul>
</li>
</ol>
<p>요약하자면, WebSocket은 주로 웹 브라우저와 서버 간의 양방향 텍스트 기반 통신에 사용되는 것에 비해, RSocket은 더 다양한 메시지 패턴과 이진 프로토콜, 리액티브 기능을 제공하여 훨씬 유연하고 효율적인 통신을 가능하게 합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>사용자 이름이 포함된 메시지를 받아들이는 서버를 구축하게 됩니다. 이에 대한 응답으로 서버는 클라이언트가 구독하는 대기열에 인사말을 푸시합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/22a6a321-fdab-4f24-8ce7-e7ebffdf9694/image.png"></p>
<h2 id="리소스-표현-클래스-생성">리소스 표현 클래스 생성</h2>
<p>이제 프로젝트와 빌드 시스템을 설정했으므로 STOMP 메시지 서비스를 생성할 수 있습니다.</p>
<p>서비스 상호 작용에 대해 생각하여 프로세스를 시작하십시오.</p>
<p>서비스는 본문이 JSON 개체인 STOMP 메시지에 이름이 포함된 메시지를 허용합니다. 이름이 <code>Fred</code>인 경우 메시지는 다음과 유사할 수 있습니다.</p>
<pre><code class="language-json">{
    "name": "Fred"
}</code></pre>
<p>이름을 전달하는 메시지를 모델링하려면 다음 목록(src/main/java/guides/messagingstompwebsocket/HelloMessage.java에서)과 같이 <code>name</code> 속성과 해당 <code>getName()</code> 메서드를 사용하여 일반 Java 객체를 생성할 수 있습니다. )는 다음을 보여줍니다:</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingstompwebsocket</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HelloMessage</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">HelloMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">HelloMessage</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>name <span class="token operator">=</span> name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>메시지를 수신하고 이름을 추출하면 서비스는 인사말을 생성하고 해당 인사말을 클라이언트가 구독하는 별도의 대기열에 게시하여 메시지를 처리합니다. 인사말은 다음 목록과 같이 JSON 개체이기도 합니다.</p>
<pre><code class="language-json">{
    "content": "Hello, Fred!"
}</code></pre>
<p>인사말 표현을 모델링하려면 다음 목록(src/main/java/guides/messagingstompwebsocket/Greeting.java에서)에 표시된 것처럼 <code>content</code> 속성과 해당 <code>getContent()</code> 메서드가 있는 또 다른 일반 Java 객체를 추가하세요.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingstompwebsocket</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Greeting</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token class-name">String</span> content<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getContent</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> content<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>Spring은 Jackson JSON 라이브러리를 사용하여 <code>Greeting</code> 유형의 인스턴스를 JSON으로 자동 마샬링합니다.</p>
<hr>
<p>"Marshalling"은 데이터를 직렬화하여 다른 형식으로 변환하는 프로세스를 의미합니다. 일반적으로 객체를 직렬화하여 텍스트, 바이너리 또는 다른 형식으로 변환하는 과정을 나타냅니다.</p>
<p>자바에서는 주로 객체를 직렬화하여 XML 또는 JSON과 같은 형식으로 변환하는 것을 의미합니다. 예를 들어, Java 객체를 JSON 문자열로 변환하는 것은 객체를 JSON 형식으로 "마샬링한다"고 합니다. </p>
<p>반대로 "언마샬링(Unmarshalling)"은 직렬화된 데이터를 역직렬화하여 객체로 변환하는 것을 의미합니다. JSON 문자열을 Java 객체로 변환하는 것은 JSON을 객체로 "언마샬링한다"고 할 수 있습니다.</p>
<p>이러한 마샬링과 언마샬링은 데이터를 다른 형식으로 변환하여 전송, 저장 또는 처리하기 위해 자주 사용됩니다. 예를 들어, 웹 애플리케이션에서는 클라이언트와 서버 간에 데이터를 주고받을 때 JSON 또는 XML 형식으로 마샬링하고 언마샬링하여 객체 간에 데이터를 교환합니다.</p>
<hr>
<p>다음으로, 안녕하세요 메시지를 받고 인사말 메시지를 보내는 컨트롤러를 만듭니다.</p>
<h2 id="메시지-처리-컨트롤러-만들기">메시지 처리 컨트롤러 만들기</h2>
<p>STOMP 메시징 작업에 대한 Spring의 접근 방식에서 STOMP 메시지는 <code>@Controller</code> 클래스로 라우팅될 수 있습니다. 예를 들어, <code>GreetingController</code>(src/main/java/guides/messagingstompwebsocket/GreetingController.java)는 다음 목록과 같이 <code>/hello</code> 대상에 대한 메시지를 처리하도록 매핑됩니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingstompwebsocket</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>messaging<span class="token punctuation">.</span>handler<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">MessageMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>messaging<span class="token punctuation">.</span>handler<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">SendTo</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">HtmlUtils</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingController</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@MessageMapping</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span>
    <span class="token annotation punctuation">@SendTo</span><span class="token punctuation">(</span><span class="token string">"/topic/greetings"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Greeting</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token class-name">HelloMessage</span> message<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span><span class="token number">1000</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">//simulated delay</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token string">"Hello, "</span> <span class="token operator">+</span> <span class="token class-name">HtmlUtils</span><span class="token punctuation">.</span><span class="token function">htmlEscape</span><span class="token punctuation">(</span>message<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>이 컨트롤러는 간결하고 단순하지만 많은 일이 일어나고 있습니다. 우리는 그것을 단계별로 분해합니다.</p>
<p><code>@MessageMapping</code> 주석은 메시지가 <code>/hello</code> 대상(destination)으로 전송되면 <code>Greeting()</code> 메서드가 호출되도록 보장합니다.</p>
<p>메시지의 페이로드는 <code>Greeting()</code>에 전달되는 <code>HelloMessage</code> 개체에 바인딩됩니다.</p>
<p>내부적으로 메서드 구현은 스레드를 1초 동안 절전 모드로 전환하여 처리 지연을 시뮬레이션합니다. 이는 클라이언트가 메시지를 보낸 후 서버가 메시지를 비동기적으로 처리하는 데 필요한 만큼의 시간이 걸릴 수 있음을 보여주기 위한 것입니다. 클라이언트는 응답을 기다리지 않고 수행해야 하는 작업을 계속할 수 있습니다.</p>
<p>1초 지연 후 <code>greeting()</code> 메서드는 <code>Greeting</code> 객체를 생성하고 이를 반환합니다. 반환 값은 <code>@SendTo</code> 주석에 지정된 대로 <code>/topic/greetings</code>의 모든 구독자에게 브로드캐스팅됩니다. 입력 메시지의 이름은 삭제됩니다. 이 경우 클라이언트측 브라우저 DOM에서 다시 에코(echoed back)되고 다시 렌더링되기 때문입니다.</p>
<hr>
<p><code>HtmlUtils.htmlEscape()</code>는 HTML에서 특수 문자를 이스케이프(escape)하여 안전하게 표현하는 데 사용됩니다. 이 함수는 HTML 코드에서 특정 문자를 해당 문자에 해당하는 HTML 엔티티로 변경합니다.</p>
<p>예를 들어, 사용자가 입력한 문자열에 HTML에서 사용되는 특수 문자(예: <code>&lt;</code>, <code>&gt;</code>, <code>&amp;</code>)가 포함되어 있다면 이를 HTML 엔티티로 변경하여 브라우저가 해당 문자를 특수 문자로 해석하지 않고 그대로 텍스트로 표시되도록 합니다. 이는 주로 사용자 입력을 안전하게 HTML 페이지에 표시하거나 XSS(Cross-site Scripting) 공격을 방지하기 위해 사용됩니다. </p>
<p>예를 들어, <code>HtmlUtils.htmlEscape(message.getName())</code>는 <code>message.getName()</code>에서 반환된 문자열을 HTML에서 특수 문자가 있는 경우 해당 문자를 이스케이프하여 안전하게 표시합니다. 만약 <code>message.getName()</code>이 사용자가 입력한 문자열을 반환한다면, 이를 안전하게 HTML에서 표시할 수 있도록 변환합니다.</p>
<hr>
<h2 id="stomp-메시징을-위한-spring-구성">STOMP 메시징을 위한 Spring 구성</h2>
<p>이제 서비스의 필수 구성 요소가 생성되었으므로 WebSocket 및 STOMP 메시징을 활성화하도록 Spring을 구성할 수 있습니다.</p>
<p>다음 목록과 유사한 <code>WebSocketConfig</code>라는 Java 클래스를 만듭니다(src/main/java/guides/messagingstompwebsocket/WebSocketConfig.java에서).</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingstompwebsocket</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>messaging<span class="token punctuation">.</span>simp<span class="token punctuation">.</span>config</span><span class="token punctuation">.</span><span class="token class-name">MessageBrokerRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>socket<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">EnableWebSocketMessageBroker</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>socket<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">StompEndpointRegistry</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>socket<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">WebSocketMessageBrokerConfigurer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebSocketMessageBroker</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebSocketConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebSocketMessageBrokerConfigurer</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureMessageBroker</span><span class="token punctuation">(</span><span class="token class-name">MessageBrokerRegistry</span> config<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        config<span class="token punctuation">.</span><span class="token function">enableSimpleBroker</span><span class="token punctuation">(</span><span class="token string">"/topic"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        config<span class="token punctuation">.</span><span class="token function">setApplicationDestinationPrefixes</span><span class="token punctuation">(</span><span class="token string">"/app"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">registerStompEndpoints</span><span class="token punctuation">(</span><span class="token class-name">StompEndpointRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        registry<span class="token punctuation">.</span><span class="token function">addEndpoint</span><span class="token punctuation">(</span><span class="token string">"/guide-websocket"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span>
</code></pre>
<p><code>WebSocketConfigurer</code> 및 <code>WebSocketMessageBrokerConfigurer</code>는 스프링에서 WebSocket을 설정하고 구성하는 데 사용되는 인터페이스입니다.</p>
<ol>
<li>
<p><strong>WebSocketConfigurer vs. WebSocketMessageBrokerConfigurer:</strong></p>
<ul>
<li><strong>WebSocketConfigurer:</strong> 이 인터페이스는 기본적인 WebSocket 구성을 설정할 때 사용됩니다. 주로 웹소켓 핸들러(WebSocketHandler) 및 엔드포인트 등을 구성하는 데 활용됩니다.</li>
<li><strong>WebSocketMessageBrokerConfigurer:</strong> 이 인터페이스는 STOMP 메시징 서브 프로토콜을 사용하여 메시지 브로커를 구성할 때 사용됩니다. 주로 WebSocket과 Message Broker를 조율하고 설정하는 데 활용됩니다. 메시지 브로커를 사용하여 클라이언트 간에 메시지를 전달하고 관리하는 데 중점을 둡니다.</li>
</ul>
</li>
<li>
<p><strong>@EnableWebSocket vs. @EnableWebSocketMessageBroker:</strong></p>
<ul>
<li><strong>@EnableWebSocket:</strong> 이 어노테이션은 기본적인 WebSocket을 활성화할 때 사용됩니다. 기본 WebSocket 구성을 설정하고 활성화할 때 이 어노테이션을 사용합니다.</li>
<li><strong>@EnableWebSocketMessageBroker:</strong> 이 어노테이션은 STOMP 메시징 서브 프로토콜을 사용하여 메시지 브로커를 활성화할 때 사용됩니다. 주로 WebSocket을 활성화하고 Message Broker를 구성할 때 이 어노테이션을 사용합니다.</li>
</ul>
</li>
</ol>
<p>간단히 말해서, <code>WebSocketConfigurer</code>와 <code>@EnableWebSocket</code>은 기본 WebSocket 설정에 중점을 두고, <code>WebSocketMessageBrokerConfigurer</code>와 <code>@EnableWebSocketMessageBroker</code>는 WebSocket을 활성화하고 메시지 브로커를 구성하는 데 중점을 둡니다.</p>
<hr>
<p><code>WebSocketConfig</code>에는 <code>@Configuration</code>이라는 주석이 달려 있어 Spring 구성 클래스임을 나타냅니다. 또한 <code>@EnableWebSocketMessageBroker</code>라는 주석이 추가됩니다. 이름에서 알 수 있듯이 <code>@EnableWebSocketMessageBroker</code>는 메시지 브로커가 지원하는 WebSocket 메시지 처리를 활성화합니다.</p>
<p><code>configureMessageBroker()</code> 메서드는 메시지 브로커를 구성하기 위해 <code>WebSocketMessageBrokerConfigurer</code>의 기본 메서드를 구현합니다. 간단한 메모리 기반 메시지 브로커가 접두사가 <code>/topic</code>인 대상의 클라이언트에 인사말 메시지를 다시 전달할 수 있도록 활성화하기 위해 <code>enableSimpleBroker()</code>를 호출하는 것으로 시작됩니다. 또한 <code>@MessageMapping</code>으로 주석이 달린 메서드에 바인딩된 메시지에 대해 <code>/app</code> 접두사를 지정합니다. 이 접두사는 모든 메시지 매핑을 정의하는 데 사용됩니다. 예를 들어 <code>/app/hello</code>는 <code>GreetingController.greeting()</code> 메서드가 처리하도록 매핑된 엔드포인트입니다.<br>
<code>registerStompEndpoints()</code> 메서드는 웹소켓 연결을 위해 <code>/guide-websocket</code> 엔드포인트를 등록합니다.</p>
<h2 id="브라우저-클라이언트-생성">브라우저 클라이언트 생성</h2>
<p>서버측 부분이 준비되면 서버측과 메시지를 보내고 받는 JavaScript 클라이언트에 주의를 돌릴 수 있습니다.</p>
<p>다음 목록과 유사한 <code>index.html</code> 파일을 만듭니다(src/main/resources/static/index.html에서).</p>
<pre><code class="language-html"><span class="token doctype">&lt;!DOCTYPE html&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>html</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>head</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>title</span><span class="token punctuation">&gt;</span></span>Hello WebSocket<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>title</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>link</span> <span class="token attr-name">rel</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>stylesheet<span class="token punctuation">"</span></span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css<span class="token punctuation">"</span></span> <span class="token attr-name">integrity</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u<span class="token punctuation">"</span></span> <span class="token attr-name">crossorigin</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anonymous<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>link</span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/main.css<span class="token punctuation">"</span></span> <span class="token attr-name">rel</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>stylesheet<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>script</span> <span class="token attr-name">src</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://code.jquery.com/jquery-3.1.1.min.js<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token script"></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>script</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>script</span> <span class="token attr-name">src</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://cdn.jsdelivr.net/npm/@stomp/stompjs@7.0.0/bundles/stomp.umd.min.js<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token script"></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>script</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>script</span> <span class="token attr-name">src</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/app.js<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token script"></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>script</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>head</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>noscript</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h2</span><span class="token style-attr language-css"><span class="token attr-name"> <span class="token attr-name">style</span></span><span class="token punctuation">="</span><span class="token attr-value"><span class="token property">color</span><span class="token punctuation">:</span> #ff0000</span><span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Seems your browser doesn't support Javascript! Websocket relies on Javascript being
    enabled. Please enable
    Javascript and reload this page!<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h2</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>noscript</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>main-content<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>container<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>row<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>col-md-6<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form-inline<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form-group<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>label</span> <span class="token attr-name">for</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>connect<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>WebSocket connection:<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>label</span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>button</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>connect<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>btn btn-default<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Connect<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>button</span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>button</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>disconnect<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>btn btn-default<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">disabled</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>disabled<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Disconnect
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>button</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>col-md-6<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form-inline<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form-group<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>label</span> <span class="token attr-name">for</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>name<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>What is your name?<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>label</span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>text<span class="token punctuation">"</span></span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>name<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form-control<span class="token punctuation">"</span></span> <span class="token attr-name">placeholder</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Your name here...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>button</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>send<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>btn btn-default<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Send<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>button</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>row<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>col-md-12<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>table</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>conversation<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>table table-striped<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>thead</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tr</span><span class="token punctuation">&gt;</span></span>
                    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>th</span><span class="token punctuation">&gt;</span></span>Greetings<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>th</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tr</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>thead</span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>tbody</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>greetings<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
                <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>tbody</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>table</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>html</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 HTML 파일은 websocket을 통한 STOMP를 통해 서버와 통신하는 데 사용되는 <code>StompJS</code> 자바스크립트 라이브러리를 가져옵니다. 또한 클라이언트 애플리케이션의 논리(logic)가 포함된 <code>app.js</code>를 가져옵니다. 다음 목록(src/main/resources/static/app.js의)은 해당 파일을 보여줍니다.</p>
<pre><code class="language-js"><span class="token keyword">const</span> stompClient <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">StompJs<span class="token punctuation">.</span>Client</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
    brokerURL<span class="token punctuation">:</span> <span class="token string">'ws://localhost:8080/guide-websocket'</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

stompClient<span class="token punctuation">.</span><span class="token function-variable function">onConnect</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token parameter">frame</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
    <span class="token function">setConnected</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">'Connected: '</span> <span class="token operator">+</span> frame<span class="token punctuation">)</span><span class="token punctuation">;</span>
    stompClient<span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span><span class="token string">'/topic/greetings'</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token parameter">greeting</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
        <span class="token function">showGreeting</span><span class="token punctuation">(</span><span class="token constant">JSON</span><span class="token punctuation">.</span><span class="token function">parse</span><span class="token punctuation">(</span>greeting<span class="token punctuation">.</span>body<span class="token punctuation">)</span><span class="token punctuation">.</span>content<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

stompClient<span class="token punctuation">.</span><span class="token function-variable function">onWebSocketError</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token parameter">error</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
    console<span class="token punctuation">.</span><span class="token function">error</span><span class="token punctuation">(</span><span class="token string">'Error with websocket'</span><span class="token punctuation">,</span> error<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

stompClient<span class="token punctuation">.</span><span class="token function-variable function">onStompError</span> <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token parameter">frame</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
    console<span class="token punctuation">.</span><span class="token function">error</span><span class="token punctuation">(</span><span class="token string">'Broker reported error: '</span> <span class="token operator">+</span> frame<span class="token punctuation">.</span>headers<span class="token punctuation">[</span><span class="token string">'message'</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    console<span class="token punctuation">.</span><span class="token function">error</span><span class="token punctuation">(</span><span class="token string">'Additional details: '</span> <span class="token operator">+</span> frame<span class="token punctuation">.</span>body<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token keyword">function</span> <span class="token function">setConnected</span><span class="token punctuation">(</span><span class="token parameter">connected</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#connect"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">prop</span><span class="token punctuation">(</span><span class="token string">"disabled"</span><span class="token punctuation">,</span> connected<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#disconnect"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">prop</span><span class="token punctuation">(</span><span class="token string">"disabled"</span><span class="token punctuation">,</span> <span class="token operator">!</span>connected<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>connected<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#conversation"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">show</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">else</span> <span class="token punctuation">{</span>
        <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#conversation"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">hide</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#greetings"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">html</span><span class="token punctuation">(</span><span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">function</span> <span class="token function">connect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    stompClient<span class="token punctuation">.</span><span class="token function">activate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">function</span> <span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    stompClient<span class="token punctuation">.</span><span class="token function">deactivate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">setConnected</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Disconnected"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">function</span> <span class="token function">sendName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    stompClient<span class="token punctuation">.</span><span class="token function">publish</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
        destination<span class="token punctuation">:</span> <span class="token string">"/app/hello"</span><span class="token punctuation">,</span>
        body<span class="token punctuation">:</span> <span class="token constant">JSON</span><span class="token punctuation">.</span><span class="token function">stringify</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token string">'name'</span><span class="token punctuation">:</span> <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#name"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">val</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">function</span> <span class="token function">showGreeting</span><span class="token punctuation">(</span><span class="token parameter">message</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"#greetings"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">append</span><span class="token punctuation">(</span><span class="token string">"&lt;tr&gt;&lt;td&gt;"</span> <span class="token operator">+</span> message <span class="token operator">+</span> <span class="token string">"&lt;/td&gt;&lt;/tr&gt;"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token function">$</span><span class="token punctuation">(</span><span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token function">$</span><span class="token punctuation">(</span><span class="token string">"form"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">on</span><span class="token punctuation">(</span><span class="token string">'submit'</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token parameter">e</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> e<span class="token punctuation">.</span><span class="token function">preventDefault</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">$</span><span class="token punctuation">(</span> <span class="token string">"#connect"</span> <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">click</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token function">connect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">$</span><span class="token punctuation">(</span> <span class="token string">"#disconnect"</span> <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">click</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token function">disconnect</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">$</span><span class="token punctuation">(</span> <span class="token string">"#send"</span> <span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">click</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token function">sendName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이해해야 할 이 JavaScript 파일의 주요 부분은 <code>stompClient.onConnect</code> 및 <code>sendName</code> 함수입니다.</p>
<p><code>stompClient</code>는 웹소켓 서버가 연결을 기다리는 위치인 <code>/guide-websocket</code> 경로를 참조하는 <code>brokerURL</code>로 초기화됩니다. 성공적으로 연결되면 클라이언트는 서버가 인사말 메시지를 게시할 <code>/topic/greetings</code> 대상(destination)을 구독합니다. 해당 대상에서 greeting을 받으면 DOM에 단락 요소(paragraph)를 추가하여 인사말 메시지를 표시합니다.</p>
<p><code>sendName()</code> 함수는 사용자가 입력한 이름을 검색하고 STOMP 클라이언트를 사용하여 이를 <code>/app/hello</code> 대상(destination)(<code>GreetingController.greeting()</code>이 수신함)으로 보냅니다.</p>
<p>원하는 경우 <code>main.css</code>를 생략하거나 <code>&lt;link&gt;</code>를 확인할 수 있도록 빈 것을 만들 수 있습니다.</p>
<hr>
<p><code>app.js</code> 코드는 WebSocket 클라이언트 측의 동작을 정의하고 있습니다. 이 코드는 HTML과 상호작용하여 웹소켓 서버와의 연결을 수립하고, 메시지를 보내고 받는 등의 작업을 수행합니다.</p>
<ol>
<li>
<p><code>stompClient</code> 객체를 생성하고 <code>brokerURL</code>을 통해 서버의 WebSocket 엔드포인트에 연결합니다. 이를 통해 서버와 통신할 수 있게 됩니다.</p>
</li>
<li>
<p><code>stompClient.onConnect</code>: WebSocket 연결이 성공적으로 이루어졌을 때 실행되는 함수로, 연결 성공 시 서버로부터 받을 구독(subscribe)을 정의합니다. <code>/topic/greetings</code>로부터 메시지를 구독하여 <code>showGreeting</code> 함수를 호출하여 메시지를 보여줍니다.</p>
</li>
<li>
<p><code>setConnected</code>: 연결 상태에 따라 버튼과 대화창의 표시 여부를 조정합니다.</p>
</li>
<li>
<p><code>connect</code>, <code>disconnect</code>, <code>sendName</code>: 버튼 클릭 시 실행될 함수들로, 각각 연결, 연결 해제, 메시지 전송 기능을 수행합니다.</p>
</li>
<li>
<p><code>showGreeting</code>: 받은 메시지를 화면에 표시합니다.</p>
</li>
</ol>
<p>이 코드에서 HTML과의 상호작용은 주로 버튼 클릭 시 발생합니다. 예를 들어 <code>$( "#connect" ).click(() =&gt; connect())</code>는 "Connect" 버튼이 클릭되면 <code>connect</code> 함수를 호출하여 서버와 연결을 시도하게 됩니다. 마찬가지로 <code>$( "#disconnect" ).click(() =&gt; disconnect())</code>는 "Disconnect" 버튼이 클릭되면 <code>disconnect</code> 함수를 호출하여 연결을 해제하게 됩니다. <code>$( "#send" ).click(() =&gt; sendName())</code>는 "Send" 버튼이 클릭되면 <code>sendName</code> 함수를 호출하여 입력된 이름을 서버로 전송합니다.</p>
<hr>
<p><code>$</code>는 주로 jQuery 라이브러리에서 사용되는 함수를 나타냅니다. jQuery는 JavaScript를 보다 쉽게 다룰 수 있도록 도와주는 라이브러리 중 하나로, HTML 요소를 선택하고 조작하는 데 사용됩니다. <code>$</code>는 jQuery 객체에 접근하고 그 객체에 정의된 함수들을 호출하기 위한 약식 표기법입니다.</p>
<p>예를 들어, <code>$("form")</code>은 문서 내의 모든 <code>&lt;form&gt;</code> 요소를 선택합니다. <code>$("#connect")</code>, <code>$("#disconnect")</code>, <code>$("#send")</code>는 각각 id가 "connect", "disconnect", "send"인 요소를 선택합니다. <code>$(function () { ... })</code>는 문서가 로드될 때 실행될 함수를 정의하는 jQuery 문법입니다. </p>
<p>이렇게 <code>$</code>를 사용하여 HTML 요소를 선택하거나 함수를 실행함으로써 jQuery를 사용하여 웹페이지의 동작을 제어하고 상호작용할 수 있습니다.</p>
<hr>
<p>Destination과 endpoint는 메시지나 데이터가 도착하거나 출발하는 위치를 나타내는데 사용됩니다. 이 둘은 유사한 개념이지만 사용되는 맥락에 따라 차이가 있습니다.</p>
<p><strong>공통점:</strong></p>
<ul>
<li><strong>위치 지정:</strong> 둘 다 메시지의 목적지 또는 출발지를 나타냅니다.</li>
<li><strong>통신에 사용:</strong> 클라이언트와 서버 간의 통신에서 메시지나 데이터를 보내거나 받을 때 사용됩니다.</li>
</ul>
<p><strong>차이점:</strong></p>
<ul>
<li><strong>사용 맥락:</strong> <ul>
<li><code>Destination</code>: 주로 메시지 브로커나 메시징 시스템에서 사용되며, 메시지의 목적지를 가리킵니다. 구독자가 해당 목적지를 구독하고, 메시지가 해당 목적지로 전달됩니다.</li>
<li><code>Endpoint</code>: 주로 네트워크 통신에서 사용되며, 특정 URL이나 경로를 가리키는 것으로, 클라이언트가 서버에 연결하는 지점을 나타냅니다. HTTP, 웹소켓 등의 통신에서 사용되며, 클라이언트가 서버와 통신하기 위해 해당 엔드포인트에 연결합니다.</li>
</ul></li>
</ul>
<p><strong>URL이 endpoint와 destination에서 모두 쓰이는 경우:</strong></p>
<ul>
<li><strong>Endpoint:</strong><ul>
<li><strong>HTTP</strong>: 클라이언트가 특정 URL로 요청을 보내거나 서버에 연결하기 위해 사용됩니다. 예를 들어, <code>/api/data</code>, <code>/user/login</code> 등의 URL이 엔드포인트 역할을 할 수 있습니다.</li>
<li><strong>웹소켓</strong>: 클라이언트가 웹소켓 서버에 연결하는데 사용됩니다. <code>/ws</code>, <code>/chat</code>, <code>/notifications</code> 등의 URL이 웹소켓 연결 엔드포인트로 사용될 수 있습니다.</li>
</ul></li>
<li><strong>Destination:</strong><ul>
<li><strong>메시징 시스템</strong>: 특정 주제나 채널을 가리키며, 메시지 브로커에서 메시지를 발행하거나 구독하는데 사용됩니다. <code>/topic/greetings</code>, <code>/queue/messages</code> 등의 URL이 목적지로 사용될 수 있습니다.</li>
</ul></li>
</ul>
<p>즉, URL이 엔드포인트로 사용되는 경우 클라이언트가 연결하거나 요청을 보내는 위치를 가리키며, 메시지 브로커에서 목적지로 사용될 경우 메시지가 발행되거나 구독되는 위치를 가리킵니다.</p>
<hr>
<h2 id="애플리케이션을-실행-가능하게-만들기">애플리케이션을 실행 가능하게 만들기</h2>
<p>Spring Boot는 당신을 위해 애플리케이션 클래스를 생성합니다. 이 경우 더 이상 수정이 필요하지 않습니다. 이 응용 프로그램을 실행하는 데 사용할 수 있습니다. 다음 목록(src/main/java/guides/messagingstompwebsocket/MessagingStompWebsocketApplication.java)은 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingstompwebsocket</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MessagingStompWebsocketApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">MessagingStompWebsocketApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<p>애플리케이션을 실행하면 로깅 출력이 표시됩니다. 서비스는 몇 초 내에 시작되어 실행되어야 합니다.</p>
<h2 id="서비스-테스트">서비스 테스트</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2d77e6ef-d0bd-4a98-b10d-3177747cb5d5/image.png"></p>
<p>이제 서비스가 실행 중이므로 브라우저에서 <a href="http://localhost:8080%EC%9D%84">http://localhost:8080을</a> 가리키고 연결 버튼을 클릭하세요.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/edf4bc25-f328-4850-bf55-d62940527639/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9255908d-dc68-4de1-8c4f-213c7f3cb738/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/abcaf272-ff26-4ed0-9c92-920d6e8abf2c/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6d8a22a2-f269-4458-b5f9-25bbc9563d8b/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/74696eae-c4de-4334-b504-5484ae39996c/image.png"></p>
<p>연결을 열면 이름을 묻는 메시지가 나타납니다. 이름을 입력하고 보내기를 클릭하세요. 귀하의 이름은 STOMP를 통해 JSON 메시지로 서버에 전송됩니다. 1초의 시뮬레이션 지연 후 서버는 페이지에 표시되는 "안녕하세요" 인사말이 포함된 메시지를 다시 보냅니다. 이 시점에서 다른 이름을 보내거나 연결 끊기 버튼을 클릭하여 연결을 닫을 수 있습니다.</p>
<hr>
<p>물론이죠! 여기에서는 Spring의 WebSocket과 STOMP를 사용하여 클라이언트와 서버 간에 상호작용하는 방법을 살펴볼게요.</p>
<ol>
<li>
<p><strong>코드 1 - 서버 측 GreetingController:</strong></p>
<ul>
<li><code>/hello</code>로 메시지 매핑하고, <code>HelloMessage</code>를 받아서 <code>greeting()</code> 메서드에서 처리합니다.</li>
<li><code>greeting()</code> 메서드는 1초의 딜레이를 시뮬레이션하고, <code>/topic/greetings</code>로 <code>Greeting</code> 객체를 반환합니다.</li>
</ul>
</li>
<li>
<p><strong>코드 2 - WebSocketConfig:</strong></p>
<ul>
<li>WebSocket 설정을 구성합니다.</li>
<li><code>configureMessageBroker()</code> 메서드에서 메시지 브로커를 활성화하고 <code>/topic</code>으로 구독 및 발행을 활성화합니다.</li>
<li><code>registerStompEndpoints()</code> 메서드에서 클라이언트에서 <code>/guide-websocket</code>으로 웹소켓 연결을 설정합니다.</li>
</ul>
</li>
<li>
<p><strong>코드 3 - 메인 애플리케이션:</strong></p>
<ul>
<li>Spring Boot 애플리케이션을 실행합니다.</li>
</ul>
</li>
<li>
<p><strong>코드 4 - 클라이언트 측 JavaScript:</strong></p>
<ul>
<li>클라이언트에서 <code>StompJs</code>를 사용하여 서버에 연결합니다. <code>ws://localhost:8080/guide-websocket</code>으로 WebSocket 연결을 설정합니다.</li>
<li><code>onConnect</code> 콜백에서 연결이 성공하면 <code>/topic/greetings</code>를 구독하고, 받은 메시지를 <code>showGreeting()</code> 함수로 처리합니다.</li>
<li><code>setConnected()</code> 함수는 연결 상태에 따라 UI를 변경합니다.</li>
<li><code>connect()</code>, <code>disconnect()</code>, <code>sendName()</code> 함수는 각각 연결, 연결 종료, 메시지 전송을 수행합니다.</li>
<li><code>showGreeting()</code> 함수는 받은 메시지를 특정 DOM 요소에 추가합니다.</li>
<li>jQuery를 사용하여 HTML 폼 요소와 버튼에 이벤트를 연결하고, 웹소켓을 통해 서버에 메시지를 전송하고 받습니다.</li>
</ul>
</li>
</ol>
<p>이렇게 서버 사이드와 클라이언트 사이드가 WebSocket을 사용하여 메시지를 주고받고, 특정 엔드포인트(<code>/hello</code>)와 목적지(<code>/topic/greetings</code>)를 통해 메시지를 처리하고 구독하는 방식으로 상호작용합니다.</p>
<hr>
<p>웹소켓(WebSocket)은 URL이 변경되지 않고도 백그라운드에서 서버와 지속적인 양방향 통신을 제공할 수 있습니다. 보통 웹소켓은 기존의 HTTP/HTTPS 프로토콜을 통해 연결이 이루어지지만, 브라우저의 주소창 URL은 변경되지 않습니다.</p>
<p>따라서 <code>localhost:8080</code>에 액세스하면 웹 애플리케이션이 시작되고, JavaScript를 통해 웹소켓을 설정하고 사용하더라도 브라우저 URL은 변경되지 않을 것입니다. 클라이언트 측 코드에서 WebSocket을 초기화할 때 사용하는 URL이 <code>ws://localhost:8080/guide-websocket</code>로 설정되어 있으며, 이는 WebSocket 연결을 설정하기 위한 URL입니다.</p>
<p>클라이언트는 JavaScript를 사용하여 WebSocket을 열고 서버로 메시지를 보내거나 서버로부터 메시지를 수신할 수 있지만, 이는 브라우저의 URL과 직접적으로 관련되어 있는 것이 아니기 때문에 주소창의 URL이 변경되지 않는 것입니다.</p>
<hr>
<p>비동기 통신은 브라우저 URL에 영향을 미치지 않고 클라이언트와 서버 간에 데이터를 주고받을 수 있습니다. 이는 페이지의 전체 콘텐츠를 새로고침하지 않고도 서버로부터 데이터를 받아오거나 보낼 수 있는 장점을 갖고 있습니다. 주로 JavaScript와 XMLHttpRequest 객체 또는 최근에는 Fetch API, Axios 등을 사용하여 구현됩니다. 이를 통해 웹 페이지의 특정 부분만 업데이트하거나 동적으로 데이터를 추가하는 등의 기능을 구현할 수 있습니다. 이러한 특성 때문에 페이지가 새로고침되지 않고도 서버와의 통신을 효과적으로 처리할 수 있습니다.</p>
<hr>
<p>브라우저의 URL은 사용자가 새로운 페이지로 이동하거나 새로고침할 때 변경됩니다. 일반적으로 다음과 같은 상황에서 URL이 변경됩니다:</p>
<ol>
<li><strong>링크를 클릭하거나 URL을 직접 입력할 때:</strong> 사용자가 주소창에 새 URL을 입력하거나 새로운 링크를 클릭하면 해당 URL로 이동하게 됩니다.</li>
<li><strong>페이지 이동 또는 리디렉션:</strong> JavaScript나 서버에서 페이지 이동을 처리하는 경우, URL은 새 페이지로 변경될 수 있습니다.</li>
<li><strong>SPA(Single Page Application)에서의 라우팅:</strong> 일부 웹 애플리케이션은 단일 페이지에서 여러 뷰를 관리하고 URL을 업데이트하여 콘텐츠를 동적으로 변경합니다.</li>
</ol>
<p>웹소켓이나 AJAX를 사용하여 데이터를 가져오는 것은 브라우저의 URL을 직접적으로 변경하지 않습니다. 이들은 페이지를 다시 로드하지 않고도 데이터를 백그라운드에서 가져오거나 업데이트할 수 있지만, 이러한 요청은 주소창의 URL을 변경하지 않습니다. 페이지를 새로 로드하거나 다른 URL로 이동하려면 사용자 또는 애플리케이션이 이에 상응하는 명령을 내려야 합니다.</p>