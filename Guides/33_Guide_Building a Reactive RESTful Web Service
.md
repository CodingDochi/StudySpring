<p>이 가이드는 "Hello, Spring!"을 만드는 과정을 안내합니다. Spring WebFlux(Spring Boot 2.0의 새로운 기능)를 사용하는 RESTful 웹 서비스를 사용하고 WebClient(또한 Spring Boot 2.0의 새로운 기능)를 통해 해당 서비스를 사용합니다.</p>
<blockquote>
<p>이 가이드는 Spring WebFlux를 사용하는 기능적인 방법을 보여줍니다. WebFlux에서 주석을 사용할 수도 있습니다.</p>
</blockquote>
<h2 id="동기--비동기--블록--논블록">동기 / 비동기 &amp; 블록 / 논블록</h2>
<p><img src="https://cdn.inflearn.com/public/comments/08f0d98d-5a36-4701-880e-e3ca0189d2a3/%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5.png"></p>
<h2 id="reactive-web과-비동기-통신">Reactive Web과 비동기 통신</h2>
<p>Reactive 웹과 RabbitMQ, Redis, JMS Artemis, WebSocket 등과 같은 기술들은 서로 다른 개념과 목적을 가지고 있습니다.</p>
<ol>
<li>
<p><strong>Reactive 웹</strong>:</p>
<ul>
<li>Reactive 웹은 비동기 및 이벤트 기반의 프로그래밍 방식을 강조하는 웹 개발 방법론입니다.</li>
<li>주로 스트림 처리와 데이터 흐름을 중심으로 하는 비동기 프로그래밍에 초점을 두고 있습니다.</li>
<li>Reactive 웹은 Spring WebFlux와 같은 프레임워크를 사용하여 이벤트 기반 및 비동기적인 웹 애플리케이션을 개발하는 것을 의미합니다.</li>
</ul>
</li>
<li>
<p><strong>RabbitMQ, Redis, JMS Artemis, WebSocket</strong>:</p>
<ul>
<li>이들은 비동기 통신을 위한 특정 기술이거나 프로토콜입니다.</li>
<li>RabbitMQ는 메시지 브로커로, 메시지 큐를 통해 비동기 통신을 지원합니다.</li>
<li>Redis는 NoSQL 데이터베이스이면서도 캐시와 메시지 브로커로 사용되며, Pub/Sub 모델을 통해 비동기 통신을 지원합니다.</li>
<li>JMS Artemis는 JMS(Java Message Service)를 구현한 메시지 브로커로, 대용량 메시징 시스템으로 사용됩니다.</li>
<li>WebSocket은 양방향 통신을 지원하는 프로토콜로, 실시간 웹 애플리케이션에서 사용됩니다.</li>
</ul>
</li>
</ol>
<p>Reactive 웹은 비동기 및 스트림 기반의 프로그래밍 방식을 강조하는 웹 개발 방법론이며, 위에서 언급된 기술들은 비동기 통신을 위한 여러 가지 도구와 프로토콜로, Reactive 웹을 개발할 때 이들을 함께 사용할 수 있습니다. 결국, Reactive 웹은 개발 방법론이고, RabbitMQ, Redis, JMS Artemis, WebSocket은 비동기 통신을 위한 도구와 프로토콜입니다.</p>
<h2 id="express는-reactive-web인가">Express는 Reactive Web인가?</h2>
<p>Express는 웹 서버 프레임워크이며, 기본적으로 Reactive 웹 프레임워크는 아닙니다. Express는 Node.js를 위한 비동기적이고 이벤트 기반의 웹 프레임워크로, 자바스크립트로 서버 측 애플리케이션을 개발하는 데 사용됩니다.</p>
<p>Reactive 웹 프레임워크는 비동기 및 이벤트 기반 프로그래밍에 초점을 맞춘 프레임워크로, 보통 Reactive Streams를 기반으로 하며, 스트림 처리와 데이터 흐름을 중심으로 하는 비동기 프로그래밍에 사용됩니다. 대표적인 Reactive 웹 프레임워크로는 Spring WebFlux가 있습니다.</p>
<p>Express는 논블로킹 I/O 모델을 사용하고 비동기적인 방식으로 요청을 처리하지만, Reactive 프로그래밍의 특성을 완전히 따르는 것은 아닙니다. Reactive 웹 프레임워크는 Reactive Streams API를 준수하거나 Flux/Mono와 같은 Reactive 타입을 사용하여 비동기 및 이벤트 기반의 프로그래밍 모델을 제공합니다. Express는 이러한 Reactive Streams를 사용하지 않으며, 전통적인 콜백 또는 Promise 기반의 비동기 방식을 주로 사용합니다.</p>
<h2 id="reactive-streams-api">Reactive Streams API</h2>
<p>Reactive Streams API는 Reactive 프로그래밍 모델을 구현하기 위한 표준 인터페이스의 모음입니다. 이 API는 비동기적이고 이벤트 기반의 스트림 처리를 위한 표준화된 규격을 제공하여 다양한 라이브러리와 프레임워크 간의 상호 운용성을 보장합니다. Reactive Streams API는 Publisher(발행자), Subscriber(구독자), Subscription(구독) 및 Processor(처리기)라는 네 가지 인터페이스로 구성되어 있습니다.</p>
<ul>
<li>
<p><strong>Publisher</strong>: 데이터를 발행(emit)하는 역할을 합니다. 이는 비동기적으로 데이터를 생성하거나, 외부 소스에서 데이터를 가져오거나, 다른 데이터 소스로부터 데이터를 전달하는 등의 작업을 수행합니다.</p>
</li>
<li>
<p><strong>Subscriber</strong>: Publisher로부터 발행되는 데이터를 구독하는 역할을 합니다. 데이터를 소비하고 처리하며, 데이터를 처리하는데 필요한 작업을 수행합니다.</p>
</li>
<li>
<p><strong>Subscription</strong>: Publisher와 Subscriber 간의 연결을 나타냅니다. Subscriber가 Publisher로부터 데이터를 요청하거나, 데이터를 받을 준비가 되었는지를 관리합니다.</p>
</li>
<li>
<p><strong>Processor</strong>: Publisher와 Subscriber 사이에서 데이터를 중간 처리하는 역할을 합니다. 입력 데이터를 받아들여 가공하고 변환한 뒤, Subscriber에게 전달합니다.</p>
</li>
</ul>
<p>Reactive Streams API는 백 프레셔(back-pressure) 메커니즘을 포함하고 있어, Subscriber가 처리할 수 있는 만큼의 데이터만 Publisher로부터 요청하고 받을 수 있도록 합니다. 이를 통해 데이터 소비자와 생산자 간의 속도 차이를 조절하고 안정적인 데이터 스트림 처리를 가능하게 합니다.</p>
<p>Reactive Streams API는 Java 9 이상부터는 java.util.concurrent.Flow 패키지에 포함되어 있습니다. 이 API를 준수하는 라이브러리 및 프레임워크들은 Reactive 스트림 처리를 표준화하여 상호 운용성을 보장하고, 비동기 및 이벤트 기반의 프로그래밍을 위한 기반을 제공합니다.	Spring WebFlux, Akka Streams, RxJava 등은 Reactive Streams API를 준수하고 있습니다.</p>
<h2 id="백프레셔-메커니즘">백프레셔 메커니즘</h2>
<p>백 프레셔(Back-pressure): 백 프레셔는 데이터를 처리하는 측(소비자 또는 구독자)가 데이터를 받을 수 있는 속도를 제어하는 메커니즘입니다. 발행자(생산자)와 구독자(소비자) 간의 속도 차이를 조절하여, 구독자가 처리할 수 있는 만큼의 데이터만 발행자로부터 요청(request)하고, 발행자는 이에 따라 데이터를 전송하거나 중단함으로써 데이터 전달 속도를 맞춥니다. 이는 데이터 전송의 효율성과 안정성을 유지하는데 도움을 줍니다.</p>
<h2 id="reactive-stream">Reactive Stream</h2>
<p><a href="http://reactive-streams.org/">http://reactive-streams.org/</a></p>
<p><a href="https://engineering.linecorp.com/ko/blog/reactive-streams-with-armeria-1">https://engineering.linecorp.com/ko/blog/reactive-streams-with-armeria-1</a></p>
<p><img src="https://engineering.linecorp.com/wp-content/uploads/2020/02/reactivestreams1-1.png"></p>
<p><img src="https://engineering.linecorp.com/wp-content/uploads/2020/02/reactivestreams1-2.png"></p>
<h2 id="reactive-vs-servelt">Reactive vs Servelt</h2>
<p><img src="https://velog.velcdn.com/images/shinmj1207/post/b5cfd98b-2495-4c11-8e37-54174be7c11a/image.png"></p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>Spring Webflux와 해당 서비스의 WebClient 소비자를 사용하여 RESTful 웹 서비스를 구축합니다. System.out과 다음 위치 모두에서 출력을 볼 수 있습니다.</p>
<pre><code class="language-shell">http://localhost:8080/hello</code></pre>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1edb2844-f2f4-4081-9007-954ffbe1e248/image.png"></p>
<h2 id="create-a-webflux-handler">Create a WebFlux Handler</h2>
<p>RESTful 서비스에 의해 JSON으로 직렬화될 Greeting POJO부터 시작하겠습니다.</p>
<p>src/main/java/hello/Greeting.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Greeting</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> message<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>message <span class="token operator">=</span> message<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>message<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMessage</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>message <span class="token operator">=</span> message<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Greeting{"</span> <span class="token operator">+</span>
                <span class="token string">"message='"</span> <span class="token operator">+</span> message <span class="token operator">+</span> <span class="token string">'\''</span> <span class="token operator">+</span>
                <span class="token string">'}'</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring Reactive 접근 방식에서는 다음 예제와 같이 핸들러를 사용하여 요청을 처리하고 응답을 생성합니다.</p>
<p>src/main/java/hello/GreetingHandler.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">MediaType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function</span><span class="token punctuation">.</span><span class="token class-name">BodyInserters</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">ServerRequest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">ServerResponse</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingHandler</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ServerResponse</span><span class="token punctuation">&gt;</span></span> <span class="token function">hello</span><span class="token punctuation">(</span><span class="token class-name">ServerRequest</span> request<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">ServerResponse</span><span class="token punctuation">.</span><span class="token function">ok</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">contentType</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span><span class="token class-name">BodyInserters</span><span class="token punctuation">.</span><span class="token function">fromValue</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Greeting</span><span class="token punctuation">(</span><span class="token string">"Hello, Spring"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<ol>
<li>
<p><code>@Component</code>:</p>
<ul>
<li>Spring의 컴포넌트 스캔을 통해 스프링 애플리케이션 컨텍스트에 해당 클래스의 인스턴스가 Bean으로 등록되도록 지정하는 어노테이션입니다.</li>
</ul>
</li>
<li>
<p><code>public Mono&lt;ServerResponse&gt; hello(ServerRequest request) { ... }</code>:</p>
<ul>
<li><code>hello</code> 메서드는 <code>ServerRequest</code>를 받아들여 비동기적인 방식으로 요청을 처리하고, <code>Mono&lt;ServerResponse&gt;</code>를 반환합니다.</li>
<li><code>Mono</code>는 Reactor 라이브러리의 Publisher 중 하나로, 0 또는 1개의 결과를 비동기적으로 발생시키는데 사용됩니다.</li>
<li><code>ServerResponse</code>는 Spring WebFlux에서 클라이언트로 응답을 보내는 데 사용되는 클래스입니다.</li>
</ul>
</li>
<li>
<p><code>return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(BodyInserters.fromValue(new Greeting("Hello, Spring")));</code>:</p>
<ul>
<li><code>ServerResponse.ok()</code>는 HTTP 응답 코드 200(OK)를 생성합니다.</li>
<li><code>contentType(MediaType.APPLICATION_JSON)</code>은 HTTP 응답의 Content-Type을 JSON 형식으로 지정합니다.</li>
<li><code>body(BodyInserters.fromValue(new Greeting("Hello, Spring")))</code>는 응답의 본문에 들어갈 내용을 지정합니다. 이 경우에는 <code>Greeting</code> 객체를 JSON 형식으로 변환하여 응답에 포함시킵니다.</li>
</ul>
</li>
<li>
<p><code>new Greeting("Hello, Spring")</code>:</p>
<ul>
<li><code>Greeting</code> 객체를 생성하는데, 이 객체는 <code>Hello, Spring</code>이라는 메시지를 가지고 있습니다. 이것이 JSON으로 변환되어 클라이언트에 응답으로 전송됩니다.</li>
</ul>
</li>
</ol>
<hr>
<p>이 간단한 반응형 클래스는 항상 "Hello, Spring!"과 함께 JSON 본문을 반환합니다. 데이터베이스의 항목(item) 스트림, 계산으로 생성된 항목 스트림 등을 포함하여 다른 많은 항목을 반환할 수 있습니다. 반응 코드(<code>ServerResponse</code> 본문(body)을 보유하는 <code>Mono</code> 개체)에 주목하세요.</p>
<h2 id="라우터-생성">라우터 생성</h2>
<p>이 애플리케이션에서는 다음 예제와 같이 라우터를 사용하여 노출된 유일한 경로(<code>/hello</code>)를 처리합니다.</p>
<p>src/main/java/hello/GreetingRouter.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">MediaType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">RouterFunction</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">RouterFunctions</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">ServerResponse</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">RequestPredicates</span><span class="token punctuation">.</span>GET<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">RequestPredicates</span><span class="token punctuation">.</span>accept<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span><span class="token punctuation">(</span>proxyBeanMethods <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingRouter</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RouterFunction</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ServerResponse</span><span class="token punctuation">&gt;</span></span> <span class="token function">route</span><span class="token punctuation">(</span><span class="token class-name">GreetingHandler</span> greetingHandler<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        <span class="token keyword">return</span> <span class="token class-name">RouterFunctions</span><span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span><span class="token function">GET</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">and</span><span class="token punctuation">(</span><span class="token function">accept</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">,</span> greetingHandler<span class="token operator">:</span><span class="token operator">:</span><span class="token function">hello</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>라우터는 <code>/hello</code> 경로에서 트래픽을 수신하고 반응 핸들러 클래스에서 제공한 값을 반환합니다.</p>
<hr>
<p><code>@Configuration(proxyBeanMethods = false)</code>는 스프링의 구성 클래스를 나타내는 애노테이션입니다. 여기서 <code>proxyBeanMethods</code>는 설정된 메서드에 대한 프록시 생성 여부를 지정합니다.</p>
<p>기본적으로 <code>proxyBeanMethods</code>는 <code>true</code>로 설정되어 있습니다. 이는 해당 구성 클래스에서 <code>@Bean</code>으로 주석이 달린 메서드를 호출할 때마다 새로운 빈 인스턴스를 만들지 않고, 스프링이 내부적으로 캐싱하여 같은 빈을 반환하도록 합니다. 이것은 보다 효율적인 프록시 생성 방법이며, 메서드 호출이 해당 구성 클래스 내에서 프록시를 통해 처리됩니다.</p>
<p>그러나 <code>proxyBeanMethods = false</code>로 설정하면 이 프록시 기능을 사용하지 않고, 매번 호출할 때마다 새로운 빈 인스턴스를 생성하게 됩니다. 이렇게 하면 캐싱의 이점이 사라지지만, 일부 특정한 상황에서 유용할 수 있습니다. </p>
<p>주로 <code>proxyBeanMethods</code>를 <code>false</code>로 설정하는 경우는 해당 구성 클래스에 싱글톤이 아닌 빈이 존재하거나, 외부에서 변경될 수 있는 설정이 있을 때입니다. 이 경우 매번 호출할 때 새로운 빈 인스턴스를 생성하여 예기치 않은 동작을 방지할 수 있습니다.</p>
<hr>
<p>리액티브 웹에서도 대부분의 경우에는 싱글톤으로 라우터를 사용하는 것이 일반적입니다. 이는 메모리 효율성과 성능 측면에서 이점을 가져다 줄 수 있습니다.</p>
<p>그러나 특정한 상황에서는 라우터를 싱글톤이 아닌 다른 스코프로 설정하는 것이 필요할 수 있습니다. 몇 가지 상황으로는:</p>
<ol>
<li>
<p><strong>상태를 가진 라우터</strong>: 라우터가 상태를 가지고 있어야 하며, 각 요청마다 다른 상태를 유지해야 하는 경우가 있습니다. 이 경우에는 요청마다 새로운 라우터 인스턴스를 생성하여 각각의 상태를 유지할 수 있습니다.</p>
</li>
<li>
<p><strong>동적으로 라우팅을 결정하는 경우</strong>: 요청이 들어올 때마다 동적으로 라우팅을 결정해야 하고, 각 요청에 맞게 다른 동작을 하는 경우에는 매 요청마다 새로운 라우터 인스턴스를 생성하여 동적으로 라우팅할 수 있습니다.</p>
</li>
</ol>
<p>이러한 경우를 제외하고는 대부분의 상황에서는 싱글톤 라우터를 사용하는 것이 권장됩니다. 싱글톤으로 관리되는 라우터는 애플리케이션 전체에서 공유되며, 메모리 사용량을 줄이고 성능을 향상시킬 수 있습니다. 하지만 특정한 요구사항이나 상황에 따라 다른 스코프의 라우터를 사용하는 것도 가능합니다.</p>
<hr>
<h2 id="웹클라이언트-생성">웹클라이언트 생성</h2>
<p>Spring <code>RestTemplate</code> 클래스는 본질적으로 차단됩니다(blocking 입니다). 결과적으로 우리는 이를 반응형 애플리케이션에서 사용하고 싶지 않습니다. 반응형 애플리케이션의 경우 Spring은 비차단형(non blocking) <code>WebClient</code> 클래스를 제공합니다. 우리는 RESTful 서비스를 사용하기 위해 WebClient 기반 구현을 사용합니다.</p>
<p>src/main/java/hello/GreetingClient.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">MediaType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">WebClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingClient</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">WebClient</span> client<span class="token punctuation">;</span>

    <span class="token comment">//Spring Boot auto-configures a `WebClient.Builder` instance with nice default and customizations.</span>
    <span class="token comment">//We can use it to create a dedicated `WebClient` for our component</span>

    <span class="token keyword">public</span> <span class="token class-name">GreetingClient</span><span class="token punctuation">(</span><span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token class-name">Builder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>client <span class="token operator">=</span> builder<span class="token punctuation">.</span><span class="token function">baseUrl</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>client<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">accept</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">bodyToMono</span><span class="token punctuation">(</span><span class="token class-name">Greeting</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span><span class="token class-name">Greeting</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">getMessage</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>WebClient</code> 클래스는 <code>Mono</code> 형식의 반응 기능을 사용하여 메시지 내용을 보관합니다(<code>getMessage</code> 메서드에서 반환됨). 이는 필수(imperative) API가 아닌 함수 API를 사용하여 반응 연산자를 연결하는 것입니다.</p>
<hr>
<p>Imperative API란 프로그래밍에서 명령형 프로그래밍 스타일을 가리킵니다. 이 스타일은 컴퓨터에게 명령을 내리는 방식으로 코드를 작성하는 것입니다. 명령형 프로그래밍은 컴퓨터가 데이터를 어떻게 처리할지 단계적으로 명시합니다.</p>
<p>Imperative API에서는 코드가 명시적으로 단계별로 실행되며, 코드 블록 안에서 명령문이 순차적으로 실행됩니다. 이러한 스타일의 코드는 작업을 수행하는 방법과 세부 동작을 명확하게 나열하고 제어합니다.</p>
<p>반면에 함수형 API 또는 리액티브 API는 데이터를 처리하는 방식을 명령하는 대신 데이터의 흐름과 변환을 설명합니다. 함수형 프로그래밍은 선언적(declarative)인데, 이는 코드가 무엇을 하는지를 명시하고, 어떻게 하는지에 대해 구체적으로 명령하지 않습니다.</p>
<p>Reactive API는 비동기적이고 데이터의 흐름에 초점을 맞춥니다. <code>Mono</code>나 <code>Flux</code>와 같은 반응형 유형은 비동기적으로 데이터를 처리하고 데이터의 스트림을 조작할 수 있도록 해줍니다.</p>
<p>따라서 <code>WebClient</code>를 사용할 때 <code>Mono</code>를 반환하는 메서드를 사용한다면, 그것은 리액티브 또는 함수형 API를 사용하고 있다는 의미입니다. 이러한 API 스타일은 데이터의 흐름과 변환에 중점을 두고, 작업을 어떻게 해야 하는지보다 무엇을 해야 하는지에 집중합니다.</p>
<hr>
<p>Reactive API에 익숙해지는 데는 시간이 걸릴 수 있지만 <code>WebClient</code>에는 흥미로운 기능이 있으며 기존 Spring MVC 애플리케이션에서도 사용할 수 있습니다.</p>
<blockquote>
<p><code>WebClient</code>를 사용하여 비반응형 차단(blocking) 서비스와도 통신할 수 있습니다.</p>
</blockquote>
<h2 id="애플리케이션을-실행-가능하게-만들기">애플리케이션을 실행 가능하게 만들기</h2>
<p>우리는 애플리케이션을 구동하고 엔드포인트로부터 Greeting 메시지를 받기 위해 <code>main()</code> 메소드를 사용할 것입니다.</p>
<p>src/main/java/hello/Application.java</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ReactiveWebServiceApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">ConfigurableApplicationContext</span> context <span class="token operator">=</span>
                <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ReactiveWebServiceApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">GreetingClient</span> greetingClient <span class="token operator">=</span> context<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">GreetingClient</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// We need to block for the content here or the JVM might exit before the message is logged</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"&gt;&gt; message = "</span> <span class="token operator">+</span> greetingClient<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">block</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p><code>GreetingHandler</code>, <code>GreetingRouter</code>, <code>GreetingClient</code> 까지만 생성했을 때, <code>GreetingRouter</code>의 <code>route</code> 함수가 인수로 사용하는 <code>greetingHandler</code>는 autowire되지 못한 상황이어서 IDE 상에서는 빨간 줄 표시가 되어 있을 것이다. </p>
<p>그런데 <code>ReactiveWebServiceApplication</code> 코드를 생성하면 그 빨간 줄이 사라진다. 이는 스프링 애플리케이션이 실행될 때, <code>GreetingHandler</code>가 컴포넌트 스캔에 의해 빈으로 등록되었기 때문이다.</p>
<p><code>@Component</code> 어노테이션이 있는 <code>GreetingHandler</code> 클래스는 스프링 컨텍스트에서 빈으로 등록됩니다. 그러나 <code>GreetingRouter</code> 클래스에서 <code>GreetingHandler</code>를 주입받으려 할 때, <code>GreetingHandler</code>가 빈으로 등록되지 않았다면 (<code>@Component</code>를 사용하여 스프링이 인식하지 못했다면) 해당 클래스를 주입할 수 없다는 오류가 발생합니다.</p>
<p><code>ReactiveWebServiceApplication</code> 클래스는 스프링 부트 애플리케이션의 진입점입니다. 여기서 <code>SpringApplication.run()</code>을 호출하여 스프링 애플리케이션이 시작됩니다. 이 때, <code>@SpringBootApplication</code> 어노테이션이 붙은 클래스를 시작으로 스프링이 컴포넌트 스캔을 수행하고 <code>@Component</code>로 표시된 클래스를 스프링 컨테이너에 빈으로 등록합니다.</p>
<hr>
<h3 id="상호작용-요약">상호작용 요약</h3>
<ol>
<li><strong>Greeting</strong><ul>
<li><code>Greeting</code> 클래스는 간단한 메시지를 저장하고 반환하는 역할을 합니다.</li>
</ul></li>
<li><strong>GreetingHandler</strong><ul>
<li><code>GreetingHandler</code>는 요청을 처리하는데, <code>/hello</code> 엔드포인트에 대한 요청을 처리합니다.</li>
</ul></li>
<li><strong>GreetingRouter</strong><ul>
<li><code>GreetingRouter</code>는 <code>GreetingHandler</code>를 사용하여 경로를 정의합니다. <code>/hello</code> 엔드포인트에 대한 요청을 <code>GreetingHandler</code>의 <code>hello()</code> 메서드로 라우팅합니다.</li>
</ul></li>
<li><strong>ReactiveWebServiceApplication</strong><ul>
<li><code>ReactiveWebServiceApplication</code>은 애플리케이션의 진입점입니다. 여기서 <code>SpringApplication.run()</code>을 호출하여 애플리케이션을 시작합니다. </li>
<li><code>GreetingClient</code> 인스턴스를 가져와서 <code>getMessage()</code> 메서드를 호출하여 <code>/hello</code> 엔드포인트에 대한 요청을 수행합니다. (<code>greetingClient.getMessage().block()</code>를 통해 결과를 블로킹 방식으로 가져옵니다.)</li>
</ul></li>
<li><strong>GreetingClient</strong><ul>
<li><code>GreetingClient</code>는 <code>WebClient</code>를 사용하여 외부 리소스에 대한 비동기 HTTP 요청을 처리합니다.</li>
<li><code>getMessage()</code> 메서드는 <code>WebClient</code>를 이용해 <code>/hello</code> 엔드포인트로 GET 요청을 보내고, 반환되는 응답을 처리합니다. 이때 JSON 데이터를 <code>Greeting</code> 객체로 변환하고 그 안의 메시지를 추출하여 반환합니다.</li>
</ul></li>
</ol>
<p>이 코드들의 상호작용은 다음과 같습니다:<br>
<code>ReactiveWebServiceApplication</code>이 실행되면, <code>GreetingClient</code>를 통해 <code>/hello</code> 엔드포인트로 GET 요청을 보내고, 이는 <code>GreetingHandler</code>로 라우팅됩니다. <code>GreetingHandler</code>는 새로운 <code>Greeting</code> 객체를 생성하여 응답으로 반환합니다. 그 결과, <code>ReactiveWebServiceApplication</code>은 이 응답을 받아와서 콘솔에 출력합니다.</p>
<hr>
<h3 id="블로킹-호출-시-제어권">블로킹 호출 시 제어권</h3>
<p><code>greetingClient.getMessage().block()</code>를 호출하는 순간에는 현재 실행 중인 스레드가 해당 메서드에서 반환되는 결과를 기다리면서 블로킹됩니다. 이때 제어권은 현재 스레드에 있습니다. 호출된 메서드가 작업을 완료하고 값을 반환할 때까지 아무것도 진행되지 않습니다. 따라서 이 라인의 코드가 실행되는 동안에는 프로그램의 흐름이 멈추고 제어권은 그 부분에서 계속 유지됩니다.</p>
<p>여기서 <code>block()</code> 메서드는 Reactor의 Mono나 Flux에서 사용되며, 비동기 작업을 동기적으로 처리하는데 사용됩니다. 이것은 일반적으로 리액티브 프로그래밍의 아이디어에 반하는 것이지만, 특정한 상황에서 블로킹 호출이 필요한 경우도 있습니다. 그러나 이는 전체 리액티브 시스템의 효율성을 떨어뜨릴 수 있으니 주의가 필요합니다.</p>
<hr>
<h3 id="block을-사용하지-않았다면">.block()을 사용하지 않았다면</h3>
<p>만약 <code>.block()</code>을 사용하지 않았다면, 리액티브 방식에 따라 비동기로 동작할 것입니다. </p>
<p><code>greetingClient.getMessage()</code>는 Mono 또는 Flux와 같은 Reactor 타입을 반환하며, 이는 Reactive Streams 스펙을 따릅니다. 이 방식에서는 비동기로 요청을 처리하고, 결과를 처리하는 함수를 등록하거나 연결하여 작업이 완료되면 이벤트를 처리합니다.</p>
<p>따라서 <code>.block()</code>을 사용하지 않으면, 메서드가 Mono를 반환하기 때문에 해당 결과를 처리하기 위해 Reactor의 다른 메서드(예: <code>subscribe()</code>, <code>flatMap()</code>, <code>map()</code> 등)를 사용하여 비동기적으로 결과를 처리할 수 있습니다. 이렇게 하면 블로킹하지 않고도 비동기로 작업을 처리할 수 있게 됩니다. </p>
<p>쉽게 말해, <code>.block()</code>을 사용하지 않으면 비동기적인 방식으로 결과를 처리하며, 결과가 나올 때까지 기다리지 않고도 프로그램이 계속 실행됩니다. 대신에 콜백 형태나 리액티브한 방식으로 결과를 받아 처리할 수 있게 됩니다.</p>
<hr>
<h3 id="subscribe를-사용한-비동기">.subscribe()를 사용한 비동기</h3>
<p>(1)</p>
<pre><code class="language-java">
<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"&gt;&gt; message = "</span> <span class="token operator">+</span> greetingClient<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 

<span class="token comment">// 출력 내용</span>
<span class="token comment">// &gt;&gt; message = reactor.core.publisher.LambdaMonoSubscriber@1624775 </span>
<span class="token comment">// localhost:8080/hello 브라우저 표시 내용</span>
<span class="token comment">// {"message":"Hello, Spring!"}</span>
</code></pre>
<p>(2)</p>
<pre><code class="language-java"><span class="token class-name">Instant</span> start <span class="token operator">=</span> <span class="token class-name">Instant</span><span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

greetingClient<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span>
    result <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            	<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"&gt;&gt; message = "</span><span class="token operator">+</span>result<span class="token punctuation">)</span><span class="token punctuation">;</span>
            	<span class="token class-name">Instant</span> end <span class="token operator">=</span> <span class="token class-name">Instant</span><span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            	<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Process completed at "</span> <span class="token operator">+</span> end<span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Subscribed at : "</span> <span class="token operator">+</span> start<span class="token punctuation">)</span><span class="token punctuation">;</span>


<span class="token comment">// 출력 내용</span>
<span class="token comment">// Subscribed at : 2023-12-26T06:42:26.031612242Z</span>
<span class="token comment">// &gt;&gt; message = Hello, Spring!</span>
<span class="token comment">// Process completed at 2023-12-26T06:42:26.329008808Z</span></code></pre>
<p><code>.subscribe()</code> 메서드는 Mono나 Flux와 같은 리액티브 스트림을 구독하는 메서드입니다. 이 메서드는 비동기적으로 결과를 처리하기 위해 사용되며, 실제로 반환하는 것은 <code>Subscription</code> 객체입니다.</p>
<p>(1) 따라서 <code>System.out.println("&gt;&gt; message = " + greetingClient.getMessage().subscribe());</code> 라인은 구독을 시작하고 구독된 <code>Mono</code>를 반환하는데, 그것이 <code>LambdaMonoSubscriber</code> 객체입니다. 이것은 구독이 발생했지만 실제로 Mono의 결과를 처리하거나 출력하는 것이 아니기 때문에 예상한 결과를 출력하지 않습니다.</p>
<p>리액티브 프로그래밍에서 비동기 작업을 수행하고 결과를 처리하기 위해서는 <code>subscribe()</code> 메서드에 대한 콜백을 등록해야 합니다. 이 콜백을 통해 결과가 나왔을 때 실행되는 로직을 구현할 수 있습니다.</p>
<p>(2) 예를 들어, 올바르게 결과를 처리하려면 다음과 같이 코드를 작성해야 합니다:</p>
<pre><code class="language-java">greetingClient<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span>result <span class="token operator">-&gt;</span> <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"&gt;&gt; message = "</span> <span class="token operator">+</span> result<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>여기서 <code>result -&gt; System.out.println("&gt;&gt; message = " + result)</code>는 결과가 나왔을 때 실행되는 콜백 함수입니다. <code>result</code>는 Mono에서 반환된 값이 됩니다. 이렇게 하면 실제로 Mono의 결과를 처리하고 콘솔에 원하는 메시지가 표시될 것입니다.</p>
<p>비동기적으로 실행되어 <code>start</code> 실행 시각의 출력문이 가장 아래에 있음에도 불구하고 먼저 콘솔에 출력되는 것을 확인할 수 있습니다.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/492e732d-1227-4b9a-b358-a3306824a081/image.png"></p>
<p>서비스가 시작되면 다음과 같은 줄을 볼 수 있습니다.</p>
<pre><code class="language-null">&gt;&gt; message = Hello, Spring!</code></pre>
<p>해당 줄은 WebClient가 사용하는(consume) 반응형 콘텐츠에서 나옵니다. 당연히(Naturally) 출력을 System.out에 넣는 것보다 출력과 관련하여 더 흥미로운 것을 찾을 수 있습니다.</p>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>이제 애플리케이션이 실행되었으므로 테스트할 수 있습니다. 우선 브라우저를 열고 <code>http://localhost:8080/hello</code>로 이동하여 "Hello, Spring!"을 확인하세요. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7ffc6f1c-5ce2-45b8-9742-6bca1eaa0e0c/image.png"></p>
<p>이 가이드에서는 WebTestClient 클래스를 사용한 테스트를 시작할 수 있도록 테스트 클래스도 만들었습니다.</p>
<p><code>src/test/java/hello/GreetingRouterTest.java</code></p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">SpringExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token comment">// We create a `@SpringBootTest`, starting an actual server on a `RANDOM_PORT`</span>
<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span>webEnvironment <span class="token operator">=</span> <span class="token class-name">SpringBootTest</span><span class="token punctuation">.</span><span class="token class-name">WebEnvironment</span><span class="token punctuation">.</span>RANDOM_PORT<span class="token punctuation">)</span>
<span class="token keyword">class</span> <span class="token class-name">GreetingRouterTest</span> <span class="token punctuation">{</span>

    <span class="token comment">// Spring Boot will create a `WebTestClient` for you,</span>
    <span class="token comment">// already configure and ready to issue requests against "localhost:RANDOM_PORT"</span>
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">WebTestClient</span> webTestClient<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">testHello</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

        webTestClient
                <span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span>
                <span class="token comment">// Create a GET request to test an endpoint</span>
                <span class="token punctuation">.</span><span class="token function">accept</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">exchange</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token comment">// and use the dedicated DSL to test assertions against the response</span>
                <span class="token punctuation">.</span><span class="token function">expectStatus</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">expectBody</span><span class="token punctuation">(</span><span class="token class-name">Greeting</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span>greeting <span class="token operator">-&gt;</span> <span class="token punctuation">{</span><span class="token function">assertThat</span><span class="token punctuation">(</span>greeting<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Hello, Spring!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<hr>
<p> DSL"이란 "Domain Specific Language"의 약어로, 특정 도메인(예: 웹 테스트)에 대한 언어를 의미합니다. 여기서 말하는 "dedicated DSL"은 WebTestClient의 메서드 체인이라고 볼 수 있습니다. WebTestClient는 Spring WebFlux 애플리케이션을 테스트하기 위한 테스트 클라이언트입니다.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ba89f208-b6e3-453b-bb3b-00c85cb5ef80/image.png"></p>