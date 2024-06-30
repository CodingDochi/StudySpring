<p>이 가이드는 Spring 관리 빈에서 캐싱을 활성화하는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>간단한 책 저장소에서 캐싱을 활성화하는 애플리케이션을 구축합니다.</p>
<h2 id="start-with-spring-initializr">Start with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0880a523-7003-4efd-9cfb-7a85b4ef9344/image.png"></p>
<h2 id="책-모델-만들기">책 모델 만들기</h2>
<p>먼저 책에 대한 간단한 모델을 만들어야 합니다. 다음 목록(src/main/java/guides/caching/Book.java)은 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">BookRepository</span> <span class="token punctuation">{</span>
    
    <span class="token class-name">Book</span> <span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token class-name">String</span> isbn<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
<span class="token punctuation">}</span></code></pre>
<p><code>{SpringData}[Spring Data]</code>를 사용하여 광범위한 SQL 또는 NoSQL 저장소에 대한 리포지토리 구현을 제공할 수 있었습니다. 그러나 이 가이드에서는 일부 대기 시간(네트워크 서비스, 느린 지연 또는 기타 문제)을 시뮬레이션하는 단순한 구현을 사용합니다. 다음 목록(src/main/java/guides/caching/SimpleBookRepository.java)은 이러한 저장소를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleBookRepository</span> <span class="token keyword">implements</span> <span class="token class-name">BookRepository</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">Book</span> <span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token class-name">String</span> isbn<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">simulateSlowService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Book</span><span class="token punctuation">(</span>isbn<span class="token punctuation">,</span> <span class="token string">"Some book"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token comment">// Don't do this at home</span>
    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">simulateSlowService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token keyword">long</span> time <span class="token operator">=</span> <span class="token number">3000L</span><span class="token punctuation">;</span>
            <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span>time<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">InterruptedException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p><code>simulateSlowService</code>는 각 <code>getByIsbn</code> 호출에 의도적으로 3초 지연을 삽입합니다. 나중에 캐싱을 사용하여 이 예제의 속도를 높일 것입니다.</p>
<h2 id="저장소-사용">저장소 사용</h2>
<p>다음으로 저장소를 연결하고 이를 사용하여 일부 책에 액세스해야 합니다. 다음 목록(src/main/java/guides/caching/CachingApplication.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CachingApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">CachingApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<p>또한 <code>BookRepository</code>를 삽입하고 다른 인수로 여러 번 호출하는 <code>CommandLineRunner</code>가 필요합니다. 다음 목록(src/main/java/guides/caching/AppRunner.java)은 해당 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppRunner</span> <span class="token keyword">implements</span> <span class="token class-name">CommandLineRunner</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> logger <span class="token operator">=</span>
            <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">AppRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">BookRepository</span> bookRepository<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">AppRunner</span><span class="token punctuation">(</span><span class="token class-name">BookRepository</span> bookRepository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>bookRepository <span class="token operator">=</span> bookRepository<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"..... Fetching books"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-1234 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-1234"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-4567 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-4567"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-1234 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-1234"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-4567 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-4567"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-1234 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-1234"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        logger<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"isbn-1234 --&gt;"</span> <span class="token operator">+</span> bookRepository<span class="token punctuation">.</span><span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token string">"isbn-1234"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>이 시점에서 응용 프로그램을 실행하려고 하면 동일한 책을 여러 번 검색하더라도 속도가 상당히 느린 것을 알 수 있습니다. 다음 샘플 출력은 (의도적으로 끔찍한) 코드에서 생성된 3초 지연을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c1886b08-2db1-4980-95ce-c9195b164e10/image.png"></p>
<p>캐싱을 활성화하면 상황을 개선할 수 있습니다.</p>
<h2 id="캐싱-활성화">캐싱 활성화</h2>
<p>이제 책이 책 캐시 내에 캐시되도록 <code>SimpleBookRepository</code>에서 캐싱을 활성화할 수 있습니다. 다음 목록(src/main/java/guides/caching/SimpleBookRepository.java)은 저장소 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cache<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Cacheable</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Component</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleBookRepository</span> <span class="token keyword">implements</span> <span class="token class-name">BookRepository</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token annotation punctuation">@Cacheable</span><span class="token punctuation">(</span><span class="token string">"books"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Book</span> <span class="token function">getByIsbn</span><span class="token punctuation">(</span><span class="token class-name">String</span> isbn<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">simulateSlowService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Book</span><span class="token punctuation">(</span>isbn<span class="token punctuation">,</span> <span class="token string">"Some book"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">// Don't do this at home</span>
    <span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">simulateSlowService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            <span class="token keyword">long</span> time <span class="token operator">=</span> <span class="token number">3000L</span><span class="token punctuation">;</span>
            <span class="token class-name">Thread</span><span class="token punctuation">.</span><span class="token function">sleep</span><span class="token punctuation">(</span>time<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">InterruptedException</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이제 다음 예제(src/main/java/guides/caching/CachingApplication.java)에서 수행 방법을 보여주듯이 캐싱 주석 처리를 활성화해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>caching</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cache<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">EnableCaching</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@EnableCaching</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CachingApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">CachingApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<p><code>@EnableCaching</code> 주석은 모든 Spring Bean을 검사하여 공용 메서드에 캐싱 주석이 있는지 검사하는 사후 프로세서(post-processor)를 트리거합니다. 이러한 주석이 발견되면 메서드 호출을 가로채고 그에 따라 캐싱 동작을 처리하기 위해 프록시가 자동으로 생성됩니다.</p>
<p>사후 프로세서는 <code>@Cacheable</code>, <code>@CachePut</code> 및 <code>@CacheEvict</code> 주석을 처리합니다. 자세한 내용은 Javadoc 및 참조 가이드를 참조하세요.</p>
<hr>
<ul>
<li><code>@Cacheable</code>: 메서드의 결과를 캐시에 저장하고, 이후 같은 파라미터로 호출될 때 캐시된 결과를 반환합니다.</li>
<li><code>@CachePut</code>: 메서드를 호출하고 그 결과를 캐시에 업데이트합니다. 기존 캐시 항목을 갱신합니다.</li>
<li><code>@CacheEvict</code>: 지정된 캐시 항목을 제거합니다. 메서드 호출 후 캐시를 비웁니다.</li>
</ul>
<hr>
<p>Spring Boot는 관련 캐시에 대한 공급자 역할을 하도록 적합한 <code>CacheManager</code>를 자동으로 구성합니다. 자세한 내용은 Spring Boot 설명서를 참조하세요.</p>
<p>우리 샘플은 특정 캐싱 라이브러리를 사용하지 않으므로 캐시 저장소는 <code>ConcurrentHashMap</code>을 사용하는 간단한 대체입니다. 캐싱 추상화는 광범위한 캐시 라이브러리를 지원하며 JSR-107(JCache)과 완벽하게 호환됩니다.</p>
<hr>
<ul>
<li><code>CacheManager</code>: 스프링이 캐시 관리를 담당하는 인터페이스입니다. 캐시 관련 동작을 수행하며, 실제 캐시 구현체와의 상호 작용을 담당합니다.</li>
<li><code>ConcurrentHashMap</code>: 스프링 캐시 추상화에서 가장 간단한 캐시 저장소 중 하나입니다. 실제로는 간단한 메모리 기반의 해시 맵으로, 캐시 데이터를 메모리에 보관합니다.<br>
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&amp;fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbs83dv%2Fbtr727xPNog%2FDYkLxCaP99KWAPdiwRGiBk%2Fimg.png"></li>
</ul>
<hr>
<h4 id="1-hashmap">1. <strong>HashMap</strong>:</h4>
<ul>
<li><strong>멀티쓰레드 환경에서 안전하지 않습니다.</strong></li>
<li>동기화를 제공하지 않기 때문에 여러 쓰레드가 동시에 HashMap을 수정하면 예측 불가능한 결과가 발생할 수 있습니다. 동시성 문제를 해결하기 위해 별도의 동기화 메커니즘이 필요합니다.</li>
</ul>
<h4 id="2-hashtable">2. <strong>Hashtable</strong>:</h4>
<ul>
<li><strong>동기화된 자료구조입니다.</strong></li>
<li>모든 메서드에 대해 Synchronized 키워드가 적용되어 있어 여러 쓰레드가 동시에 접근하여 수정할 수 있습니다. 이로 인해 안전하지만 동기화 비용이 높아 성능이 떨어질 수 있습니다.</li>
</ul>
<h4 id="3-concurrenthashmap">3. <strong>ConcurrentHashMap</strong>:</h4>
<ul>
<li><strong>스레드 안전한 자료구조이며, 성능이 우수합니다.</strong></li>
<li>내부적으로 세분화된 잠금(Lock Striping) 기법을 사용하여 맵을 여러 부분으로 분할하여 동시에 여러 스레드가 맵을 읽고 쓸 수 있습니다. 이로써 동시성을 향상시키고 전체 맵을 잠그지 않고도 안전하게 동작합니다.</li>
</ul>
<h4 id="4-linkedhashmap">4. <strong>LinkedHashMap</strong>:</h4>
<ul>
<li><strong>내부적으로는 단일 쓰레드에서 더 효율적으로 동작합니다.</strong></li>
<li>연결된 리스트로 데이터를 저장하여 삽입 순서를 보존합니다. 멀티쓰레드 환경에서 동기화를 제공하지 않으므로 동시에 여러 쓰레드가 접근하면 안전하지 않습니다.</li>
</ul>
<hr>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>이제 캐싱이 활성화되었으므로 애플리케이션을 다시 실행하고 동일한 ISBN이 있거나 없는 추가 호출을 추가하여 차이점을 확인할 수 있습니다. 그것은 큰 변화를 가져올 것입니다. 다음 목록은 캐싱이 활성화된 출력을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d6d4a6ab-c934-47ea-9267-3d97174168a2/image.png"></p>
<p>이전 샘플 출력에서 책을 처음 검색하는 데는 여전히 3초가 걸립니다. 그러나 동일한 책에 대한 두 번째 및 그 이후의 시간은 훨씬 빠르며 이는 캐시가 해당 작업을 수행하고 있음을 나타냅니다.</p>