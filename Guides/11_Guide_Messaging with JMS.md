<p>이 가이드는 JMS 브로커를 사용하여 메시지를 게시하고 구독하는 과정을 안내합니다.</p>
<h2 id="메시지-브로커와-미들웨어">메시지 브로커와 미들웨어</h2>
<h3 id="1-메시지-topic-queue-개념-요약">1. 메시지, Topic, Queue 개념 요약:</h3>
<ul>
<li>
<p><strong>메시지(Message)</strong>: 데이터 교환을 위한 패킷. 일반적으로 구조화된 데이터 형태로 전달되며, 시스템 간 통신을 위한 정보를 포함합니다.</p>
</li>
<li>
<p><strong>Queue</strong>: 메시지가 저장되는 곳으로, 한 송신자가 보낸 메시지는 하나 이상의 수신자에게 전달됩니다. 일반적으로 'FIFO(First In, First Out)' 방식으로 메시지를 처리합니다.</p>
</li>
<li>
<p><strong>Topic</strong>: Publish/Subscribe 패턴에서 사용됩니다. 메시지는 Topic으로 발행되며, 해당 Topic을 구독하는 모든 수신자가 해당 메시지를 수신합니다. 이는 특정 주제나 키워드에 관심이 있는 수신자들에게 메시지를 전송하는 데 사용됩니다.</p>
</li>
</ul>
<h3 id="2-대략적인-구조">2. 대략적인 구조</h3>
<ul>
<li>
<p><strong>POJO(Plain Old Java Object)</strong>: 메시지의 내용을 표현하는 데 사용됩니다. 이는 데이터를 담고 있는 단순한 자바 객체입니다.</p>
</li>
<li>
<p><strong>메시지 리스너 컨테이너(Message Listener Container)</strong>: 대기열에 메시지를 보내고 처리하는 컴포넌트입니다. 메시지를 수신하고 필요한 처리를 위해 메시지 리스너(예: @JmsListener)를 구독하고 관리합니다.</p>
<ul>
<li>메시지 수신 및 전달, 스케줄링, 변환, 오류 처리</li>
<li>비동기적 처리와 병렬화를 통한 메시지 처리 성능 향상</li>
<li>메시지 브로커와 메시지 송수신 관리하는 런타임 환경</li>
</ul>
</li>
<li>
<p><strong>템플릿(Template)</strong>: 메시지를 생성하고 송수신하는 추상화된 인터페이스를 제공합니다. 메시지를 송수신할 때 사용하는 다양한 연산을 단순화하고 추상화시킵니다. 예를 들어, JMS의 JmsTemplate, RabbitMQ의 RabbitTemplate 등이 있습니다.</p>
</li>
<li>
<p><strong>커넥션 팩토리(Connection Factory)</strong>: 메시지 브로커와의 연결을 설정하고 관리하는 데 사용됩니다. 연결을 생성하고 관리하여 통신을 가능하게 합니다.</p>
<ul>
<li>커넥션, 세션, 프로듀서, 컨슈머 등을 관리하고 재사용 할 수 있게 하는 인터페잇 제공</li>
</ul>
</li>
</ul>
<h3 id="3-redis-rabbitmq-jms">3. Redis, RabbitMQ, JMS</h3>
<ul>
<li>
<p>공통점:</p>
<ul>
<li>모두 메시지 기반 통신을 지원하여 분산 시스템 간의 통신을 용이하게 합니다.</li>
<li>메시지 큐를 통해 메시지를 비동기적으로 송수신할 수 있어 시스템 간 결합도를 낮출 수 있습니다.</li>
<li>안정성과 확장성을 제공하여 대량의 데이터 및 요청을 처리할 수 있습니다.</li>
</ul>
</li>
<li>
<p>차이점:</p>
<ul>
<li>Redis: 메모리 기반의 데이터 구조 저장소로, 빠른 속도와 성능을 제공합니다. 주로 캐싱, 세션 관리, 게임 리더보드 등에 사용됩니다.</li>
<li>RabbitMQ: AMQP(Advanced Message Queuing Protocol)를 기반으로 하는 오픈 소스 메시지 브로커로, 대기열을 통해 메시지를 처리합니다. 유연한 라우팅 및 교환 기능을 제공합니다.</li>
<li>JMS(Java Message Service): 자바 플랫폼용 메시지 기반 미들웨어 표준으로, 여러 메시지 브로커를 사용할 수 있습니다. JMS API는 다양한 메시지 형식 및 전송 프로토콜을 지원합니다. JMS는 다른 메시지 브로커들과 호환성을 유지하며, Spring에서도 JMS를 쉽게 사용할 수 있도록 지원합니다.</li>
</ul>
</li>
</ul>
<h3 id="4-redis-rabbitmq-jms의-메시징-방식이-프로그래밍적-요소에-미치는-영향">4. Redis, RabbitMQ, JMS의 메시징 방식이 프로그래밍적 요소에 미치는 영향:</h3>
<ul>
<li>
<p><strong>Redis</strong>: Pub/Sub 모델을 사용하며, 메시지를 채널을 통해 발행하고 구독합니다. 이는 Queue와 Topic과는 조금 다른 패러다임이며, 메시지 브로커나 컨테이너 등의 추상화 요소가 적습니다.</p>
</li>
<li>
<p><strong>RabbitMQ</strong>: Exchange를 사용하여 메시지 라우팅을 관리하며, Direct, Fanout, Topic 등 다양한 Exchange 타입을 지원합니다. 이에 맞춰 컨테이너, 템플릿, 리스너 등의 프로그래밍적 요소가 Exchange에 따라 다르게 설정됩니다.</p>
</li>
<li>
<p><strong>JMS</strong>: Queue는 P2P 메시징, Topic은 Publish/Subscribe 모델을 지원하여 프로그래머가 필요에 따라 이러한 패턴을 적용할 수 있습니다. JMS의 컨테이너, 커넥션 팩토리, 템플릿, 리스너 등은 이러한 다양한 메시징 모델을 지원하도록 구성됩니다.</p>
</li>
</ul>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring의 <code>JmsTemplate</code>을 사용하여 단일 메시지를 게시하고 관리되는 Bean의 <code>@JmsListener</code> 주석 메서드를 사용하여 이를 구독하는 애플리케이션을 빌드합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-null">plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.0'
	id 'io.spring.dependency-management' version '1.1.4'
}

group = 'guides'
version = '0.0.1-SNAPSHOT'

java {
	sourceCompatibility = '17'
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-artemis'
	implementation 'org.springframework.boot:spring-boot-starter-json'
	runtimeOnly 'org.apache.activemq:artemis-jakarta-server'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h2 id="create-a-message-receiver">Create a message Receiver</h2>
<p>Spring은 POJO(Plain Old Java Object)에 메시지를 게시(publish)하는 수단을 제공합니다.</p>
<p>이 가이드에서는 JMS 메시지 브로커를 통해 메시지를 보내는 방법을 설명합니다. 시작하려면 이메일 메시지의 세부정보를 구현하는(embodies) 간단한 POJO를 만듭니다. 우리는 이메일 메시지를 보내는 것이 아닙니다. 우리는 메시지로 보낼 내용(WHAT to send)에 대한 세부 정보를 한 곳에서 다른 곳으로 보냅니다.</p>
<p>src/main/java/hello/Email.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>


<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Email</span> <span class="token punctuation">{</span>

  <span class="token keyword">private</span> <span class="token class-name">String</span> <span class="token keyword">to</span><span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> body<span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">Email</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token class-name">Email</span><span class="token punctuation">(</span><span class="token class-name">String</span> <span class="token keyword">to</span><span class="token punctuation">,</span> <span class="token class-name">String</span> body<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token keyword">to</span> <span class="token operator">=</span> <span class="token keyword">to</span><span class="token punctuation">;</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>body <span class="token operator">=</span> body<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getTo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token keyword">to</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setTo</span><span class="token punctuation">(</span><span class="token class-name">String</span> <span class="token keyword">to</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token keyword">to</span> <span class="token operator">=</span> <span class="token keyword">to</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> body<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBody</span><span class="token punctuation">(</span><span class="token class-name">String</span> body<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>body <span class="token operator">=</span> body<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"Email{to=%s, body=%s}"</span><span class="token punctuation">,</span> <span class="token function">getTo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">getBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이 POJO는 추정된 getter 및 setter 세트와 함께 두 개의 필드(<code>to</code> 및 <code>body</code>)를 포함하는 매우 간단합니다.</p>
<p>여기에서 메시지 수신자(Receiver)를 정의할 수 있습니다.</p>
<p>src/main/java/hello/Receiver.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JmsListener</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Receiver</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@JmsListener</span><span class="token punctuation">(</span>destination <span class="token operator">=</span> <span class="token string">"mailbox"</span><span class="token punctuation">,</span> containerFactory <span class="token operator">=</span> <span class="token string">"myFactory"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">receiveMessage</span><span class="token punctuation">(</span><span class="token class-name">Email</span> email<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Received &lt;"</span><span class="token operator">+</span>email<span class="token operator">+</span><span class="token string">"&gt;"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Receiver</code>는 <strong>메시지 기반 POJO(message-driven POJO)</strong> 라고도 합니다. 코드에서 볼 수 있듯이 특정 인터페이스를 구현하거나 메서드가 특정 이름을 가질 필요가 없습니다. 게다가, 이 메소드는 유연한 시그니처를 가질 수도 있습니다. 특히 이 클래스에는 JMS API를 가져오지 않는다는 점에 유의하세요.</p>
<p><code>JmsListener</code> 주석은 이 메소드가 수신(listen)해야 하는 <code>Destination</code>의 이름과 기본 메시지 수신기(listener) 컨테이너를 생성하는 데 사용할 <code>JmsListenerContainerFactory</code>에 대한 참조를 정의합니다. 엄밀히 말하면 Spring Boot는 필요한 경우 기본 팩토리를 등록하므로 컨테이너 빌드 방식을 사용자 정의해야 하는 경우가 아니면 마지막 속성은 필요하지 않습니다.</p>
<p>참조 문서에서 이에 대해 더 자세히 다루고 있습니다.</p>
<h2 id="spring을-사용하여-jms-메시지-보내기-및-받기">Spring을 사용하여 JMS 메시지 보내기 및 받기</h2>
<p>다음으로 송신자(sender)와 수신자(receiver)를 연결합니다.</p>
<p>src/main/java/hello/Application.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>jms</span><span class="token punctuation">.</span><span class="token class-name">ConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>jms</span><span class="token punctuation">.</span><span class="token class-name">DefaultJmsListenerContainerFactoryConfigurer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ConfigurableApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">EnableJms</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JmsListener</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>config</span><span class="token punctuation">.</span><span class="token class-name">DefaultJmsListenerContainerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>config</span><span class="token punctuation">.</span><span class="token class-name">JmsListenerContainerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">JmsTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>support<span class="token punctuation">.</span>converter</span><span class="token punctuation">.</span><span class="token class-name">MappingJackson2MessageConverter</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>support<span class="token punctuation">.</span>converter</span><span class="token punctuation">.</span><span class="token class-name">MessageConverter</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>jms<span class="token punctuation">.</span>support<span class="token punctuation">.</span>converter</span><span class="token punctuation">.</span><span class="token class-name">MessageType</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@EnableJms</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Application</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">JmsListenerContainerFactory</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span> <span class="token function">myFactory</span><span class="token punctuation">(</span><span class="token class-name">ConnectionFactory</span> connectionFactory<span class="token punctuation">,</span> <span class="token class-name">DefaultJmsListenerContainerFactoryConfigurer</span> configurer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">DefaultJmsListenerContainerFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultJmsListenerContainerFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// 메시지 변환기를 포함한 모든 자동 구성 기본값들을 이 팩토리에 제공합니다.</span>
        configurer<span class="token punctuation">.</span><span class="token function">configure</span><span class="token punctuation">(</span>factory<span class="token punctuation">,</span> connectionFactory<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// 필요하다면 설정을 더 override 할 수 있습니다.</span>
        <span class="token keyword">return</span>  factory<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span> <span class="token comment">// Serialize message content to json using TextMessage</span>
    <span class="token keyword">public</span> <span class="token class-name">MessageConverter</span> <span class="token function">jacksonJmsMessageConverter</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">MappingJackson2MessageConverter</span> converter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MappingJackson2MessageConverter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        converter<span class="token punctuation">.</span><span class="token function">setTargetType</span><span class="token punctuation">(</span><span class="token class-name">MessageType</span><span class="token punctuation">.</span>TEXT<span class="token punctuation">)</span><span class="token punctuation">;</span>
        converter<span class="token punctuation">.</span><span class="token function">setTypeIdPropertyName</span><span class="token punctuation">(</span><span class="token string">"_type"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> converter<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// Launch the application</span>
        <span class="token class-name">ConfigurableApplicationContext</span> context <span class="token operator">=</span>
                <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">Application</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">JmsTemplate</span> jmsTemplate <span class="token operator">=</span> context<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">JmsTemplate</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Send a message with a POJO - the template reuse the message converter</span>
        <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Sending an email message"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        jmsTemplate<span class="token punctuation">.</span><span class="token function">convertAndSend</span><span class="token punctuation">(</span><span class="token string">"mailbox"</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">Email</span><span class="token punctuation">(</span><span class="token string">"info@example.com"</span><span class="token punctuation">,</span> <span class="token string">"Hello"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<ul>
<li>
<p><code>MessageConverter</code>: JMS 메시지를 변환하는 데 사용되며, 메시지의 <code>Payload</code>를 직렬화하고 역직렬화합니다.</p>
<ul>
<li>페이로드는 메시지가 전송하는 데이터 자체를 의미하며, 메시지 시스템에 따라 선택적으로 사용될 수 있습니다.</li>
<li>일부 메시지 시스템에서는 메시지 페이로드를 명시적으로 다루기 위해 특정한 형식으로 메시지를 구성하거나, <code>MessageConverter</code>와 같은 도구를 사용하여 메시지의 내용을 변환하고 처리합니다. 이러한 변환기는 메시지의 페이로드를 시스템이 처리 가능한 형태로 변경하는 데 사용될 수 있습니다.</li>
</ul>
</li>
<li>
<p><code>MappingJackson2MessageConverter</code>: JSON 형식의 메시지를 JMS 메시지로 변환하고 역변환하는 데 사용됩니다.</p>
</li>
<li>
<p><code>ConfigurableApplicationContext</code>는 <code>ApplicationContext</code>를 확장한 인터페이스입니다.   </p>
<ul>
<li><code>ApplicationContext</code>는 애플리케이션 컨텍스트의 빈들을 로드하고 관리하는 역할을 수행합니다.</li>
<li><code>ConfigurableApplicationContext</code>는 이러한 빈 관리 능력에 추가로 애플리케이션의 라이프사이클을 제어하고, 환경 설정을 동적으로 변경할 수 있는 기능을 갖추고 있습니다.</li>
</ul>
</li>
<li>
<p><code>JmsTemplate jmsTemplate = context.getBean(JmsTemplate.class);</code>  </p>
<ul>
<li><code>context.getBean(JmsTemplate.class)</code>이 호출되면 스프링 컨텍스트가 <code>JmsTemplate</code> 타입의 빈을 찾아 반환합니다.</li>
<li>스프링은 아래 두 Bean을 자동으로 검색하여 <code>JmsTemplate</code>에 의존성 주입(Dependency Injection)합니다.<ul>
<li><code>myFactory</code> 메서드에서 : <code>DefaultJmsListenerContainerFactory</code> 빈 (JMS Listener를 생성)</li>
<li><code>jacksonJmsMessageConverter</code> 메서드에서 : <code>appingJackson2MessageConverter</code> 빈 (JSON 형식의 메시지를 JMS 메시지로 변환)</li>
</ul></li>
</ul>
</li>
<li>
<p><code>jmsTemplate.convertAndSend("mailbox", new Email("info@example.com", "Hello"));</code> : <code>JmsTemplate</code>은 내부적으로 <code>MappingJackson2MessageConverter</code>를 사용하여 POJO 객체인 <code>Email</code>을 JMS 메시지로 변환한 후, <code>mailbox</code>라는 대상(destination)에 메시지를 전송합니다.</p>
</li>
</ul>
<hr>
<p>명확성을 위해 수신기의 <code>JmsListener</code> 주석에서 참조되는 <code>myFactory</code> Bean도 정의했습니다. Spring Boot에서 제공하는 <code>DefaultJmsListenerContainerFactoryConfigurer</code> 인프라를 사용하기 때문에 <code>JmsMessageListenerContainer</code>는 Spring Boot가 기본적으로 생성하는 것과 동일합니다.</p>
<p>기본 <code>MessageConverter</code>는 기본 유형(예: <code>String</code>, <code>Map</code>, <code>Serializable</code>)만 변환할 수 있으며 <code>Email</code>은 의도적으로 <code>Serializable</code> 하지 않습니다. 우리는 Jackson을 사용하여 콘텐츠를 텍스트 형식(즉, <code>TextMessage</code>)으로 JSON으로 직렬화하려고 합니다. Spring Boot는 <code>MessageConverter</code>의 존재를 감지하고 이를 기본 <code>JmsTemplate</code>과 <code>DefaultJmsListenerContainerFactoryConfigurer</code>에 의해 생성된 <code>JmsListenerContainerFactory</code>에 연결합니다. JSON 변환기에는 <code>spring-boot-starter-json</code> 종속성이 필요합니다.</p>
<p><code>JmsTemplate</code>을 사용하면 JMS 대상(destination)으로 메시지를 보내는 것이 간단해집니다. <code>main</code> 러너(runner) 메소드에서는 작업을 시작한 후 <code>jmsTemplate</code>을 사용하여 <code>Email</code> POJO를 보낼 수 있습니다. 사용자 정의 <code>MessageConverter</code>가 자동으로 연결되었기 때문에 JSON 문서는 <code>TextMessage</code>에서만 생성됩니다.</p>
<p>정의되지 않은 두 개의 Bean은 <code>JmsTemplate</code> 및 <code>ConnectionFactory</code>입니다. 이는 Spring Boot에 의해 자동으로 생성됩니다. 또한 Spring Boot는 JMS 인프라를 사용할 수 있을 때 <code>@JmsListener</code> 주석이 달린 메서드를 자동으로 검색합니다. 즉, <code>@EnableJms</code>를 추가할 필요가 없습니다.</p>
<p>기본적으로 Spring Boot는 로컬 시스템에서 실행되는 artemis 브로커에 연결을 시도합니다. 다음 구성 속성을 추가하여 브로커를 포함할 수도 있습니다.</p>
<pre><code class="language-null">spring.artemis.mode=embedded</code></pre>
<p>또한 <code>org.apache.activemq:artemis-jakarta-server</code>에 대한 종속성을 추가해야 합니다.</p>
<p>기본적으로 Spring Boot는 <code>pubSubDomain</code>을 <code>false</code>로 설정하여 대기열(queue)로 전송하도록 구성된 <code>JmsTemplate</code>을 생성합니다. <code>JmsMessageListenerContainer</code>도 동일한 방식으로 구성됩니다. 재정의하려면 Spring Boot의 속성 설정(<code>application.properties</code> 내부 또는 환경 변수 설정)을 통해 <code>spring.jms.pub-sub-domain=true</code>를 설정하세요. 그런 다음 수신(receiving) 컨테이너의 설정이 동일한지 확인하세요.</p>
<blockquote>
<p>Spring의 <code>JmsTemplate</code>은 <code>receive</code> 메소드를 통해 직접 메시지를 수신할 수 있지만 이는 동기적으로만 작동하며, 이는 곧 'block'을 의미합니다. 그렇기 때문에 캐시 기반 연결 팩토리와 함께 <code>DefaultMessageListenerContainer</code>와 같은 리스너 컨테이너를 사용하는 것이 좋습니다. 그러면 메시지를 비동기적으로 최대 연결 효율성으로 사용할 수 있습니다.</p>
</blockquote>
<h2 id="실행-결과">실행 결과</h2>
<pre><code class="language-null">2023-12-16T00:32:25.991+09:00  INFO 480774 --- [           main] hello.Application                        : Starting Application using Java 17.0.9 with PID 480774 (/home/Dev-Hammy/IdeaProjects/messaging-jms/build/classes/java/main started by Dev-Hammy in /home/Dev-Hammy/IdeaProjects/messaging-jms)
2023-12-16T00:32:25.996+09:00  INFO 480774 --- [           main] hello.Application                        : No active profile set, falling back to 1 default profile: "default"
2023-12-16T00:32:27.059+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221000: live Message Broker is starting with configuration Broker Configuration (clustered=false,journalDirectory=/tmp/artemis-data/journal,bindingsDirectory=data/bindings,largeMessagesDirectory=data/largemessages,pagingDirectory=data/paging)
2023-12-16T00:32:27.079+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221045: libaio is not available, switching the configuration into NIO
2023-12-16T00:32:27.137+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221057: Global Max Size is being adjusted to 1/2 of the JVM max size (-Xmx). being defined as 1028653056
2023-12-16T00:32:27.177+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221043: Protocol module found: [artemis-server]. Adding protocol support for: CORE
2023-12-16T00:32:27.217+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601138: User anonymous@unknown is getting notification info on target resource: null
2023-12-16T00:32:27.218+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.ActiveMQServerControlImpl@2dd8239
2023-12-16T00:32:27.218+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601138: User anonymous@unknown is getting notification info on target resource: ActiveMQServerImpl::name=localhost
2023-12-16T00:32:27.232+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ224092: Despite disabled persistence, page files will be persisted.
2023-12-16T00:32:27.244+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221080: Deploying address DLQ supporting [ANYCAST]
2023-12-16T00:32:27.247+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.AddressControlImpl@71ad3d8a
2023-12-16T00:32:27.250+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221003: Deploying ANYCAST queue DLQ on address DLQ
2023-12-16T00:32:27.375+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.QueueControlImpl@6eb17ec8
2023-12-16T00:32:27.377+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221080: Deploying address ExpiryQueue supporting [ANYCAST]
2023-12-16T00:32:27.378+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.AddressControlImpl@730f9695
2023-12-16T00:32:27.378+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221003: Deploying ANYCAST queue ExpiryQueue on address ExpiryQueue
2023-12-16T00:32:27.379+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.QueueControlImpl@277bf091
2023-12-16T00:32:27.382+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.AddressControlImpl@3374b5bc
2023-12-16T00:32:27.423+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.base           : AMQ601019: User anonymous@unknown is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.AcceptorControlImpl@1687eb01
2023-12-16T00:32:27.431+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221007: Server is now live
2023-12-16T00:32:27.431+09:00  INFO 480774 --- [           main] o.apache.activemq.artemis.core.server    : AMQ221001: Apache ActiveMQ Artemis Message Broker version 2.31.2 [localhost, nodeID=26d18dee-9b5f-11ee-bab0-00e04c361b49] 
2023-12-16T00:32:27.718+09:00  INFO 480774 --- [           main] org.apache.activemq.audit.resource       : AMQ601767: CORE connection 2734bc93-9b5f-11ee-bab0-00e04c361b49 for user unknown@invm:0 created
2023-12-16T00:32:27.798+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601267: User anonymous@invm:0 is creating a core session on target resource ActiveMQServerImpl::name=localhost with parameters: [27422a16-9b5f-11ee-bab0-00e04c361b49, null, ****, 102400, RemotingConnectionImpl [ID=2734bc93-9b5f-11ee-bab0-00e04c361b49, clientID=null, nodeID=26d18dee-9b5f-11ee-bab0-00e04c361b49, transportConnection=InVMConnection [serverID=0, id=2734bc93-9b5f-11ee-bab0-00e04c361b49]], false, false, false, false, null, org.apache.activemq.artemis.core.protocol.core.impl.CoreSessionCallback@5fe78d8e, true, {}]
2023-12-16T00:32:27.852+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601267: User anonymous@invm:0 is creating a core session on target resource ActiveMQServerImpl::name=localhost with parameters: [274adca7-9b5f-11ee-bab0-00e04c361b49, null, ****, 102400, RemotingConnectionImpl [ID=2734bc93-9b5f-11ee-bab0-00e04c361b49, clientID=null, nodeID=26d18dee-9b5f-11ee-bab0-00e04c361b49, transportConnection=InVMConnection [serverID=0, id=2734bc93-9b5f-11ee-bab0-00e04c361b49]], false, false, false, false, null, org.apache.activemq.artemis.core.protocol.core.impl.CoreSessionCallback@1553d276, true, {}]
2023-12-16T00:32:27.861+09:00  INFO 480774 --- [           main] hello.Application                        : Started Application in 2.306 seconds (process running for 3.006)
Sending an email message
2023-12-16T00:32:27.868+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601267: User anonymous@invm:0 is creating a core session on target resource ActiveMQServerImpl::name=localhost with parameters: [274de9e8-9b5f-11ee-bab0-00e04c361b49, null, ****, 102400, RemotingConnectionImpl [ID=2734bc93-9b5f-11ee-bab0-00e04c361b49, clientID=null, nodeID=26d18dee-9b5f-11ee-bab0-00e04c361b49, transportConnection=InVMConnection [serverID=0, id=2734bc93-9b5f-11ee-bab0-00e04c361b49]], true, true, false, false, null, org.apache.activemq.artemis.core.protocol.core.impl.CoreSessionCallback@378dd896, true, {}]
2023-12-16T00:32:27.881+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601262: User anonymous@invm:0 is creating address on target resource: 274de9e8-9b5f-11ee-bab0-00e04c361b49 with parameters: [mailbox, [ANYCAST], true]
2023-12-16T00:32:27.884+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601019: User anonymous@invm:0 is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.AddressControlImpl@682c966e
2023-12-16T00:32:27.908+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.resource       : AMQ601065: User anonymous@invm:0 is creating a queue on target resource: ServerSessionImpl() with parameters: [QueueConfiguration [id=null, name=mailbox, address=mailbox, routingType=ANYCAST, filterString=null, durable=true, user=null, maxConsumers=-1, exclusive=null, groupRebalance=null, groupRebalancePauseDispatch=null, groupBuckets=null, groupFirstKey=null, lastValue=null, lastValueKey=null, nonDestructive=null, purgeOnNoConsumers=false, enabled=null, consumersBeforeDispatch=null, delayBeforeDispatch=null, consumerPriority=null, autoDelete=null, autoDeleteDelay=null, autoDeleteMessageCount=null, ringSize=null, configurationManaged=null, temporary=false, autoCreateAddress=null, internal=null, transient=null, autoCreated=true, fqqn=null]]
2023-12-16T00:32:27.909+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.resource       : AMQ601065: User anonymous@invm:0 is creating a queue on target resource: ServerSessionImpl() with parameters: [QueueConfiguration [id=null, name=mailbox, address=mailbox, routingType=ANYCAST, filterString=null, durable=true, user=null, maxConsumers=-1, exclusive=null, groupRebalance=null, groupRebalancePauseDispatch=null, groupBuckets=null, groupFirstKey=null, lastValue=null, lastValueKey=null, nonDestructive=null, purgeOnNoConsumers=false, enabled=null, consumersBeforeDispatch=null, delayBeforeDispatch=null, consumerPriority=null, autoDelete=null, autoDeleteDelay=null, autoDeleteMessageCount=null, ringSize=null, configurationManaged=null, temporary=false, autoCreateAddress=null, internal=null, transient=null, autoCreated=true, fqqn=null]]
2023-12-16T00:32:27.910+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601019: User anonymous@invm:0 is getting mbean info on target resource: org.apache.activemq.artemis.core.management.impl.QueueControlImpl@75d57014
2023-12-16T00:32:27.913+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.base           : AMQ601265: User anonymous@invm:0 is creating a core consumer on target resource ServerSessionImpl() with parameters: [0, mailbox, null, 0, false, true, null]
2023-12-16T00:32:28.019+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.message        : AMQ601501: User anonymous@invm:0 is consuming a message from mailbox: Reference[19]:RELIABLE:CoreMessage[messageID=19, durable=true, userID=2760d5ab-9b5f-11ee-bab0-00e04c361b49, priority=4, timestamp=Sat Dec 16 00:32:27 KST 2023, expiration=0, durable=true, address=mailbox, size=337, properties=TypedProperties[__AMQ_CID=27422a15-9b5f-11ee-bab0-00e04c361b49, _type=hello.Email, _AMQ_ROUTING_TYPE=1]]@207280815
2023-12-16T00:32:28.018+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.message        : AMQ601500: User anonymous@invm:0 sent a message CoreMessage[messageID=19, durable=true, userID=2760d5ab-9b5f-11ee-bab0-00e04c361b49, priority=4, timestamp=Sat Dec 16 00:32:27 KST 2023, expiration=0, durable=true, address=mailbox, size=337, properties=TypedProperties[__AMQ_CID=27422a15-9b5f-11ee-bab0-00e04c361b49, _type=hello.Email, _AMQ_ROUTING_TYPE=1]]@207280815, context: RoutingContextImpl(Address=mailbox, routingType=ANYCAST, PreviousAddress=mailbox previousRoute:ANYCAST, reusable=true, version=-2147483645)
..................................................
***** durable queues mailbox:
- queueID=14 address:mailbox name:mailbox filter:null
***** non durable for mailbox:
..................................................
, transaction: null
Received &lt;Email{to=info@example.com, body=Hello}&gt;
2023-12-16T00:32:28.079+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.message        : AMQ601759: User anonymous@invm:0 added acknowledgement of a message from mailbox: CoreMessage[messageID=19, durable=true, userID=2760d5ab-9b5f-11ee-bab0-00e04c361b49, priority=4, timestamp=Sat Dec 16 00:32:27 KST 2023, expiration=0, durable=true, address=mailbox, size=337, properties=TypedProperties[__AMQ_CID=27422a15-9b5f-11ee-bab0-00e04c361b49, _type=hello.Email, _AMQ_ROUTING_TYPE=1]]@207280815 to transaction: TransactionImpl [xid=null, txID=9, xid=null, state=ACTIVE, createTime=1702654347852(Sat Dec 16 00:32:27 KST 2023), timeoutSeconds=300, nr operations = 1]@662460a6
2023-12-16T00:32:28.081+09:00  INFO 480774 --- [mpl$6@62566842)] org.apache.activemq.audit.message        : AMQ601502: User anonymous@invm:0 acknowledged message from mailbox: CoreMessage[messageID=19, durable=true, userID=2760d5ab-9b5f-11ee-bab0-00e04c361b49, priority=4, timestamp=Sat Dec 16 00:32:27 KST 2023, expiration=0, durable=true, address=mailbox, size=337, properties=TypedProperties[__AMQ_CID=27422a15-9b5f-11ee-bab0-00e04c361b49, _type=hello.Email, _AMQ_ROUTING_TYPE=1]]@207280815, transaction: TransactionImpl [xid=null, txID=9, xid=null, state=COMMITTED, createTime=1702654347852(Sat Dec 16 00:32:27 KST 2023), timeoutSeconds=300, nr operations = 0]@662460a6</code></pre>
<p>이 로그들은 어플리케이션이 시작되고 ActiveMQ Artemis 메시지 브로커가 설정되고 동작하는 과정을 나타냅니다. </p>
<ol>
<li>
<p><strong>시작 및 설정 정보:</strong></p>
<ul>
<li><code>hello.Application</code>이라는 애플리케이션이 Java 17.0.9 버전으로 시작됨.</li>
<li>어떤 프로필도 설정되지 않았으므로 "default" 프로필을 사용하고 있음.</li>
<li>ActiveMQ Artemis 메시지 브로커가 설정되고 live 상태로 시작됨.</li>
</ul>
</li>
<li>
<p><strong>ActiveMQ Artemis 설정 정보:</strong></p>
<ul>
<li><code>libaio</code>가 사용 불가능하여 <code>NIO</code>로 설정 전환됨.</li>
<li>메모리 설정(<code>Global Max Size</code>)이 JVM 최대 메모리 크기의 절반으로 조정됨.</li>
<li>Protocol module <code>artemis-server</code>가 발견되어 <code>CORE</code> 프로토콜 지원 추가됨.</li>
<li><code>persistence</code>가 비활성화되어 있음에도 불구하고 페이지 파일이 지속됨.</li>
</ul>
</li>
<li>
<p><strong>주요 설정 및 큐 배포:</strong></p>
<ul>
<li>Dead Letter Queue(DLQ)와 ExpiryQueue가 배포됨.</li>
<li>각각의 큐에 대한 설정과 정보를 가져오는 것이 로그되어 있음.</li>
</ul>
</li>
<li>
<p><strong>서버 동작 및 메시지 전송:</strong></p>
<ul>
<li>서버가 라이브 상태로 설정되고 메시지 브로커 버전이 로깅됨.</li>
<li>메시지가 전송되고 송수신과 관련된 정보들이 로깅됨.</li>
</ul>
</li>
</ol>
<pre><code class="language-shell">Sending an email message
Received <span class="token operator">&lt;</span>Email<span class="token punctuation">{</span>to<span class="token operator">=</span>info@example.com, <span class="token assign-left variable">body</span><span class="token operator">=</span>Hello<span class="token punctuation">}</span><span class="token operator">&gt;</span></code></pre>
<p>이메일 전송에 관련된 내용</p>