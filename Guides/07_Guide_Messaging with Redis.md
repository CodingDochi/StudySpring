<p>이 가이드는 Spring Data Redis를 사용하여 Redis로 전송된 메시지를 게시하고 구독하는 프로세스를 안내합니다.</p>
<p><img src="https://substackcdn.com/image/fetch/w_1272,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2Fd9fe2432-5422-4299-9afe-5d9d6ab4f55a_2913x4653.jpeg"></p>
<h2 id="메시징-모델의-유형">메시징 모델의 유형</h2>
<h3 id="주요-용어">주요 용어</h3>
<ul>
<li><strong>Receiver(수신자), Listener(리스너), Consumer(소비자), Subscriber(구독자)</strong> :모두 메시징 시스템에서 메시지를 수신하고 처리하는 역할을 합니다.</li>
<li><strong>Sender(송신자), Producer(생산자), Publisher(발행자)</strong> : 모두 메시징 시스템에서 메시지를 생성하고 보내는 역할을 합니다.</li>
<li>메시지(Message): 데이터 덩어리로, 전달하고자 하는 정보를 포함합니다.</li>
</ul>
<h3 id="pubsub">Pub/Sub</h3>
<ul>
<li>주제(Topic): Pub/Sub 모델에서 메시지를 구분하는 데 사용되는 명명된 채널이나 주제입니다.</li>
<li>메시지 브로커(Message Broker): Pub/Sub 모델에서 발행자와 구독자 간의 중간 매개체로, 메시지를 중개하고 보관하는 시스템입니다.</li>
<li>Pub/Sub은 여러 구독자가 메시지를 동시에 수신할 수 있음</li>
<li>Pub/Sub은 이벤트 기반 시스템에서 활발하게 사용</li>
</ul>
<h3 id="p2p">P2P</h3>
<ul>
<li>큐(Queue): P2P 모델에서 메시지가 대기하는 FIFO(First-In-First-Out) 구조의 저장 공간입니다.</li>
<li>메시지 큐 서버(Message Queue Server): P2P 모델에서 메시지를 보내고 받는 데 사용되는 서버입니다.</li>
<li>P2P는 메시지를 처리할 수신자가 반드시 존재해야 하며, 한 번에 하나의 수신자만 메시지를 처리</li>
<li>P2P는 메시지 큐 시스템에서 대기열 처리에 적합</li>
</ul>
<h2 id="이벤트-수신-객체의-종류">이벤트 수신 객체의 종류</h2>





















<table><thead><tr><th>개념</th><th>형식</th></tr></thead><tbody><tr><td>Receiver</td><td>POJO</td></tr><tr><td>Listener</td><td>인터페이스 구현체</td></tr><tr><td>Subscriber</td><td>인터페이스 구현체(ActionEvent 타입 매개변수)</td></tr></tbody></table>
<p><strong>Receiver</strong></p>
<ul>
<li>이벤트를 수신할 수 있습니다.</li>
<li>이벤트 발생 시 특정 작업을 수행합니다.</li>
</ul>
<pre><code class="language-java"><span class="token comment">// Receiver 예시</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ButtonReceiver</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onButtonClick</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 이벤트 발생 시 수행할 작업</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><strong>Listener</strong></p>
<ul>
<li>이벤트를 수신할 수 있습니다.</li>
<li>이벤트 발생 시 호출될 메서드를 등록할 수 있습니다.</li>
<li>이벤트 발생에 대한 응답을 제공할 수 있습니다.</li>
</ul>
<pre><code class="language-java"><span class="token comment">// Listener 예시</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ButtonListener</span> <span class="token keyword">implements</span> <span class="token class-name">ActionListener</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">actionPerformed</span><span class="token punctuation">(</span><span class="token class-name">ActionEvent</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 이벤트 발생 시 수행할 작업</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><strong>Subscriber</strong></p>
<ul>
<li>이벤트를 구독할 수 있습니다.</li>
<li>이벤트 발생 시 특정 작업을 수행합니다.</li>
</ul>
<pre><code class="language-java"><span class="token comment">// Subscriber 예시</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ButtonSubscriber</span> <span class="token keyword">implements</span> <span class="token class-name">Subscriber</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">ActionEvent</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onNext</span><span class="token punctuation">(</span><span class="token class-name">ActionEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 이벤트 발생 시 수행할 작업</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>PoJO (Plain Old Java Object)<br>
특정 프레임워크나 라이브러리에 종속되지 않은 단순한 자바 객체를 의미합니다. POJO는 다음과 같은 특징을 가지고 있습니다.</p>
<ul>
<li>인터페이스나 추상 클래스를 상속하지 않습니다.</li>
<li>특정 프레임워크나 라이브러리에서 제공하는 특수한 메서드나 속성을 사용하지 않습니다.</li>
<li>기본 자바 클래스와 같은 방식으로 생성, 사용, 수정할 수 있습니다.</li>
</ul>
</blockquote>
<h2 id="what-you-will-build">What You Will Build</h2>
<p><code>StringRedisTemplate</code>을 사용하여 문자열 메시지를 게시하고 <code>MessageListenerAdapter</code>를 사용하여 POJO가 메시지를 구독하도록 하는 애플리케이션을 빌드합니다.</p>
<blockquote>
<p>메시지를 게시하는 수단으로 Spring Data Redis를 사용하는 것이 이상하게 들릴 수도 있지만, Redis는 NoSQL 데이터 저장소뿐만 아니라 메시징 시스템도 제공합니다.</p>
</blockquote>
<h2 id="standing-up-a-redis-server">Standing up a Redis Server</h2>
<p>메시징 애플리케이션을 구축하기 전에 메시지 수신 및 전송을 처리할 서버를 설정해야 합니다.</p>
<p>Redis는 메시징 시스템과 함께 제공되는 오픈 소스 BSD 라이선스 키-값 데이터 저장소입니다. 서버는 <a href="https://redis.io/download%EC%97%90%EC%84%9C">https://redis.io/download에서</a> 무료로 사용할 수 있습니다. 수동으로 다운로드하거나 Mac을 사용하는 경우 Homebrew를 사용하여 터미널 창에서 다음 명령을 실행하여 다운로드할 수 있습니다.</p>
<p><a href="https://redis.io/docs/install/install-redis/install-redis-on-linux/">https://redis.io/docs/install/install-redis/install-redis-on-linux/</a><br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/cdec3cbf-001e-4254-9489-9a3d02bf5562/image.png"></p>
<p><strong>prerequisites</strong><br>
If you're running a very minimal distribution (such as a Docker container) you may need to install lsb-release, curl and gpg first:</p>
<pre><code class="language-shell"><span class="token function">sudo</span> <span class="token function">apt</span> <span class="token function">install</span> lsb-release <span class="token function">curl</span> gpg</code></pre>
<p><strong>install on ubuntu</strong></p>
<pre><code class="language-shell"><span class="token function">curl</span> -fsSL https://packages.redis.io/gpg <span class="token operator">|</span> <span class="token function">sudo</span> gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

<span class="token builtin class-name">echo</span> <span class="token string">"deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb <span class="token variable"><span class="token variable">$(</span>lsb_release -cs<span class="token variable">)</span></span> main"</span> <span class="token operator">|</span> <span class="token function">sudo</span> <span class="token function">tee</span> /etc/apt/sources.list.d/redis.list

<span class="token function">sudo</span> <span class="token function">apt-get</span> update
<span class="token function">sudo</span> <span class="token function">apt-get</span> <span class="token function">install</span> redis</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/242a1a48-b700-44f9-8ca9-309c93fb013f/image.png"></p>
<pre><code class="language-shell">redis-server</code></pre>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/41d98913-b593-4f29-950a-c113cc2b9622/image.png"></p>
<h2 id="create-a-redis-message-receiver">Create a Redis Message Receiver</h2>
<p>모든 메시징 기반 애플리케이션에는 메시지 게시자(Publisher)와 메시징 수신자(Reciever)가 있습니다. 메시지 수신기를 생성하려면 다음 예제(src/main/java/guides/messagingredis/Receiver.java)에 표시된 대로 메시지에 응답하는 메서드를 사용하여 수신기를 구현합니다.</p>
<blockquote>
<p>데모(demonstration) 목적으로 수신자(receiver)는 수신된 메시지 수를 계산(count)합니다. 이렇게 하면 메시지를 받았을 때 신호를 보낼 수 있습니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingredis</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>concurrent<span class="token punctuation">.</span>atomic</span><span class="token punctuation">.</span><span class="token class-name">AtomicInteger</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Receiver</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> LOGGER <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">AtomicInteger</span> counter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AtomicInteger</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">receiveMessage</span><span class="token punctuation">(</span><span class="token class-name">String</span> message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        LOGGER<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Received &lt;"</span> <span class="token operator">+</span> message <span class="token operator">+</span> <span class="token string">"&gt;"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        counter<span class="token punctuation">.</span><span class="token function">incrementAndGet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> counter<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Receiver</code>는 메시지 수신 방법을 정의하는 POJO입니다. <code>Reciever</code>를 메시지 <code>Listner</code>로 등록할 때 메시지 처리 방법의 이름을 원하는 대로 지정할 수 있습니다.</p>
<ul>
<li><code>LoggerFactory.getLogger(클래스.class)</code>가 현재 클래스의 로거 인스턴스를 생성한다. 로거 인스턴스는 파일, 콘솔, 네트워크 등을 대상으로 추적하고 오류를 진단하는 역할을 한다. </li>
</ul>
<h2 id="register-the-listener-and-send-a-message">Register the Listener and Send a Message</h2>
<p>Spring Data Redis는 Redis로 메시지를 보내고 받는 데 필요한 모든 구성 요소를 제공합니다. 특히 다음을 구성해야 합니다.</p>
<ol>
<li>
<p>연결 팩토리 (Connection Factory) : 템플릿과 메시지 리스너 컨테이너를 모두 구동하여 Redis 서버에 연결</p>
</li>
<li>
<p>메시지 리스너 컨테이너 (Message Listener Container)</p>
</li>
</ol>
<ul>
<li>메시지를 수신할 수 있도록 메시지 리스너 컨테이너에 <code>Receiver</code>를 등록</li>
</ul>
<ol start="3">
<li>Redis 템플릿 (Redis Template) : 템플릿을 사용하여 메시지를 송신</li>
</ol>
<p>이 예제에서는 Jedis Redis 라이브러리를 기반으로 하는 <code>JedisConnectionFactory</code>의 인스턴스인 Spring Boot의 기본 <code>RedisConnectionFactory</code>를 사용합니다. 다음 예제(src/main/java/guides/messagingredis/MessagingRedisApplication.java)에 표시된 것처럼 연결 팩토리는 메시지 리스너 컨테이너와 Redis 템플릿 모두에 주입됩니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>messagingredis</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>connection</span><span class="token punctuation">.</span><span class="token class-name">RedisConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">StringRedisTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>listener</span><span class="token punctuation">.</span><span class="token class-name">PatternTopic</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>listener</span><span class="token punctuation">.</span><span class="token class-name">RedisMessageListenerContainer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>listener<span class="token punctuation">.</span>adapter</span><span class="token punctuation">.</span><span class="token class-name">MessageListenerAdapter</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MessagingRedisApplication</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> LOGGER <span class="token operator">=</span>
            <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">MessagingRedisApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">RedisMessageListenerContainer</span> <span class="token function">container</span><span class="token punctuation">(</span><span class="token class-name">RedisConnectionFactory</span> connectionFactory<span class="token punctuation">,</span> <span class="token class-name">MessageListenerAdapter</span> listenerAdapter<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">RedisMessageListenerContainer</span> container <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">RedisMessageListenerContainer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        container<span class="token punctuation">.</span><span class="token function">setConnectionFactory</span><span class="token punctuation">(</span>connectionFactory<span class="token punctuation">)</span><span class="token punctuation">;</span>
        container<span class="token punctuation">.</span><span class="token function">addMessageListener</span><span class="token punctuation">(</span>listenerAdapter<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">PatternTopic</span><span class="token punctuation">(</span><span class="token string">"chat"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">return</span> container<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">MessageListenerAdapter</span> <span class="token function">listenerAdapter</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span> receiver<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MessageListenerAdapter</span><span class="token punctuation">(</span>receiver<span class="token punctuation">,</span> <span class="token string">"receiveMessage"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">Receiver</span> <span class="token function">receiver</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Receiver</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token class-name">StringRedisTemplate</span> <span class="token function">template</span><span class="token punctuation">(</span><span class="token class-name">RedisConnectionFactory</span> connectionFactory<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">StringRedisTemplate</span><span class="token punctuation">(</span>connectionFactory<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">InterruptedException</span> <span class="token punctuation">{</span>
        <span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">MessagingRedisApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">StringRedisTemplate</span> template <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">StringRedisTemplate</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">Receiver</span> receiver <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">while</span> <span class="token punctuation">(</span>receiver<span class="token punctuation">.</span><span class="token function">getCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            LOGGER<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Sending message..."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            template<span class="token punctuation">.</span><span class="token function">convertAndSend</span><span class="token punctuation">(</span><span class="token string">"chat"</span><span class="token punctuation">,</span> <span class="token string">"Hello from Redis!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span><span class="token number">500L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token class-name">System</span><span class="token punctuation">.</span><span class="token function">exit</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 코드들은 Redis를 통해 메시지를 주고받는 과정을 담고 있어요. 여러 부분이 상호작용하며 메시지를 생성, 송수신하고 처리하는 구조입니다. 이들의 상호작용은 다음과 같습니다:</p>
<ol>
<li>
<p><strong>Spring Boot Application 시작</strong></p>
<ul>
<li><code>main()</code> 메서드 실행</li>
<li><code>SpringApplication.run(MessagingRedisApplication.class, args)</code> 호출</li>
<li>Spring 컨텍스트가 생성되고, <code>ApplicationContext</code>가 반환됨</li>
</ul>
</li>
<li>
<p><strong>Spring 컨텍스트 설정</strong></p>
<ul>
<li><code>@Bean</code> 어노테이션을 사용하여 빈을 설정하고 등록</li>
<li><code>container()</code>, <code>listenerAdapter()</code>, <code>receiver()</code>, <code>template()</code> 메서드가 실행되어 각각의 빈이 생성되고 컨테이너에 등록됨</li>
</ul>
</li>
<li>
<p><strong>RedisMessageListenerContainer 설정</strong></p>
<ul>
<li><code>container()</code> 메서드가 호출되어 <code>RedisMessageListenerContainer</code> 빈이 생성됨</li>
<li><code>setConnectionFactory()</code>로 Redis 연결 설정</li>
<li><code>addMessageListener()</code>로 <code>listenerAdapter</code>를 등록하고, "chat" 토픽을 구독하여 해당 토픽의 메시지를 처리할 준비를 함</li>
</ul>
</li>
<li>
<p><strong>메시지 전송 및 수신</strong></p>
<ul>
<li><code>StringRedisTemplate</code>을 이용해 <code>convertAndSend()</code>가 호출되어 "chat" 채널로 메시지 전송</li>
<li>Redis에 "Hello from Redis!" 메시지가 전송됨</li>
<li><code>RedisMessageListenerContainer</code>는 등록된 리스너로부터 "chat" 토픽에 도착하는 메시지를 감지하고, <code>MessageListenerAdapter</code>를 통해 수신된 메시지를 <code>Receiver</code> 클래스의 <code>receiveMessage()</code> 메서드로 라우팅함</li>
</ul>
</li>
</ol>
<p>이런 과정을 통해 메시지가 생성되고 Redis를 통해 전송되며, <code>RedisMessageListenerContainer</code>가 해당 토픽을 구독하여 메시지를 수신하고, 지정된 리스너를 통해 메시지를 처리하는 과정이 이뤄집니다.</p>
<p><strong><code>while</code> 루프</strong>:</p>
<ul>
<li><code>Receiver</code> 클래스의 <code>getCount()</code> 메서드는 카운터 값을 반환합니다.</li>
<li><code>MessagingRedisApplication</code> 클래스의 <code>main()</code> 메서드에서는 <code>Receiver</code>의 인스턴스를 얻고, <code>getCount()</code>를 사용하여 현재까지 받은 메시지의 수를 확인합니다.</li>
<li><code>while (receiver.getCount() == 0)</code> 구문은 받은 메시지의 수가 0인 경우에만 실행됩니다.</li>
<li>이 구문은 Redis를 통해 "chat" 채널로 메시지를 전송하고, <code>Receiver</code> 클래스에서 메시지를 받을 때까지 루프를 돌게끔 설계되어 있습니다.</li>
<li>따라서 이 루프는 메시지를 받을 때까지 기다리고, <code>Receiver</code> 클래스에서 메시지를 처리할 수 있도록 로직을 구성하고 있습니다.</li>
</ul>
<p>이 <code>while</code> 루프는 받은 메시지의 수가 0인 경우에만 실행되며, 메시지가 도착할 때까지 메시지를 기다립니다. 메시지가 도착하면 <code>Receiver</code> 클래스의 <code>getCount()</code>가 0이 아닌 값을 반환하고 루프를 종료하게 됩니다.</p>
<hr>
<p>main에서 ApplicationContext.getBean을 사용하는 방식은 CommandLineRunner를 이용해서도 같은 기능을 구현할 수 있다. </p>
<p>예시 1</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RedisMessageProcessor</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> LOGGER <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">RedisMessageProcessor</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Receiver</span> receiver<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">StringRedisTemplate</span> template<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">RedisMessageProcessor</span><span class="token punctuation">(</span><span class="token class-name">Receiver</span> receiver<span class="token punctuation">,</span> <span class="token class-name">StringRedisTemplate</span> template<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>receiver <span class="token operator">=</span> receiver<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>template <span class="token operator">=</span> template<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">InterruptedException</span> <span class="token punctuation">{</span>
        <span class="token keyword">while</span> <span class="token punctuation">(</span>receiver<span class="token punctuation">.</span><span class="token function">getCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            LOGGER<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Sending message..."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            template<span class="token punctuation">.</span><span class="token function">convertAndSend</span><span class="token punctuation">(</span><span class="token string">"chat"</span><span class="token punctuation">,</span> <span class="token string">"Hello from Redis!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span><span class="token number">500L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>예시 2</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">StringRedisTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CommandLineRunnerConfig</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">Receiver</span> receiver<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">StringRedisTemplate</span> template<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">redisMessageProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token keyword">while</span> <span class="token punctuation">(</span>receiver<span class="token punctuation">.</span><span class="token function">getCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                LOGGER<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"Sending message..."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                template<span class="token punctuation">.</span><span class="token function">convertAndSend</span><span class="token punctuation">(</span><span class="token string">"chat"</span><span class="token punctuation">,</span> <span class="token string">"Hello from Redis!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span><span class="token number">500L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p><code>listenerAdapter</code> 메서드에서 정의된 빈은 <code>container</code> 메서드에서 정의된 메시지 리스너 컨테이너에 메시지 리스너로 등록되어 "<code>chat</code>" 토픽의 메시지를 수신합니다. </p>
<p><code>Receiver</code> 클래스는 POJO이므로 <code>addMessageListener()</code>에 필요한 <code>MessageListener</code> 인터페이스를 구현하는 메시지 리스너 어댑터로 래핑해야 합니다. 메시지 리스너 어댑터는 또한 메시지가 도착할 때 <code>Receiver</code> 클래스의 <code>receiveMessage()</code> 메서드를 호출하도록 구성됩니다.</p>
<p>메시지를 리슨하기 위해 필요한 것은 연결 팩토리와 메시지 리스너 컨테이너 빈 뿐입니다. 메시지를 보내려면 Redis 템플릿도 필요합니다. 여기에서는 키와 값 모두 <code>String</code> 인스턴스인 Redis의 일반적인 사용에 초점을 맞춘 <code>RedisTemplate</code> 구현인 <code>StringRedisTemplate</code>로 구성된 빈입니다.</p>
<p><code>main()</code> 메서드는 스프링 애플리케이션 컨텍스트를 만들면서 모든 것을 시작합니다. 애플리케이션 컨텍스트는 메시지 리스너 컨테이너를 시작하고, 메시지 리스너 컨테이너 빈은 메시지를 수신하기 시작합니다. <code>main()</code> 메서드는 애플리케이션 컨텍스트에서 <code>StringRedisTemplate</code> 빈을 검색하고 그것을 사용하여 "<code>chat</code>" 토픽에 "<code>Hello from Redis!</code>" 메시지를 보냅니다. 마지막으로 스프링 애플리케이션 컨텍스트를 닫고 애플리케이션이 종료됩니다.</p>
<h2 id="실행해보기">실행해보기</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2a2158b8-4265-49df-a5ae-d3bef7c14377/image.png"></p>