<p>이 가이드는 비차단(non-blocking) Lettuce 드라이버를 사용하여 Redis와 상호 작용하기 위해 Spring Data를 사용하는 기능적(functional) 반응형(reactive) 애플리케이션을 생성하는 과정을 안내합니다.</p>
<hr>
<h2 id="reactive-redis와-일반-redis의-메시지-송수신-방식-차이">Reactive Redis와 일반 Redis의 메시지 송수신 방식 차이</h2>
<h3 id="reactive-redis">Reactive Redis</h3>
<ul>
<li><strong>제어권</strong>: 호출된 Reactive 함수 또는 메서드</li>
<li><strong>비동기/동기</strong>: 비동기적으로 작동</li>
<li><strong>논블로킹/블로킹</strong>: 논블로킹 방식</li>
</ul>
<h4 id="상황-설명">상황 설명:</h4>
<p>Reactive Redis는 비동기 및 논블로킹 방식으로 동작합니다. 예를 들어, ReactiveRedisOperations를 사용하여 Redis에 요청을 보내면, 해당 메서드는 호출되고 데이터를 요청한 후 즉시 반환됩니다. 그러나 데이터가 준비될 때까지 다른 작업을 수행할 수 있습니다. 결과는 콜백(callback)이나 리액티브 스트림(Reactive Streams)을 통해 처리됩니다. 이 경우 데이터의 처리와 제어권은 콜백이나 리액티브 스트림을 처리하는 컴포넌트에게 있습니다.</p>
<h3 id="일반-redis">일반 Redis</h3>
<ul>
<li><strong>제어권</strong>: 호출된 Redis 명령어 또는 함수</li>
<li><strong>비동기/동기</strong>: 대부분의 라이브러리는 동기적으로 작동하지만, 비동기 라이브러리도 있음</li>
<li><strong>논블로킹/블로킹</strong>: 블로킹 방식</li>
</ul>
<h4 id="상황-설명-1">상황 설명:</h4>
<p>대부분의 일반 Redis 라이브러리는 동기적으로 작동합니다. 따라서 Redis 명령어를 호출하면 해당 명령이 완료될 때까지 대기하며, 결과가 반환될 때까지 다른 작업을 수행할 수 없습니다. 이러한 방식에서는 명령어를 호출한 코드가 결과가 준비될 때까지 제어권을 가지게 됩니다. 그러나 일부 비동기 Redis 라이브러리의 경우, 명령어를 호출하고 결과를 기다리지 않고 다른 작업을 수행할 수 있게 해주며, 결과는 콜백 등을 통해 처리됩니다.</p>
<p>요약하자면, Reactive Redis에서는 데이터 처리 및 제어권이 콜백이나 리액티브 스트림을 처리하는 컴포넌트에게 있으며, 일반 Redis에서는 데이터 처리 및 제어권이 명령어를 호출한 코드에게 있거나, 비동기 라이브러리의 경우 콜백을 통해 처리됩니다.</p>
<hr>
<h2 id="redis에서-데이터의-저장">Redis에서 데이터의 저장</h2>
<p>Redis는 디스크나 메모리와 같은 물리적인 저장 공간에 데이터를 보관하는 인메모리 데이터베이스입니다. 기본적으로 Redis는 메모리에 데이터를 저장하며, 디스크에도 영구적으로 저장할 수 있도록 설정할 수 있습니다.</p>
<p>Redis의 데이터는 키-값(Key-Value) 형태로 저장되며, 각 키에는 해당하는 값이 할당됩니다. Redis는 다양한 데이터 구조를 지원하며, 문자열, 리스트, 해시, 집합, 정렬 집합 등 다양한 데이터 타입을 저장할 수 있습니다.</p>
<p>데이터가 메모리에 저장되므로 Redis는 빠른 읽기와 쓰기 속도를 제공합니다. 그러나 메모리의 용량 한계가 있기 때문에 Redis의 설정에 따라 메모리가 가득 차게 되면 LRU(Least Recently Used) 등의 정책에 따라 데이터를 삭제하거나 디스크에 영구적으로 저장합니다.</p>
<p>따라서 Redis는 디스크에 영속적으로 데이터를 저장할 수 있고, 캐시, 세션 관리, 메시지 브로커 등 다양한 용도로 사용되는데, 이때 디스크 저장 옵션을 설정하면 Redis는 메모리와 디스크 간에 데이터를 보관합니다.</p>
<hr>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>Spring Data Redis 및 Project Reactor를 사용하여 Redis 데이터 저장소와 반응적으로 상호 작용하고 차단(blocking) 없이 <code>Coffee</code> 객체를 저장하고 검색하는 Spring 애플리케이션을 구축합니다. 이 애플리케이션은 Reactive Streams 사양을 기반으로 하는 Reactor의 <code>Publisher</code> 구현, 즉 <code>Mono</code>(0 또는 1 값을 반환하는 publisher용) 및 <code>Flux</code>(0~n 값을 반환하는 publisher용)를 사용합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6b7ccb8f-66f1-4a5f-a9cd-df8caa83768e/image.png"></p>
<h2 id="standing-up-a-redis-server">Standing up a Redis server</h2>
<p>메시징 애플리케이션을 구축하기 전에 메시지 수신 및 전송을 처리할 서버를 설정해야 합니다.</p>
<p>Redis는 메시징 시스템과 함께 제공되는 오픈 소스 BSD 라이선스 키-값 데이터 저장소입니다. 서버는 <code>https://redis.io/download</code>에서 무료로 사용할 수 있습니다. </p>
<p>설치 과정은 다른 포스트 참고</p>
<h2 id="create-a-domain-class">Create a Domain Class</h2>
<p>커피 카탈로그에 보관하려는 커피 유형을 나타내는 클래스를 만듭니다. : src/main/java/com/example/demo/Coffee.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>demo</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> lombok<span class="token punctuation">.</span><span class="token class-name">AllArgsConstructor</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span><span class="token class-name">Data</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span><span class="token class-name">NoArgsConstructor</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Data</span>
<span class="token annotation punctuation">@NoArgsConstructor</span>
<span class="token annotation punctuation">@AllArgsConstructor</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Coffee</span> <span class="token punctuation">{</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> id<span class="token punctuation">;</span>
  <span class="token keyword">private</span> <span class="token class-name">String</span> name<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p>이 예제에서는 Lombok을 사용하여 생성자 및 소위 "데이터 클래스" 메서드(accessors/mutators, <code>equals()</code>, <code>toString()</code>, &amp; <code>hashCode()</code>)에 대한 상용구 코드를 제거했습니다.</p>
</blockquote>
<h2 id="create-a-configuration-class">Create a Configuration Class</h2>
<p>반응형 Redis 작업을 지원하는 Spring Bean을 포함하는 클래스를 만듭니다. src/main/java/com/example/demo/CoffeeConfiguration.java</p>
<pre><code class="language-java">

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>connection</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisOperations</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">RedisSerializationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>serializer</span><span class="token punctuation">.</span><span class="token class-name">StringRedisSerializer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CoffeeConfiguration</span> <span class="token punctuation">{</span>
  <span class="token annotation punctuation">@Bean</span>
  <span class="token class-name">ReactiveRedisOperations</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> <span class="token function">redisOperations</span><span class="token punctuation">(</span><span class="token class-name">ReactiveRedisConnectionFactory</span> factory<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> serializer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Jackson2JsonRedisSerializer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token class-name">Coffee</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">RedisSerializationContext</span><span class="token punctuation">.</span><span class="token class-name">RedisSerializationContextBuilder</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> builder <span class="token operator">=</span>
        <span class="token class-name">RedisSerializationContext</span><span class="token punctuation">.</span><span class="token function">newSerializationContext</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">StringRedisSerializer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">RedisSerializationContext</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> context <span class="token operator">=</span> builder<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span>serializer<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">ReactiveRedisTemplate</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>factory<span class="token punctuation">,</span> context<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<hr>
<h4 id="configurable과-configuration-차이"><code>@Configurable</code>과 <code>@Configuration</code> 차이</h4>
<p>간단히 말해서, <code>@Configurable</code>은 객체를 스프링 컨텍스트로 주입하기 위해 사용되는 반면, <code>@Configuration</code>은 스프링의 설정 정보를 포함하고 있는 클래스임을 선언하는 데 사용됩니다.</p>
<ol>
<li>
<p><strong><code>@Configurable</code></strong>:</p>
<ul>
<li><code>@Configurable</code>은 스프링에서 자동 와이어링(의존성 주입)을 허용하기 위해 사용됩니다.</li>
<li>주로 AspectJ 컴파일 타임 위빙 또는 스프링 컨텍스트의 <code>EnableLoadTimeWeaving</code> 설정과 함께 사용됩니다.</li>
<li>엔티티 클래스 등 스프링이 관리하지 않는 객체에서 스프링 빈을 사용하고 싶을 때 사용됩니다. 이를 위해 해당 클래스에 <code>@Configurable</code>을 붙이고 AspectJ 또는 load-time weaving을 설정하여 해당 클래스의 인스턴스가 스프링 빈처럼 관리되도록 할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong><code>@Configuration</code></strong>:</p>
<ul>
<li><code>@Configuration</code>은 스프링 설정 클래스를 정의할 때 사용됩니다.</li>
<li>이 어노테이션을 사용하여 자바 기반의 스프링 설정을 정의하고, <code>@Bean</code> 어노테이션을 사용하여 빈을 생성하고 구성합니다.</li>
<li>이 클래스는 일반적으로 <code>@ComponentScan</code>, <code>@Import</code>, <code>@PropertySource</code> 등 다른 설정 어노테이션과 함께 사용되며, 스프링 애플리케이션 컨텍스트를 초기화하는 데 필요한 설정 정보를 제공합니다.</li>
</ul>
</li>
</ol>
<hr>
<h4 id="reactive-redis와-일반-redis의-컴포넌트-대응">Reactive Redis와 일반 Redis의 컴포넌트 대응</h4>
<ol>
<li><strong>ReactiveRedisOperations</strong></li>
</ol>
<ul>
<li><strong>일반적인 웹에서</strong>: RedisTemplate 또는 StringRedisTemplate</li>
<li><strong>설명</strong>: Redis 데이터와 상호작용하는 데 사용되는 인터페이스. 키-값 형태의 데이터를 읽고 쓰는 기능을 제공합니다.</li>
</ul>
<ol start="2">
<li><strong>ReactiveRedisTemplate&lt;&gt;(factory, context)</strong></li>
</ol>
<ul>
<li><strong>일반적인 웹에서</strong>: RedisTemplate 또는 StringRedisTemplate</li>
<li><strong>설명</strong>: ReactiveRedisOperations의 구현체. Redis에 비동기적으로 데이터를 저장하고 검색하는 데 사용됩니다. Reactive 스타일의 Redis 작업을 수행할 수 있게 해줍니다.</li>
</ul>
<ol start="3">
<li><strong>ReactiveRedisConnectionFactory</strong></li>
</ol>
<ul>
<li><strong>일반적인 웹에서</strong>: RedisConnectionFactory</li>
<li><strong>설명</strong>: Redis 서버와의 연결을 설정하고 관리하는 팩토리 인터페이스. 연결을 생성하고 관리하는 역할을 담당합니다.</li>
</ul>
<ol start="4">
<li><strong>RedisSerializationContext</strong></li>
</ol>
<ul>
<li><strong>일반적인 웹에서</strong>: RedisSerializer</li>
<li><strong>설명</strong>: Redis에 데이터를 저장하고 검색할 때 사용되는 직렬화 및 역직렬화를 구성하는 클래스. 데이터를 직렬화하고 역직렬화하는 방법을 정의합니다.</li>
</ul>
<hr>
<h4 id="예제-코드-해석">예제 코드 해석</h4>
<p>이 코드는 Redis와 상호작용하기 위한 스프링 빈을 구성하는 <code>CoffeeConfiguration</code> 클래스입니다. 여기서 사용된 주요 요소들을 설명해드릴게요.</p>
<ol>
<li>
<p><code>@Configuration</code>: 이 어노테이션은 이 클래스가 스프링의 구성 클래스임을 나타냅니다. 따라서 이 클래스에서는 스프링 빈(bean)을 정의하고 구성할 수 있습니다.</p>
</li>
<li>
<p><code>@Bean</code>: 이 어노테이션은 해당 메서드가 빈을 생성하고 스프링 컨테이너에 등록하는 역할을 합니다.</p>
</li>
<li>
<p><code>ReactiveRedisOperations&lt;String, Coffee&gt; redisOperations(ReactiveRedisConnectionFactory factory)</code>: 이 메서드는 Reactive Redis 연산을 수행하기 위한 <code>ReactiveRedisOperations</code> 빈을 생성합니다. 이 빈은 Redis와 상호작용할 수 있는 메서드를 제공합니다. 메서드는 <code>ReactiveRedisConnectionFactory</code>를 매개변수로 받아와 Redis 연결 팩토리를 주입받습니다.</p>
</li>
<li>
<p><code>Jackson2JsonRedisSerializer</code>: 이 클래스는 Jackson을 사용하여 Java 객체를 JSON 형식으로 직렬화/역직렬화할 수 있는 Redis Serializer입니다. 여기서는 <code>Coffee</code> 객체를 JSON으로 직렬화하는데 사용됩니다.</p>
</li>
<li>
<p><code>RedisSerializationContext</code>: Redis 직렬화 컨텍스트를 구성합니다. <code>StringRedisSerializer</code>를 키(key)에 사용하고, <code>Jackson2JsonRedisSerializer</code>를 값(value)에 사용하여 Redis에 저장될 데이터의 직렬화 형식을 설정합니다.</p>
</li>
<li>
<p><code>new ReactiveRedisTemplate&lt;&gt;(factory, context)</code>: <code>ReactiveRedisTemplate</code>을 생성하고, 위에서 구성한 연결 팩토리와 직렬화 컨텍스트를 사용하여 Redis와 상호작용할 수 있는 Reactive Redis 템플릿을 생성합니다.</p>
</li>
</ol>
<p>이 코드는 Reactive 스타일의 Redis 작업을 위해 Redis 연결을 설정하고, <code>Coffee</code> 객체를 JSON으로 직렬화하여 Redis에 저장하고 검색할 수 있는 기능을 스프링 빈으로 구성하고 있어요.</p>
<hr>
<h2 id="create-a-spring-bean-to-load-data">Create a Spring Bean to Load Data</h2>
<p>애플리케이션을 시작할 때 애플리케이션에 대한 샘플 데이터를 로드하기 위해 Spring Bean을 생성합니다.</p>
<blockquote>
<p>애플리케이션을 여러 번 (다시) 시작할 수 있으므로 먼저 이전 실행에서 여전히 존재할 수 있는 데이터를 제거해야 합니다. 이는 <code>flashAll()</code>(Redis) 서버 명령을 사용하여 수행합니다. 기존 데이터를 플러시한 후에는 작은 <code>Flux</code>를 만들고 각 커피 이름을 <code>Coffee</code> 객체에 매핑한 다음 반응형 Redis 저장소에 저장합니다. 그런 다음 저장소에서 모든 값을 쿼리하고 표시합니다.</p>
</blockquote>
<p>src/main/java/com/example/demo/CoffeeLoader.java</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">jakarta<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">PostConstruct</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>connection</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisConnectionFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisOperations</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Flux</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span>UUID<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CoffeeLoader</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ReactiveRedisConnectionFactory</span> factory<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ReactiveRedisOperations</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> coffeOps<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">CoffeeLoader</span><span class="token punctuation">(</span><span class="token class-name">ReactiveRedisConnectionFactory</span> factory<span class="token punctuation">,</span> <span class="token class-name">ReactiveRedisOperations</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> coffeeOps<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>factory <span class="token operator">=</span> factory<span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>coffeOps <span class="token operator">=</span> coffeeOps<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
   
    <span class="token annotation punctuation">@PostConstruct</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">loadData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        factory<span class="token punctuation">.</span><span class="token function">getReactiveConnection</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">serverCommands</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">flushAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenMany</span><span class="token punctuation">(</span>
            <span class="token class-name">Flux</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token string">"Jet Black Redis"</span><span class="token punctuation">,</span> <span class="token string">"Darth Redis"</span><span class="token punctuation">,</span> <span class="token string">"Black Alert Redis"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>name <span class="token operator">-&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Coffee</span><span class="token punctuation">(</span>UUID<span class="token punctuation">.</span><span class="token function">randomUUID</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>coffee <span class="token operator">-&gt;</span> coffeOps<span class="token punctuation">.</span><span class="token function">opsForValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">set</span><span class="token punctuation">(</span>coffee<span class="token punctuation">.</span><span class="token function">getId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> coffee<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">thenMany</span><span class="token punctuation">(</span>coffeOps<span class="token punctuation">.</span><span class="token function">keys</span><span class="token punctuation">(</span><span class="token string">"*"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>coffeOps<span class="token punctuation">.</span><span class="token function">opsForValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">get</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span><span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token operator">:</span><span class="token operator">:</span><span class="token function">println</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<h4 id="redistemplate-의-opsfor-메서드">RedisTemplate 의 <code>opsFor</code> 메서드</h4>
<ol>
<li>
<p><strong>opsForValue()</strong></p>
<ul>
<li><code>opsForValue()</code> 메서드는 Redis의 Key-Value 구조의 Value에 대한 작업을 수행합니다.</li>
<li><code>ValueOperations</code> 반환</li>
<li>주요 메서드:<ul>
<li><code>set(K key, V value)</code>: 주어진 키에 값을 설정합니다.</li>
<li><code>get(K key)</code>: 주어진 키의 값을 가져옵니다.</li>
<li><code>increment(K key)</code>: 숫자 값을 증가시킵니다.</li>
<li><code>delete(K key)</code>: 주어진 키와 해당 값을 삭제합니다.</li>
</ul></li>
</ul>
</li>
<li>
<p><strong>opsForList()</strong></p>
<ul>
<li><code>opsForList()</code> 메서드는 Redis의 리스트 구조에 대한 작업을 수행합니다.</li>
<li><code>ListOperations</code> 반환</li>
<li>주요 메서드:<ul>
<li><code>leftPush(K key, V value)</code>: 리스트 왼쪽에 값을 추가합니다.</li>
<li><code>rightPop(K key)</code>: 리스트 오른쪽에서 값을 꺼냅니다.</li>
<li><code>size(K key)</code>: 리스트의 크기를 가져옵니다.</li>
<li><code>trim(K key, long start, long end)</code>: 리스트를 잘라서 원하는 부분만 남깁니다.</li>
</ul></li>
</ul>
</li>
<li>
<p><strong>opsForSet()</strong></p>
<ul>
<li><code>opsForSet()</code> 메서드는 Redis의 집합 구조에 대한 작업을 수행합니다.</li>
<li><code>SetOperations</code> 반환</li>
<li>주요 메서드:<ul>
<li><code>add(K key, V... values)</code>: 집합에 값을 추가합니다.</li>
<li><code>members(K key)</code>: 집합의 모든 멤버를 가져옵니다.</li>
<li><code>size(K key)</code>: 집합의 크기를 가져옵니다.</li>
<li><code>remove(K key, V... values)</code>: 집합에서 값을 제거합니다.</li>
</ul></li>
</ul>
</li>
<li>
<p><strong>opsForHash()</strong></p>
<ul>
<li><code>opsForHash()</code> 메서드는 Redis의 해시 구조에 대한 작업을 수행합니다.</li>
<li><code>HashOperations</code> 반환</li>
<li>주요 메서드:<ul>
<li><code>put(K key, HK hashKey, HV value)</code>: 해시에 값을 추가합니다.</li>
<li><code>get(K key, Object hashKey)</code>: 해시에서 특정 키의 값을 가져옵니다.</li>
<li><code>delete(K key, Object... hashKeys)</code>: 해시에서 키와 해당 값을 제거합니다.</li>
</ul></li>
</ul>
</li>
</ol>
<hr>
<h4 id="예제-코드-요소">예제 코드 요소</h4>
<ol>
<li><strong>Flux</strong>: Reactor 라이브러리의 핵심 요소 중 하나로, Reactive Streams 스펙을 따르는 Publisher입니다. 여러 개의 데이터를 생성하고 비동기적으로 처리할 수 있는 시퀀스를 나타내는데 사용됩니다. 여러 값을 생성할 수 있는데, 이 때 Flux는 0부터 N개의 요소를 처리할 수 있습니다.</li>
</ol>
<ul>
<li>
<p><strong><code>Flux.just</code></strong>:</p>
<ul>
<li><code>Flux.just</code>는 주어진 값들을 이용하여 Flux를 생성하는 메서드입니다.</li>
<li>0개 이상의 값을 가질 수 있으며, 주어진 값들을 순서대로 내보냅니다.</li>
<li>예를 들어, <code>Flux.just(1, 2, 3)</code>은 1, 2, 3의 값을 차례대로 내보내는 Flux를 생성합니다.</li>
</ul>
<pre><code class="language-java"> <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> numbers <span class="token operator">=</span> <span class="token class-name">Flux</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
</li>
<li>
<p><strong><code>thenMany</code></strong>:</p>
<ul>
<li><code>thenMany</code>는 이전 작업이 완료된 후에 다른 Flux나 Mono를 실행하는데 사용됩니다.</li>
<li>보통 비동기 작업이 순차적으로 수행되어야 할 때 사용됩니다.</li>
<li><code>thenMany</code>는 Mono나 Flux에서 사용할 수 있으며, 다른 Flux나 Mono를 반환하거나 연결하는데 사용됩니다.</li>
</ul>
<pre><code class="language-java"><span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> flux1 <span class="token operator">=</span> <span class="token class-name">Flux</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> flux2 <span class="token operator">=</span> <span class="token class-name">Flux</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

 flux1<span class="token punctuation">.</span><span class="token function">thenMany</span><span class="token punctuation">(</span>flux2<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">subscribe</span><span class="token punctuation">(</span><span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token operator">:</span><span class="token operator">:</span><span class="token function">println</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// flux1이 완료된 후 flux2의 값을 차례로 출력합니다.</span></code></pre>
</li>
</ul>
<ol start="2">
<li>
<p><strong>map과 flatMap의 차이</strong>:</p>
<ul>
<li><strong>map</strong>: 스트림 내의 각 요소에 함수를 적용하여 새로운 요소로 변환합니다. 즉, 각 요소를 일대일로 매핑하여 변환합니다.</li>
<li><strong>flatMap</strong>: 각 요소를 어떤 함수에 적용하고 그 결과로 생성된 여러 Flux나 Mono를 1개의 Flux로 평탄화(flatten)합니다. 즉, 여러 Flux를 하나로 합치는 작업을 수행합니다.</li>
</ul>
</li>
<li>
<p><strong>System.out::println</strong>: 이것은 메서드 레퍼런스(Method Reference)입니다. Java 8부터 도입된 기능으로, 람다식으로 표현된 메서드를 축약해서 표현할 수 있는 방법 중 하나입니다. <code>System.out.println()</code>을 사용하여 콘솔에 출력하는 메서드를 가리킵니다. <code>::</code>은 메서드 레퍼런스를 사용할 때의 구분자입니다. <code>System.out::println</code>은 메서드 레퍼런스로, <code>println()</code> 메서드를 가리킵니다. 이것을 subscribe 시 사용하면 각 요소를 <code>println</code> 메서드에 전달하여 출력합니다.</p>
</li>
</ol>
<h4 id="예제-코드-해석-1">예제 코드 해석</h4>
<p>이 코드는 <code>CoffeeLoader</code>라는 Spring <code>@Component</code>입니다. 클래스가 생성되고, 모든 의존성이 주입된 후에 <code>@PostConstruct</code>가 붙은 메서드가 실행됩니다. 따라서 <code>CoffeeLoader</code> 클래스가 생성된 후에 <code>@PostConstruct</code>가 달린 <code>loadData()</code> 메서드가 실행되어 Redis 데이터를 초기화하고 작업을 수행하게 됩니다</p>
<ol>
<li>
<p><code>ReactiveRedisConnectionFactory</code>와 <code>ReactiveRedisOperations</code>를 필드로 주입받습니다. 이는 Reactive Redis에 연결하고 데이터를 처리하기 위한 연결과 작업을 수행할 수 있게 합니다.</p>
</li>
<li>
<p><code>loadData()</code> 메서드는 애플리케이션이 시작될 때 실행됩니다. 이 메서드는 Redis의 데이터를 모두 지우고(<code>flushAll()</code>) 새로운 데이터를 저장합니다.</p>
</li>
<li>
<p><code>Flux.just("Jet Black Redis", "Darth Redis", "Black Alert Redis")</code>는 세 가지 커피 종류를 Flux로 생성합니다.</p>
</li>
<li>
<p><code>map(name -&gt; new Coffee(UUID.randomUUID().toString(), name))</code>는 각 커피 종류마다 UUID와 이름을 가진 Coffee 객체를 생성합니다.</p>
</li>
<li>
<p><code>flatMap(coffee -&gt; coffeOps.opsForValue().set(coffee.getId(), coffee))</code>는 생성된 Coffee 객체를 Redis에 저장합니다. <code>opsForValue().set()</code>을 사용하여 각 Coffee 객체를 Redis의 Key-Value 형태로 저장합니다.</p>
</li>
<li>
<p><code>coffeOps.keys("*").flatMap(coffeOps.opsForValue()::get)</code>는 Redis에 저장된 모든 데이터를 가져옵니다. <code>keys("*")</code>로 모든 키를 가져온 뒤, 각 키에 대해 <code>opsForValue().get()</code>을 통해 해당 값을 가져옵니다.</p>
</li>
<li>
<p><code>subscribe(System.out::println)</code>은 가져온 데이터를 출력합니다. 즉, Redis에 저장된 모든 Coffee 객체를 가져와서 출력합니다.</p>
</li>
</ol>
<hr>
<h2 id="create-a-restcontroller">Create a RestController</h2>
<p>애플리케이션에 외부 인터페이스를 제공하기 위해 <code>RestController</code>를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>data<span class="token punctuation">.</span>redis<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">ReactiveRedisOperations</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Flux</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CoffeeController</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ReactiveRedisOperations</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> coffeeOps<span class="token punctuation">;</span>

    <span class="token class-name">CoffeeController</span><span class="token punctuation">(</span><span class="token class-name">ReactiveRedisOperations</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> coffeeOps<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>coffeeOps <span class="token operator">=</span> coffeeOps<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/coffees"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> <span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> coffeeOps<span class="token punctuation">.</span><span class="token function">keys</span><span class="token punctuation">(</span><span class="token string">"*"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>coffeeOps<span class="token punctuation">.</span><span class="token function">opsForValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token operator">:</span><span class="token operator">:</span><span class="token function">get</span><span class="token punctuation">)</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p><code>flatMap</code>에서 메서드 레퍼런스 대신 람다 표현식을 사용하여 같은 코드를 표현하려면 아래와 같이 할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/coffees"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Coffee</span><span class="token punctuation">&gt;</span></span> <span class="token function">all</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> coffeeOps<span class="token punctuation">.</span><span class="token function">keys</span><span class="token punctuation">(</span><span class="token string">"*"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">flatMap</span><span class="token punctuation">(</span>key <span class="token operator">-&gt;</span> coffeeOps<span class="token punctuation">.</span><span class="token function">opsForValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>key<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<h2 id="make-the-application-executable">Make the Application Executable</h2>
<p>외부 애플리케이션 서버에 배포하기 위해 이 서비스를 기존 WAR 파일로 패키징할 수 있지만 여기에 표시된 더 간단한 접근 방식을 사용하면 독립 실행형(single executable) 애플리케이션을 만들 수 있습니다. 좋은 오래된 Java <code>main()</code> 메소드에 의해 구동되는 단일 실행 가능한 <code>JAR</code> 파일에 모든 것을 패키지합니다. 그 과정에서 외부 인스턴스에 배포하는 대신 Netty를 HTTP 런타임으로 비동기 "컨테이너(container)"에 내장(embedding)하기 위한 Spring의 지원을 사용합니다.</p>
<p>src/main/java/com/example/demo/DemoApplication.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>demo</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DemoApplication</span> <span class="token punctuation">{</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">DemoApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<h4 id="netty를-http-런타임으로-비동기-컨테이너container에-내장">Netty를 HTTP 런타임으로 비동기 '컨테이너(container)'에 내장</h4>
<ol>
<li>
<p><strong>Netty</strong>:</p>
<ul>
<li>Netty는 네트워크 애플리케이션을 개발하기 위한 Java 기반의 비동기 이벤트 드리븐 네트워크 프레임워크입니다. 네트워크 통신에 관련된 다양한 기능과 기술을 제공하여 비동기 방식으로 빠르고 확장 가능한 네트워크 애플리케이션을 구축할 수 있게 합니다.</li>
</ul>
</li>
<li>
<p><strong>HTTP 런타임으로 내장</strong>:</p>
<ul>
<li>여기서 HTTP 런타임으로 내장되는 것은 Netty가 HTTP 프로토콜을 처리하는 데 사용된다는 것을 의미합니다. Netty는 HTTP 요청을 비동기적이고 논블로킹 방식으로 처리할 수 있는 능력을 갖추고 있습니다.</li>
</ul>
</li>
<li>
<p><strong>비동기 '컨테이너(container)'</strong>:</p>
<ul>
<li>여기서 '컨테이너'는 Netty가 동작하는 환경이라고 생각할 수 있습니다. Netty는 HTTP 요청을 비동기적으로 처리하고 응답을 생성하는 데 사용될 수 있는 런타임 환경을 제공합니다. 이는 일반적인 서블릿 기반의 동기적인 방식보다 비동기적으로 동작하는 컨테이너로 볼 수 있습니다.</li>
</ul>
</li>
</ol>
<p>요약하면, "Netty를 HTTP 런타임으로 비동기 '컨테이너(container)'에 내장"이라는 말은 Netty가 HTTP 프로토콜을 처리하는 비동기적인 환경(컨테이너)에서 동작한다는 것을 의미합니다. Netty를 사용하면 비동기적이고 효율적인 방식으로 HTTP 요청을 처리할 수 있는 환경을 제공할 수 있습니다.</p>
<hr>
<h4 id="용어--런타임-환경--컨테이너--컨텍스트">용어 : 런타임 환경 / 컨테이너 / 컨텍스트</h4>
<ol>
<li>
<p><strong>런타임 환경(Runtime Environment)</strong>:</p>
<ul>
<li>런타임 환경은 어떤 프로그램이 실행될 때 동작하는 환경을 가리킵니다. 이는 특정 프로그래밍 언어나 플랫폼에서 프로그램이 실행되기 위해 필요한 라이브러리, 패키지, 런타임 시스템 등을 포함합니다.</li>
<li>예를 들어, Java 프로그램의 런타임 환경은 JVM(Java Virtual Machine)과 Java 런타임 라이브러리 등을 포함합니다. Python의 경우에는 Python 인터프리터와 관련된 라이브러리가 런타임 환경입니다.</li>
</ul>
</li>
<li>
<p><strong>컨테이너(Container)</strong>:</p>
<ul>
<li>컨테이너는 격리된 환경에서 애플리케이션이 실행되도록 하는 기술을 말합니다. 가상화 기술 중 하나로, 운영체제 수준에서 여러 애플리케이션을 격리하여 실행할 수 있게 해줍니다.</li>
<li>컨테이너는 독립적인 파일 시스템, 환경 변수, 네트워크, CPU, 메모리 등을 갖춘 격리된 환경을 제공하여 애플리케이션을 실행합니다. Docker와 Kubernetes는 컨테이너 기술을 관리하고 배포하는 데 사용되는 대표적인 도구입니다.</li>
</ul>
</li>
<li>
<p><strong>스프링 컨테이너(Spring Container)</strong>:</p>
<ul>
<li>스프링에서는 IoC(Inversion of Control) 컨테이너 또는 스프링 컨테이너라고도 부릅니다. 이는 스프링 프레임워크에서 제공하는 핵심 컨테이너입니다. 스프링 컨테이너는 빈(bean) 객체를 생성하고 관리하며, 의존성 주입(Dependency Injection)을 통해 이들을 연결해줍니다.</li>
</ul>
</li>
<li>
<p><strong>스프링 컨텍스트(Spring Context)</strong>:</p>
<ul>
<li>스프링 컨텍스트는 스프링 애플리케이션에서 객체들 간의 상호작용 및 설정 정보를 가지고 있는 객체입니다. ApplicationContext 인터페이스를 구현한 것이 스프링 컨텍스트입니다. 스프링 컨텍스트는 빈들의 라이프사이클 관리, 빈의 생성 및 관리, 리소스 로딩, 이벤트 발행 등의 작업을 수행합니다.</li>
</ul>
</li>
</ol>
<hr>
<h4 id="tcpip-3-way-handshaking">TCP/IP, 3-way Handshaking</h4>
<p>3-way handshake는 TCP 연결을 설정하는 과정으로, 일반적으로 동기적이고 블로킹되는 작업입니다. </p>
<p>3-way handshake는 TCP 연결을 초기화하기 위해 클라이언트가 서버에게 SYN 패킷을 보내고, 서버가 이에 응답하여 SYN-ACK 패킷을 보내고, 마지막으로 클라이언트가 ACK 패킷을 보내는 과정입니다. 이러한 과정은 일반적으로 동기적이며, 연결이 설정될 때까지 기다리며 블로킹될 수 있습니다.</p>
<p>이 과정은 TCP/IP 프로토콜 스택에서 일어나며, 동기/블로킹 방식으로 작동합니다. 이와 관련하여 비동기적이거나 논블로킹인 HTTP와는 직접적인 연관성은 없습니다. HTTP는 TCP/IP 위에서 동작하며, <strong>TCP 연결 설정을 위한 3-way handshake는 HTTP 요청/응답과는 별개로 TCP/IP 수준에서 처리됩니다.</strong></p>
<hr>
<h2 id="test-the-application">Test the application</h2>
<h3 id="실행-로그">실행 로그</h3>
<pre><code class="language-shell"> :: Spring Boot ::                <span class="token punctuation">(</span>v3.2.1<span class="token punctuation">)</span>

<span class="token number">2024</span>-01-04T13:45:09.770+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> g.s.SpringDataReactiveRedisApplication   <span class="token builtin class-name">:</span> Starting SpringDataReactiveRedisApplication using Java <span class="token number">17.0</span>.9 with PID <span class="token number">11958</span> <span class="token punctuation">(</span>/home/dev-hammy/IdeaProjects/SpringBoot_Guides/spring-data-reactive-redis/build/classes/java/main started by dev-hammy <span class="token keyword">in</span> /home/dev-hammy/IdeaProjects/SpringBoot_Guides/spring-data-reactive-redis<span class="token punctuation">)</span>
<span class="token number">2024</span>-01-04T13:45:09.773+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> g.s.SpringDataReactiveRedisApplication   <span class="token builtin class-name">:</span> No active profile set, falling back to <span class="token number">1</span> default profile: <span class="token string">"default"</span>
<span class="token number">2024</span>-01-04T13:45:10.532+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> .s.d.r.c.RepositoryConfigurationDelegate <span class="token builtin class-name">:</span> Multiple Spring Data modules found, entering strict repository configuration mode
<span class="token number">2024</span>-01-04T13:45:10.534+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> .s.d.r.c.RepositoryConfigurationDelegate <span class="token builtin class-name">:</span> Bootstrapping Spring Data Redis repositories <span class="token keyword">in</span> DEFAULT mode.
<span class="token number">2024</span>-01-04T13:45:10.560+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> .s.d.r.c.RepositoryConfigurationDelegate <span class="token builtin class-name">:</span> Finished Spring Data repository scanning <span class="token keyword">in</span> <span class="token number">11</span> ms. Found <span class="token number">0</span> Redis repository interfaces.
Coffee<span class="token punctuation">(</span>id<span class="token operator">=</span>d2b4ea7f-b872-4c31-b602-0771e2d86cb5, <span class="token assign-left variable">name</span><span class="token operator">=</span>Black Alert Redis<span class="token punctuation">)</span>
Coffee<span class="token punctuation">(</span>id<span class="token operator">=</span>4439dd73-919c-4f56-8aee-c6df817d5282, <span class="token assign-left variable">name</span><span class="token operator">=</span>Jet Black Redis<span class="token punctuation">)</span>
Coffee<span class="token punctuation">(</span>id<span class="token operator">=</span>66ddf2e9-f094-4931-861b-64214fba032d, <span class="token assign-left variable">name</span><span class="token operator">=</span>Darth Redis<span class="token punctuation">)</span>
<span class="token number">2024</span>-01-04T13:45:12.182+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> o.s.b.web.embedded.netty.NettyWebServer  <span class="token builtin class-name">:</span> Netty started on port <span class="token number">8080</span>
<span class="token number">2024</span>-01-04T13:45:12.195+09:00  INFO <span class="token number">11958</span> --- <span class="token punctuation">[</span>           main<span class="token punctuation">]</span> g.s.SpringDataReactiveRedisApplication   <span class="token builtin class-name">:</span> Started SpringDataReactiveRedisApplication <span class="token keyword">in</span> <span class="token number">3.028</span> seconds <span class="token punctuation">(</span>process running <span class="token keyword">for</span> <span class="token number">4.32</span><span class="token punctuation">)</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f94db323-851c-4e42-bfc0-f004523cca69/image.png"></p>