<p>이 가이드는 Spring Cloud Circuit Breaker를 사용하여 잠재적으로 실패하는 메서드 호출에 회로 차단기를 적용하는 과정을 안내합니다.</p>
<hr>
<p>fallback은 일반적으로 프로그램이 예상치 못한 오류 상황에 대비하여 대체 동작을 수행하는 메커니즘을 말합니다. 주로 네트워크 호출, 데이터베이스 연결, 외부 서비스 호출 등과 같은 상황에서 사용됩니다.</p>
<p>스프링 프레임워크에서는 서킷 브레이커 패턴과 함께 사용되며, 서킷 브레이커가 네트워크 호출 등의 작업을 모니터링하고, 지정된 임계값을 초과할 경우에 서킷을 열어 해당 호출을 차단하는 것과 달리, fallback은 오류 발생 시 대체되는 동작을 제공합니다.</p>
<p>일반적으로 fallback은 기본값을 반환하거나, 예상 가능한 값으로 대체하는 등의 방식으로 구현됩니다. 이는 사용자가 예상치 못한 오류 상황에서도 프로그램이 종료되지 않고 대체 동작을 수행하여 안정성을 유지할 수 있도록 도와줍니다.</p>
<hr>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>메서드 호출이 실패할 때 기능을 단계적으로 저하(gracefully degrade functionality)시키기 위해 회로 차단기 패턴을 사용하는 마이크로서비스 애플리케이션을 구축합니다. 회로 차단기 패턴을 사용하면 관련 서비스에 오류가 발생해도 마이크로서비스가 계속 작동하여 오류가 연속적으로 발생하는 것을 방지하고 오류가 발생한 서비스에 복구할 시간을 제공할 수 있습니다.</p>
<hr>
<p>Server 프로그램인 bookstore와 client 프로그램인 reading 모듈을 추가합니다. bookstore는 의존성으로 Spring Reactive Web을 추가하고 reading은 의존성으로 SpringReactive Web과 Resillience4J를 추가합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5d0e039e-9a2d-4c26-8552-0cec3e70d645/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/f32778f8-216f-49c1-9780-40c0bf31d4a7/image.png"></p>
<hr>
<h2 id="set-up-a-server-microservice-application">Set up a Server Microservice Application</h2>
<p>Bookstore 서비스에는 단일 엔드포인트가 있습니다. <code>/recommended</code>에서 액세스할 수 있으며 (단순화를 위해) 권장 읽기 목록을 <code>String</code>으로된 <code>Mono</code>로 반환합니다.</p>
<p><code>BookstoreApplication.java</code>의 기본 클래스는 다음과 같습니다.</p>
<p>bookstore/src/main/java/hello/BookstoreApplication.java</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BookstoreApplication</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"/recommended"</span><span class="token punctuation">)</span>
  <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">readingList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token class-name">Mono</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token string">"Spring in Action (Manning), Cloud Native Java (O'Reilly), Learning Spring Boot (Packt)"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">BookstoreApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@RestController</code> 주석은 <code>@Controller</code>처럼 <code>BookstoreApplication</code>을 컨트롤러 클래스로 표시하고 이 클래스의 <code>@RequestMapping</code> 메서드가 <code>@ResponseBody</code>로 주석이 달린 것처럼 동작하도록 보장합니다. 즉, 이 클래스의 <code>@RequestMapping</code> 메소드의 반환 값은 원래 유형에서 적절하게 자동으로 변환되어 응답 본문에 직접 기록됩니다.</p>
<p>이 애플리케이션을 클라이언트 서비스 애플리케이션과 함께 로컬로 실행하려면 <code>src/main/resources/application.properties</code>에서 Bookstore 서비스가 클라이언트와 충돌하지 않도록 <code>server.port</code>를 설정합니다.</p>
<p><code>bookstore/src/main/resources/application.properties</code></p>
<pre><code class="language-null">server.port=8090</code></pre>
<h2 id="set-up-a-client-microservice-application">Set up a Client Microservice Application</h2>
<p>Reading 애플리케이션은 Bookstore 애플리케이션의 프런트 엔드입니다. <code>/to-read</code>에서 읽기 목록을 볼 수 있으며 해당 읽기 목록은 Bookstore 서비스 애플리케이션에서 검색됩니다. <code>reading/src/main/java/hello/ReadingApplication.java</code></p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">WebClient</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ReadingApplication</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/to-read"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">toRead</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8090/recommended"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">bodyToMono</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ReadingApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>bookstore에서 list을 얻으려면 Spring의 <code>WebClient</code> 클래스를 사용합니다. <code>WebClient</code>는 우리가 제공하는 Bookstore 서비스의 URL에 대해 HTTP GET 요청을 한 다음 결과를 <code>String</code>으로 된<code>Mono</code>로 반환합니다. (Spring을 사용하여 WebClient를 사용하여 RESTful 서비스를 사용하는 방법에 대한 자세한 내용은 반응형 RESTful 웹 서비스 구축 가이드를 참조하세요.)</p>
<p><code>src/main/resources/application.properties</code>에 <code>server.port</code> 속성을 추가합니다.</p>
<p>reading/src/main/resources/application.properties</p>
<pre><code class="language-null">server.port=8080</code></pre>
<p>이제 브라우저에서 읽기 애플리케이션의 <code>/to-read</code> 엔드포인트에 액세스하여 읽기 목록을 볼 수 있습니다. 그러나 우리는 Bookstore 애플리케이션에 의존하기 때문에 문제가 발생하거나 Reading 애플리케이션이 Bookstore에 액세스할 수 없는 경우 목록이 없으며 사용자는 불쾌한 HTTP <code>500</code> 오류 메시지를 받게 됩니다.</p>
<h2 id="apply-the-circuit-breaker-pattern">Apply The Circuit Breaker Pattern</h2>
<p>Spring Cloud의 Circuit Breaker 라이브러리는 회로 차단기 패턴의 구현을 제공합니다. 회로 차단기에서 메서드 호출을 래핑하면 Spring Cloud 회로 차단기는 해당 메서드에 대한 호출 실패를 감시하고, 오류가 지정된 임계값까지 누적되면(if failures build up to a specified threshold) Spring Cloud 회로 차단기는 후속 호출이 자동으로 실패하도록 회로를 엽니다. 회로가 열려 있는 동안 Spring Cloud 회로 차단기는 호출을 메서드로 리디렉션하고 지정된 fallback 메서드로 전달됩니다.</p>
<p>Spring Cloud Circuit Breaker는 Resilience4J, Hystrix, Sentinal 및 Spring Retry를 포함한 다양한 회로 차단기 구현을 지원합니다. 이 가이드에서는 Resilience4J 구현을 사용합니다. 이 구현을 사용하려면 애플리케이션의 classpath에 <code>spring-cloud-starter-circuitbreaker-reactor-resilience4j</code>를 추가해야 합니다.</p>
<h3 id="readingbuildgradle">reading/build.gradle</h3>
<pre><code class="language-null">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.2'
    id 'io.spring.dependency-management' version '1.1.4'
}

group = 'guide'
version = '0.0.1-SNAPSHOT'

java {
    sourceCompatibility = '17'
}

repositories {
    mavenCentral()
}

ext {
    set('springCloudVersion', "2023.0.0")
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-webflux'
    implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-reactor-resilience4j'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'io.projectreactor:reactor-test'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}
</code></pre>
<p>Spring Cloud Circuit Breaker는 애플리케이션을 위한 새로운 회로 차단기를 생성하는 데 사용할 수 있는 <code>ReactiveCircuitBreakerFactory</code>라는 인터페이스를 제공합니다. 이 인터페이스의 구현은 애플리케이션의 classpath에 있는 스타터를 기반으로 자동 구성됩니다. 이제 이 인터페이스를 사용하여 Bookstore 애플리케이션에 대한 API 호출을 수행하는 새로운 서비스를 만들 수 있습니다.</p>
<p>reading/src/main/java/hello/BookService.java</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client<span class="token punctuation">.</span>circuitbreaker</span><span class="token punctuation">.</span><span class="token class-name">ReactiveCircuitBreaker</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client<span class="token punctuation">.</span>circuitbreaker</span><span class="token punctuation">.</span><span class="token class-name">ReactiveCircuitBreakerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Service</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">WebClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BookService</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> LOG <span class="token operator">=</span> <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">BookService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">WebClient</span> webClient<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ReactiveCircuitBreaker</span> readingListCircuitBreaker<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">BookService</span><span class="token punctuation">(</span><span class="token class-name">ReactiveCircuitBreakerFactory</span> circuitBreakerFactory<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>webClient <span class="token operator">=</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">baseUrl</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8090"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>readingListCircuitBreaker <span class="token operator">=</span> circuitBreakerFactory<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token string">"recommended"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">readingList</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> readingListCircuitBreaker<span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span>webClient<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"recommended"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">bodyToMono</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">,</span> throwable <span class="token operator">-&gt;</span> <span class="token punctuation">{</span> LOG<span class="token punctuation">.</span><span class="token function">warn</span><span class="token punctuation">(</span><span class="token string">"Error making request to book service"</span><span class="token punctuation">,</span> throwable<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token class-name">Mono</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token string">"Cloud Native Java (O'Reilly)"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<ol>
<li>
<p><code>WebClient</code>는 리액티브한 방식으로 HTTP 요청을 만들고 응답을 받는 데 사용되는 클래스입니다. 주요 사용 방법은 다음과 같습니다:</p>
<ul>
<li><code>WebClient.builder()</code>를 사용하여 <code>WebClient.Builder</code>를 생성합니다.</li>
<li><code>baseUrl()</code> 메서드를 사용하여 기본 URL을 설정합니다.</li>
<li><code>build()</code> 메서드를 호출하여 <code>WebClient</code> 인스턴스를 생성합니다.</li>
<li>생성된 <code>WebClient</code> 인스턴스를 사용하여 다양한 HTTP 메서드(GET, POST 등) 및 요청 구성을 수행합니다.</li>
<li>요청을 보내고 응답을 처리하기 위해 다양한 메서드 체이닝을 사용합니다. 예를 들어, <code>get()</code>, <code>uri()</code>, <code>retrieve()</code>, <code>bodyToMono()</code> 등의 메서드를 사용하여 요청을 구성하고 응답을 처리합니다.</li>
</ul>
</li>
<li>
<p><code>ReactiveCircuitBreakerFactory</code>는 스프링 클라우드의 서킷 브레이커를 생성하기 위한 팩토리입니다. 이 클래스는 주입된 <code>circuitBreakerFactory</code>를 사용하여 서킷 브레이커를 생성합니다. 생성자에서 이를 참조할 수 있는 이유는 해당 클래스가 외부에서 주입되는 의존성으로 사용되기 때문입니다. 따라서 외부에서 주입되는 의존성을 통해 이를 참조할 수 있습니다.</p>
</li>
<li>
<p><code>WebClient.builder().build()</code>는 기본적인 <code>WebClient</code> 인스턴스를 생성합니다. 반면에 <code>WebClient.builder().baseUrl().build()</code>는 기본 URL을 설정한 후에 <code>WebClient</code> 인스턴스를 생성합니다. 즉, 두 번째 방법은 기본 URL을 설정한 상태로 <code>WebClient</code> 인스턴스를 생성합니다.</p>
</li>
<li>
<p><code>throwable</code>은 예외를 나타내는 변수입니다. 스프링에서 <code>run()</code> 메서드의 두 번째 매개변수로 전달된 람다식에서 예외가 발생할 경우 해당 예외를 <code>throwable</code> 변수로 받아 처리할 수 있습니다. 보통은 이를 사용하여 예외가 발생했을 때의 처리 로직을 정의합니다.</p>
</li>
<li>
<p><code>readingList()</code> 메서드에서 서킷 브레이커를 실행한 후, <code>run()</code> 메서드의 두 번째 매개변수로 전달된 람다식 안에서 예외가 발생한 경우에 대한 처리를 수행합니다. 이 때, <code>throwable</code>을 사용하여 예외를 받아와 처리하고, <code>return Mono.just("Cloud Native Java (O'Reilly)")</code>를 사용하여 기본값을 반환합니다. 이러한 사용 패턴은 스프링 컨텍스트를 새로 만들어내는 것이 아니라, 해당 메서드의 일부로서 예외 처리를 수행하는 것입니다. 스프링은 리액티브한 방식으로 이러한 예외 처리를 허용하며, 이를 통해 장애 처리 및 회복 메커니즘을 쉽게 구현할 수 있습니다.</p>
</li>
</ol>
<hr>
<p><code>ReactiveCircuitBreakerFactory</code>에는 새로운 회로 차단기를 생성하는 데 사용할 수 있는 <code>create</code>라는 단일 메서드가 있습니다. 회로 차단기가 있으면 <code>run</code>을 호출하기만 하면 됩니다. <code>run</code>에는 <code>Mono</code> 또는 <code>Flux</code>와 선택적 <code>Function</code>이 사용됩니다. 선택적 <code>Function</code> 매개변수는 문제가 발생할 경우 대체 역할을 합니다. 샘플에서 폴백은 <code>String</code> <code>Cloud Native Java(O'Reilly)</code>를 포함하는 <code>Mono</code>를 반환합니다.</p>
<p>새로운 서비스가 준비되면 <code>ReadingApplication</code>의 코드를 업데이트하여 이 새로운 서비스를 사용할 수 있습니다.</p>
<p>reading/src/main/java/hello/ReadingApplication.java</p>
<pre><code class="language-java">mport org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">WebClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Mono</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ReadingApplication</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">BookService</span> bookService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/to-read"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">toRead</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> bookService<span class="token punctuation">.</span><span class="token function">readingList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ReadingApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Bookstore 서비스와 Reading 서비스를 모두 실행한 다음 <code>localhost:8080/to-read</code>에서 Reading 서비스에 대한 브라우저를 엽니다. 전체 권장 읽기 목록이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/577e2570-d621-4aea-963c-ca2b060f8399/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/6df66a28-9218-4cc3-bf09-41d4a3b8b81f/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/4d104e81-7416-41cf-840f-8783c61f1d04/image.png"></p>
<pre><code class="language-null">Spring in Action (Manning), Cloud Native Java (O'Reilly), Learning Spring Boot (Packt)</code></pre>
<p>이제 Bookstore 애플리케이션을 종료합니다. 목록 소스는 사라졌지만 Hystrix와 Spring Cloud Netflix 덕분에 공백을 메울 수 있는 신뢰할 수 있는 축약 목록이 생겼습니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e80a9ad1-df9b-4b96-b73b-f92293c2be28/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ef61223a-66a9-422e-b831-09aca80b64d5/image.png"></p>
<pre><code class="language-null">Cloud Native Java (O'Reilly)</code></pre>