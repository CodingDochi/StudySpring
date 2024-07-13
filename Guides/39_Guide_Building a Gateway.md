<h2 id="게이트웨이">게이트웨이</h2>
<p>게이트웨이는 시스템 간의 상호 작용을 관리하고 보안, 통합, 프로토콜 변환 등의 기능을 제공하여 서로 다른 환경 간에 효율적인 통신을 가능하게 합니다.</p>
<ol>
<li><strong>네트워크 게이트웨이(Network Gateway)</strong>: 두 개 이상의 네트워크를 연결하고 데이터를 중계하는 역할을 합니다. 인터넷 게이트웨이(Internet Gateway)도 이에 속합니다.</li>
<li><strong>API 게이트웨이(API Gateway)</strong>: 클라이언트와 마이크로서비스 아키텍처 내의 서비스들 간의 통신을 관리하고 조정합니다. 보안, 인증, 모니터링, 로깅 등의 기능을 제공합니다.</li>
<li><strong>데이터베이스 게이트웨이(Database Gateway)</strong>: 여러 종류의 데이터베이스 시스템 간의 데이터 전송 및 변환을 지원합니다.</li>
</ol>
<h2 id="게이트웨이와-메시지-미들웨어브로커의-차이">게이트웨이와 메시지 미들웨어/브로커의 차이</h2>
<p>게이트웨이와 메시지 미들웨어(또는 메시지 브로커)는 서로 다른 기능과 목적을 갖고 있습니다.</p>
<h3 id="게이트웨이-gateway">게이트웨이 (Gateway):</h3>
<ul>
<li><strong>목적</strong>: 다른 네트워크, 서비스 또는 프로토콜 간의 통신을 중계하고 연결하는 역할을 합니다. 주로 서로 다른 시스템 간의 통신을 용이하게 하기 위해 사용됩니다.</li>
<li><strong>기능</strong>: 데이터를 받아들이고 변환한 후 다른 형식으로 재구성하여 다른 시스템에 전달합니다. 네트워크 간의 통신 프로토콜을 변환하거나, 보안 및 인증을 추가할 수도 있습니다.</li>
</ul>
<h3 id="메시지-미들웨어--메시지-브로커-message-middleware--message-broker">메시지 미들웨어 / 메시지 브로커 (Message Middleware / Message Broker):</h3>
<ul>
<li><strong>목적</strong>: 애플리케이션, 서비스, 시스템 간에 데이터를 안정적으로 교환하고 전송하기 위한 소프트웨어 또는 서비스입니다. 주로 비동기 통신을 위해 사용됩니다.</li>
<li><strong>기능</strong>: 메시지를 수신하고 보관하며, 다른 시스템에 전달합니다. 메시지 큐, 토픽, 이벤트 버스 등을 통해 메시지를 관리하고 전달하는 역할을 합니다. 또한 보안, 메시지 변환, 라우팅 등의 기능도 제공합니다.</li>
</ul>
<p>때때로 게이트웨이는 메시지 미들웨어를 통해 데이터를 전달할 수도 있지만, 두 가지는 서로 다른 목적과 기능을 가지고 있습니다.</p>
<h2 id="resillience4j">Resillience4j</h2>
<p>물론이죠! </p>
<h3 id="resilience4j">Resilience4J</h3>
<p>Resilience4J는 자바 애플리케이션을 위한 내결함성 라이브러리입니다. 이 라이브러리는 분산 시스템에서의 내결함성을 쉽게 구현하고 관리할 수 있도록 다양한 기능을 제공합니다. 주로 다음과 같은 기능들을 포함하고 있습니다:</p>
<ol>
<li><strong>Circuit Breaker (서킷 브레이커)</strong>: 외부 서비스 호출 시 장애가 발생할 경우, 해당 서비스와의 통신을 일시적으로 차단하여 애플리케이션 전체의 부하를 줄입니다.</li>
<li><strong>Retry (재시도)</strong>: 장애가 발생했을 때, 자동으로 재시도하여 장애를 극복하고 서비스의 가용성을 높입니다.</li>
<li><strong>Rate Limiter (요청 제한기)</strong>: 특정 서비스에 대한 요청 속도를 제한하여 너무 많은 요청으로 인한 과부하를 방지합니다.</li>
<li><strong>Bulkhead (벌크헤드)</strong>: 서비스 간의 격리를 통해 장애가 전체 시스템으로 확산되는 것을 방지합니다.</li>
<li><strong>Retry, Timeouts, Fallbacks 등의 기능</strong>: 장애 대응을 위한 다양한 기능을 제공합니다.</li>
</ol>
<h2 id="contract-stub-runner">Contract Stub Runner</h2>
<p>Contract Stub Runner는 Spring Cloud Contract 프로젝트의 일부분입니다. 이를 사용하면 마이크로서비스 간의 통합을 위한 API나 메시지를 설계, 테스트 및 관리할 수 있습니다. </p>
<ul>
<li><strong>Contracts (계약)</strong>: API나 메시지의 협업을 위한 스펙을 작성하고 공유합니다. 이는 Producer(생산자)와 Consumer(소비자) 간의 상호 작용을 정의합니다.</li>
<li><strong>Stub Runner</strong>: 이를 사용하여 소비자의 테스트 환경에서 Producer의 행위를 시뮬레이션할 수 있습니다. Producer가 아직 구현되지 않았더라도 Consumer가 예상되는 동작을 테스트할 수 있습니다.</li>
<li><strong>Pacts (계약서)</strong>: Consumer와 Provider 간의 상호 작용을 기록하고 검증하는데 사용됩니다.</li>
</ul>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Cloud Gateway를 사용하여 게이트웨이를 만들 것입니다. </p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/3809d011-6dc2-4987-8d08-f0a772822b3d/image.png"></p>
<h3 id="buildgradle-튜토리얼-샘플">build.gradle [튜토리얼 샘플]</h3>
<pre><code class="language-null">plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.0'
	id 'io.spring.dependency-management' version '1.1.4'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'

java {
	sourceCompatibility = '17'
}

repositories {
	mavenCentral()
	maven { url 'https://repo.spring.io/milestone' }
}

ext {
	set('springCloudVersion', "2023.0.0-RC1")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-resilience4j'
	// tag::dependency[]
	implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-reactor-resilience4j'
	// end::dependency[]
	implementation 'org.springframework.cloud:spring-cloud-starter-gateway'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.cloud:spring-cloud-starter-contract-stub-runner'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h3 id="buildgradle-수정-필요">build.gradle [수정 필요]</h3>
<pre><code class="language-null">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.1'
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
    implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-resilience4j'
    implementation 'org.springframework.cloud:spring-cloud-starter-gateway-mvc'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.cloud:spring-cloud-starter-contract-stub-runner'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<p>튜토리얼에 명시된 대로 스프링 이니셜라이저에서 Testing에서 Contract Stub Runner, Spring Cloud Routing에서 Gateway, Spring Cloud Circuit Breaker에서 Resillence4J를 골라서 생성했지만 큰 문제가 있다.<br>
<a href="https://docs.spring.io/spring-cloud-gateway/reference/spring-cloud-gateway/starter.html">Spring Cloud Gateway</a>는 Spring Boot, Spring WebFlux, Project Reactor 기반 위에 빌드 되었고, Netty 런타임을 사용하므로 전통적 Servelet 컨테이너에서는 작동하지 않는다고 한다. </p>
<p>현재 생성된 빌드 파일에는 gateway가 mvc로 서블릿 환경을 나타내고 있다. </p>
<h3 id="buildgradle-reactive">build.gradle [reactive]</h3>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0c071ca4-4dcf-449e-85ce-0f79d75d400a/image.png"></p>
<pre><code class="language-null">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.1'
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
    implementation 'org.springframework.cloud:spring-cloud-starter-gateway'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'io.projectreactor:reactor-test'
    testImplementation 'org.springframework.cloud:spring-cloud-starter-contract-stub-runner'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<p>튜토리얼 샘플의 빌드 파일에서 보이는 'reactive'가 반영되었다.  </p>
<h2 id="creating-a-simple-route">Creating A Simple Route</h2>
<p>Spring Cloud Gateway는 경로(routes)를 사용하여 다운스트림 서비스에 대한 요청을 처리합니다. 이 가이드에서는 모든 요청을 HTTPBin으로 라우팅합니다. 경로(routes)는 다양한 방법으로 구성할 수 있지만 이 가이드에서는 게이트웨이에서 제공하는 Java API를 사용합니다.</p>
<p>시작하려면 <code>Application.java</code>에서 <code>RouteLocator</code> 유형의 새 <code>Bean</code>을 생성하십시오.<br>
src/main/java/gateway/Application.java</p>
<pre><code class="language-java">    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RouteLocator</span> <span class="token function">myRoutes</span><span class="token punctuation">(</span><span class="token class-name">RouteLocatorBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">routes</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span></code></pre>
<p><code>myRoutes</code> 메소드는 경로(route)를 생성하는 데 사용할 수 있는 <code>RouteLocatorBuilder</code>를 사용합니다. 경로를 생성하는 것 외에도 <code>RouteLocatorBuilder</code>를 사용하면 경로에 조건자(predicates)와 필터(filter)를 추가하여 특정 조건에 따라 핸들(handle)을 라우팅하고 요청/응답을 적절하게 변경할 수 있습니다.</p>
<p>이제 <code>/get</code>의 게이트웨이에 요청이 있을 때 요청을 <code>https://httpbin.org/get</code>으로 라우팅하는 경로를 생성할 수 있습니다. 이 경로 구성에서는 라우팅되기 전에 요청에 <code>World</code> 값이 있는 <code>Hello</code> 요청 헤더를 추가하는 필터를 추가합니다.</p>
<p>src/main/java/gateway/Application.java</p>
<pre><code class="language-java">    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RouteLocator</span> <span class="token function">myRoutes</span><span class="token punctuation">(</span><span class="token class-name">RouteLocatorBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">routes</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
                <span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/get"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">addRequestHeader</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">,</span> <span class="token string">"World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span></code></pre>
<p>간단한 게이트웨이를 테스트하려면 포트 <code>8080</code>에서 <code>Application.java</code>를 실행할 수 있습니다. 애플리케이션이 실행되면 <code>http://localhost:8080/get</code>에 요청하세요. 터미널에서 다음 cURL 명령을 사용하면 됩니다.</p>
<pre><code class="language-shell"><span class="token function">curl</span> http://localhost:8080/get</code></pre>
<hr>
<h4 id="애플리케이션-실행-로그">애플리케이션 실행 로그</h4>
<pre><code class="language-null"> :: Spring Boot ::                (v3.2.1)

2024-01-04T15:10:49.358+09:00  INFO 14791 --- [           main] guide.newgateway.NewGatewayApplication   : Starting NewGatewayApplication using Java 17.0.9 with PID 14791 (/home/dev-hammy/IdeaProjects/SpringBoot_Guides/new-gateway/build/classes/java/main started by dev-hammy in /home/dev-hammy/IdeaProjects/SpringBoot_Guides/new-gateway)
2024-01-04T15:10:49.363+09:00  INFO 14791 --- [           main] guide.newgateway.NewGatewayApplication   : No active profile set, falling back to 1 default profile: "default"
2024-01-04T15:10:50.482+09:00  INFO 14791 --- [           main] o.s.cloud.context.scope.GenericScope     : BeanFactory id=5fc4c573-7123-3fb1-a26b-be8b8e250883
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [After]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Before]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Between]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Cookie]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Header]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Host]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Method]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Path]
2024-01-04T15:10:51.249+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Query]
2024-01-04T15:10:51.250+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [ReadBody]
2024-01-04T15:10:51.250+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [RemoteAddr]
2024-01-04T15:10:51.250+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [XForwardedRemoteAddr]
2024-01-04T15:10:51.250+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Weight]
2024-01-04T15:10:51.250+09:00  INFO 14791 --- [           main] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [CloudFoundryRouteService]
2024-01-04T15:10:51.727+09:00  INFO 14791 --- [           main] o.s.b.web.embedded.netty.NettyWebServer  : Netty started on port 8080
2024-01-04T15:10:51.794+09:00  INFO 14791 --- [           main] guide.newgateway.NewGatewayApplication   : Started NewGatewayApplication in 2.866 seconds (process running for 3.53)</code></pre>
<ul>
<li>
<p>루트 라우팅(Route Routing): <code>Loaded RoutePredicateFactory:</code> 라우트 조건을 나타내는 <code>RoutePredicateFactory</code>들이 로드되었습니다. 예를 들어, <code>Path</code>, <code>Header</code>, <code>Query</code> 등의 라우트 조건들이 설정되어 있음을 나타냅니다.</p>
</li>
<li>
<p>웹 서버 시작: <code>Netty started on port 8080:</code> <code>Netty</code> 웹 서버가 <code>8080</code> 포트에서 성공적으로 시작되었습니다.</p>
</li>
</ul>
<h4 id="실행-결과">실행 결과</h4>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/58e48f00-6f88-4cbb-8eda-9be204abfbda/image.png"></p>
<p>HTTPBin은 <code>World</code> 값이 포함된 <code>Hello</code> 헤더가 요청에서 전송되었음을 보여줍니다.</p>
<h2 id="스프링-클라우드-circuitbreaker-사용">스프링 클라우드 CircuitBreaker 사용</h2>
<p>이제 우리는 좀 더 흥미로운 일을 할 수 있습니다. 게이트웨이 뒤에 있는 서비스는 잠재적으로 제대로 작동하지 않고 클라이언트에 영향을 미칠 수 있으므로 회로 차단기(cirduit breaker)에서 생성한 경로(route)를 래핑해야 할 수도 있습니다. Resilience4J Spring Cloud CircuitBreaker 구현을 사용하여 Spring Cloud Gateway에서 이를 수행할 수 있습니다. 이는 요청에 추가할 수 있는 간단한 필터를 통해 구현됩니다. 이를 보여주기 위해 다른 경로(route)를 만들 수 있습니다.</p>
<p>이 필터를 사용하려면 classpath에 반응형 Resilience4J CircuitBreaker 종속성을 추가해야 합니다.</p>
<h3 id="buildgradle-아까-수정했던">build.gradle [아까 수정했던]</h3>
<pre><code class="language-null">implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-reactor-resilience4j'</code></pre>
<p>다음 예에서는 응답을 보내기 전에 특정 시간 동안 기다리는 HTTPBin의 지연 API를 사용합니다. 이 API는 응답을 보내는 데 시간이 오래 걸릴 수 있으므로 회로 차단기(circuit breaker)에서 이 API를 사용하는 경로를 래핑할 수 있습니다. 다음 목록은 <code>RouteLocator</code> 개체에 새 경로(route)를 추가합니다.</p>
<p>src/main/java/gateway/Application.java</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">RouteLocator</span> <span class="token function">myRoutes</span><span class="token punctuation">(</span><span class="token class-name">RouteLocatorBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">routes</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
            <span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/get"</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">addRequestHeader</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">,</span> <span class="token string">"World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
            <span class="token punctuation">.</span><span class="token function">host</span><span class="token punctuation">(</span><span class="token string">"*.circuitbreaker.com"</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">circuitBreaker</span><span class="token punctuation">(</span>config <span class="token operator">-&gt;</span> config<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"mycmd"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span>
        <span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<ol>
<li>
<p><code>.route</code>는 들어오는 요청을 인식하고, 그에 따라 특정한 규칙을 정하는 역할을 합니다. 람다식 내부에서 사용되는 <code>.path</code>는 URL의 경로를, <code>.host</code>는 요청이 보내진 호스트를 가리킵니다. 예를 들어, <code>.path("/get")</code>는 URL 경로가 <code>/get</code>인 요청을, <code>.host("*.circuitbreaker.com")</code>는 <code>circuitbreaker.com</code> 도메인으로부터 오는 모든 요청을 가리킵니다.</p>
</li>
<li>
<p><code>.uri</code>는 프록시를 설정하는데 사용됩니다. 프록시는 클라이언트가 요청하는 내용을 받아서 대신 서버에 전달하고, 서버의 응답을 다시 클라이언트에게 전달하는 중개 역할을 합니다. 여기서 <code>http://httpbin.org:80</code>은 프록시 대상입니다. 이 설정은 들어오는 요청을 해당 URI로 프록시하여 외부 서버인 <code>http://httpbin.org</code>의 80번 포트에 요청을 보내는 역할을 합니다. <code>httpbin.org</code>는 HTTP 요청을 테스트하기 위한 유용한 도구이며, 해당 요청을 받아서 다양한 형태로 반환하거나 테스트하는 데 사용됩니다.</p>
</li>
</ol>
<hr>
<p>이 새로운 경로(route) 구성과 이전에 만든 경로(route) 구성에는 몇 가지 차이점이 있습니다. 우선, 경로(route) 조건자(predicate) 대신 호스트 조건자(predicate)를 사용합니다. 이는 호스트가 <code>circuitbreaker.com</code>인 한 요청을 HTTPBin으로 라우팅하고 해당 요청을 회로 차단기(circuit breaker)에 래핑한다는 의미입니다. 이를 위해 경로에 필터를 적용합니다. 구성 개체를 사용하여 회로 차단기 필터를 구성할 수 있습니다. 이 예에서는 회로 차단기 이름을 <code>mycmd</code>로 지정합니다.</p>
<p>이제 이 새로운 경로를 테스트할 수 있습니다. 그러기 위해서는 애플리케이션을 시작해야 하는데, 이번에는 <code>/delay/3</code>에 요청을 하도록 하겠습니다. 또한, <code>circuitbreaker.com</code> 호스트가 있는 <code>Host</code> 헤더를 포함하는 것도 중요합니다. 그렇지 않으면 요청이 라우팅되지 않습니다. 다음 cURL 명령을 사용할 수 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> --dump-header - --header <span class="token string">'Host: www.circuitbreaker.com'</span> http://localhost:8080/delay/3</code></pre>
<blockquote>
<p><code>--dump-header</code>를 사용하여 응답 헤더를 확인합니다. <code>-</code> after <code>--dump-header</code>는 cURL에 헤더를 stdout으로 인쇄하도록 지시합니다.</p>
</blockquote>
<p>이 명령을 사용한 후 터미널에 다음이 표시되어야 합니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/7607470a-0b7f-401b-b2be-918fdea9c861/image.png"></p>
<pre><code class="language-null">HTTP/1.1 504 Gateway Timeout
content-length: 0</code></pre>
<hr>
<p>HTTP 응답은 상태 코드와 함께 헤더와 본문(body)로 이루어집니다. 여기서 Content-Length는 본문(body)의 크기를 나타냅니다. 만약 본문이 비어있지만 Content-Length가 127이라고 나온다면, 이는 응답의 헤더에 Content-Length가 잘못 설정되었거나 서버가 본문의 크기를 잘못 계산하여 그렇게 응답하는 것일 수 있습니다.</p>
<p>일반적으로는 본문이 없는 경우 Content-Length 헤더는 0으로 설정됩니다. 하지만 서버 또는 응답을 생성하는 코드가 잘못 설정되어 있거나 예상치 못한 결과가 발생할 수도 있습니다. 가능하다면 서버 코드를 다시 확인하여 응답을 생성하는 부분에 대한 문제를 찾아보는 것이 좋을 것입니다</p>
<hr>
<p>보시다시피 HTTPBin의 응답을 기다리는 동안 회로 차단기(circuit) 시간이 초과되었습니다. 회로 차단기 시간이 초과되면 클라이언트가 <code>504</code>를 수신하지 않고 더 의미 있는 메시지를 수신하도록 선택적으로 대체를 제공할 수 있습니다. 예를 들어 프로덕션 시나리오에서는 캐시에서 일부 데이터를 반환할 수 있지만 간단한 예에서는 대신 본문 <code>fallback</code>를 사용하여 응답을 반환합니다.</p>
<p>이를 위해 회로 차단기 필터를 수정하여 시간 초과 시 호출할 URL을 제공할 수 있습니다.</p>
<hr>
<p><code>fallback</code>은 서킷 브레이커(circuit breaker) 패턴에서 발생하는 실패 시 대체 동작을 정의하는 부분입니다. </p>
<p>서킷 브레이커는 특정 서비스의 장애나 지연으로부터 애플리케이션을 보호하는 데 사용됩니다. 만약 서비스가 지연되거나 장애가 발생하여 서킷 브레이커가 활성화된다면, 서킷 브레이커는 해당 서비스에 대한 요청을 중단하고, 대신 설정된 대체 동작을 실행합니다.</p>
<p>여기서 <code>setFallbackUri("forward:/fallback")</code>은 서킷 브레이커가 작동할 때, <code>*.circuitbreaker.com</code> 호스트에 대한 요청에 대해 미리 정의된 <code>/fallback</code> 경로로의 대체 경로를 설정하는 것입니다. 이는 서비스가 실패했을 때 이 경로로 요청이 전달되어 다른 처리나 에러 메시지를 반환하도록 하는 기능입니다. 예를 들어, 장애 시에 사용자에게 예상치 못한 에러 메시지나 대안적인 데이터를 보여주는 등의 처리를 할 수 있습니다.</p>
<hr>
<p>src/main/java/gateway/Application.java</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">RouteLocator</span> <span class="token function">myRoutes</span><span class="token punctuation">(</span><span class="token class-name">RouteLocatorBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">routes</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
            <span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/get"</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">addRequestHeader</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">,</span> <span class="token string">"World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
            <span class="token punctuation">.</span><span class="token function">host</span><span class="token punctuation">(</span><span class="token string">"*.circuitbreaker.com"</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">circuitBreaker</span><span class="token punctuation">(</span>config <span class="token operator">-&gt;</span> config
                <span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"mycmd"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">setFallbackUri</span><span class="token punctuation">(</span><span class="token string">"forward:/fallback"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 회로 차단기 래핑 경로(route) 시간이 초과되면 게이트웨이 애플리케이션에서 <code>/fallback</code>을 호출합니다. 이제 애플리케이션에 <code>/fallback</code> 엔드포인트를 추가할 수 있습니다.</p>
<p><code>Application.java</code>에서 <code>@RestController</code> 클래스 수준 주석을 추가한 후 다음 <code>@RequestMapping</code>을 클래스에 추가합니다.</p>
<p>src/main/java/gateway/Application.java</p>
<pre><code class="language-null">@SpringBootApplication
@RestController
public class NewGatewayApplication {

//... 중략

@RequestMapping("/fallback")
public Mono<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>String</span><span class="token punctuation">&gt;</span></span> fallback() {
  return Mono.just("fallback");
}</code></pre>
<p>이 새로운 대체 기능을 테스트하려면 애플리케이션을 다시 시작하고 다음 cURL 명령을 다시 실행하십시오.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> --dump-header - --header <span class="token string">'Host: www.circuitbreaker.com'</span> http://localhost:8080/delay/3</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/a0edf8c7-d46e-4bb4-b1ef-d42cc850d770/image.png"></p>
<p>폴백이 적용되면 이제 <code>fallback</code> 응답 본문과 함께 게이트웨이로부터 <code>200</code>을 돌려받는 것을 볼 수 있습니다.</p>
<h2 id="테스트-작성">테스트 작성</h2>
<p>훌륭한 개발자로서 우리는 게이트웨이가 예상대로 작동하는지 확인하기 위해 몇 가지 테스트를 작성해야 합니다. 대부분의 경우, 특히 단위 테스트에서 외부 리소스에 대한 종속성을 제한하려고 하므로 HTTPBin에 의존해서는 안 됩니다. 이 문제에 대한 한 가지 해결책은 경로(route)의 URI를 구성(configurable) 가능하게 만들어 필요한 경우 URI를 변경할 수 있도록 하는 것입니다.</p>
<p>이를 위해 <code>Application.java</code>에서 <code>UriConfiguration</code>이라는 새 클래스를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ConfigurationProperties</span>
<span class="token keyword">class</span> <span class="token class-name">UriConfiguration</span> <span class="token punctuation">{</span>
  
  <span class="token keyword">private</span> <span class="token class-name">String</span> httpbin <span class="token operator">=</span> <span class="token string">"http://httpbin.org:80"</span><span class="token punctuation">;</span>

  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getHttpbin</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> httpbin<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setHttpbin</span><span class="token punctuation">(</span><span class="token class-name">String</span> httpbin<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>httpbin <span class="token operator">=</span> httpbin<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ConfigurationProperties</code>를 활성화하려면 <code>Application.java</code>에 클래스 수준 주석도 추가해야 합니다. </p>
<pre><code class="language-java"><span class="token annotation punctuation">@EnableConfigurationProperties</span><span class="token punctuation">(</span><span class="token class-name">UriConfiguration</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NewGatewayApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">NewGatewayApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RouteLocator</span> <span class="token function">myRoutes</span><span class="token punctuation">(</span><span class="token class-name">RouteLocatorBuilder</span> builder<span class="token punctuation">,</span> <span class="token class-name">UriConfiguration</span> uriConfiguration<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        
        <span class="token class-name">String</span> httpUri <span class="token operator">=</span> uriConfiguration<span class="token punctuation">.</span><span class="token function">getHttpbin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">routes</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
                <span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/get"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">addRequestHeader</span><span class="token punctuation">(</span><span class="token string">"Hello"</span><span class="token punctuation">,</span> <span class="token string">"World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span>httpUri<span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">route</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p
                <span class="token punctuation">.</span><span class="token function">host</span><span class="token punctuation">(</span><span class="token string">"*.circuitbreaker.com"</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">filters</span><span class="token punctuation">(</span>f <span class="token operator">-&gt;</span> f<span class="token punctuation">.</span><span class="token function">circuitBreaker</span><span class="token punctuation">(</span>config <span class="token operator">-&gt;</span> config
                    <span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span><span class="token string">"mycmd"</span><span class="token punctuation">)</span>
                    <span class="token punctuation">.</span><span class="token function">setFallbackUri</span><span class="token punctuation">(</span><span class="token string">"forward:/fallback"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span>httpUri<span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/fallback"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">fallback</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">Mono</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token string">"fallback"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>    </code></pre>
<p>URL을 HTTPBin으로 하드코딩하는 대신 새 구성 클래스에서 URL을 가져옵니다.</p>
<p>이제 src/main/test/java/gateway에 <code>ApplicationTest</code>라는 새 클래스를 만들 수 있습니다. 새 클래스에는 다음 콘텐츠를 추가합니다.</p>
<pre><code class="language-java"></code></pre>
<p>우리의 테스트는 Spring Cloud Contract의 <code>WireMock</code>을 활용하여 HTTPBin의 API를 모의(mock)할 수 있는 서버를 구축합니다. 가장 먼저 주목해야 할 것은 <code>@AutoConfigureWireMock(port = 0)</code>의 사용입니다. 이 주석은 임의의 포트에서 WireMock을 시작합니다.</p>
<p>다음으로 <code>UriConfiguration</code> 클래스를 활용하고 <code>@SpringBootTest</code> 주석의 <code>httpbin</code> 속성을 로컬에서 실행되는 WireMock 서버로 설정합니다. 그런 다음 테스트 내에서 게이트웨이를 통해 호출하는 HTTPBin API에 대한 "스텁"을 설정하고 예상되는 동작을 모의합니다. 마지막으로 <code>WebTestClient</code>를 사용하여 게이트웨이에 요청하고 응답을 검증합니다.</p>
<hr>
<h4 id="테스트-출력">테스트 출력</h4>
<pre><code class="language-null">&gt; Task :compileJava UP-TO-DATE
&gt; Task :processResources UP-TO-DATE
&gt; Task :classes UP-TO-DATE
&gt; Task :compileTestJava
&gt; Task :processTestResources NO-SOURCE
&gt; Task :testClasses
19:56:49.330 [Test worker] INFO org.springframework.test.context.support.AnnotationConfigContextLoaderUtils -- Could not detect default configuration classes for test class [guide.newgateway.NewGatewayApplicationTests]: NewGatewayApplicationTests does not declare any static, non-private, non-final, nested classes annotated with @Configuration.
19:56:49.498 [Test worker] INFO org.springframework.boot.test.context.SpringBootTestContextBootstrapper -- Found @SpringBootConfiguration guide.newgateway.NewGatewayApplication for test class guide.newgateway.NewGatewayApplicationTests

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.2.1)

2024-01-04T19:56:50.178+09:00  INFO 6306 --- [    Test worker] g.newgateway.NewGatewayApplicationTests  : Starting NewGatewayApplicationTests using Java 17.0.9 with PID 6306 (started by dev-hammy in /home/dev-hammy/IdeaProjects/SpringBoot_Guides/new-gateway)
2024-01-04T19:56:50.181+09:00  INFO 6306 --- [    Test worker] g.newgateway.NewGatewayApplicationTests  : No active profile set, falling back to 1 default profile: "default"
2024-01-04T19:56:51.766+09:00  INFO 6306 --- [    Test worker] o.s.cloud.context.scope.GenericScope     : BeanFactory id=fe8dedfa-4f0c-3d57-ba7d-3d2d231b728a
2024-01-04T19:56:53.790+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [After]
2024-01-04T19:56:53.791+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Before]
2024-01-04T19:56:53.791+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Between]
2024-01-04T19:56:53.791+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Cookie]
2024-01-04T19:56:53.791+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Header]
2024-01-04T19:56:53.791+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Host]
2024-01-04T19:56:53.792+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Method]
2024-01-04T19:56:53.792+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Path]
2024-01-04T19:56:53.792+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Query]
2024-01-04T19:56:53.792+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [ReadBody]
2024-01-04T19:56:53.792+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [RemoteAddr]
2024-01-04T19:56:53.793+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [XForwardedRemoteAddr]
2024-01-04T19:56:53.793+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [Weight]
2024-01-04T19:56:53.793+09:00  INFO 6306 --- [    Test worker] o.s.c.g.r.RouteDefinitionRouteLocator    : Loaded RoutePredicateFactory [CloudFoundryRouteService]
2024-01-04T19:56:54.474+09:00  INFO 6306 --- [    Test worker] o.s.b.web.embedded.netty.NettyWebServer  : Netty started on port 34597
2024-01-04T19:56:54.554+09:00  INFO 6306 --- [    Test worker] g.newgateway.NewGatewayApplicationTests  : Started NewGatewayApplicationTests in 4.847 seconds (process running for 6.605)
2024-01-04T19:56:56.004+09:00  INFO 6306 --- [tp1219681220-42] WireMock                                 : Request received:
127.0.0.1 - GET /get

Accept-Encoding: [gzip]
User-Agent: [ReactorNetty/1.1.14]
Accept: [*/*]
WebTestClient-Request-Id: [1]
Hello: [World]
Forwarded: [proto=http;host="localhost:34597";for="127.0.0.1:43158"]
X-Forwarded-For: [127.0.0.1]
X-Forwarded-Proto: [http]
X-Forwarded-Port: [34597]
X-Forwarded-Host: [localhost:34597]
Host: [localhost:12466]
Content-Length: [0]



Matched response definition:
{
  "status" : 200,
  "body" : "{\"headers\":{\"Hello\":\"World\"}}",
  "headers" : {
    "Content-Type" : "application/json"
  }
}

Response:
HTTP/1.1 200
Content-Type: [application/json]
Matched-Stub-Id: [1d77ac92-44db-4bd7-97ff-28dbb11d389b]

2024-01-04T19:56:56.185+09:00  INFO 6306 --- [tp1219681220-24] WireMock                                 : Request received:
127.0.0.1 - GET /delay/3

Accept-Encoding: [gzip]
User-Agent: [ReactorNetty/1.1.14]
Accept: [*/*]
WebTestClient-Request-Id: [2]
Forwarded: [proto=http;host=www.circuitbreaker.com;for="127.0.0.1:43158"]
X-Forwarded-For: [127.0.0.1]
X-Forwarded-Proto: [http]
X-Forwarded-Port: [80]
X-Forwarded-Host: [www.circuitbreaker.com]
Host: [localhost:12466]
Content-Length: [0]



Matched response definition:
{
  "status" : 200,
  "body" : "no fallback",
  "fixedDelayMilliseconds" : 3000
}

Response:
HTTP/1.1 200
Matched-Stub-Id: [71c688c0-c47c-4702-9285-19222ff9cc53]

Java HotSpot(TM) 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended
&gt; Task :test
Deprecated Gradle features were used in this build, making it incompatible with Gradle 9.0.
You can use '--warning-mode all' to show the individual deprecation warnings and determine if they come from your own scripts or plugins.
For more on this, please refer to https://docs.gradle.org/8.5/userguide/command_line_interface.html#sec:command_line_warnings in the Gradle documentation.
BUILD SUCCESSFUL in 40s
4 actionable tasks: 2 executed, 2 up-to-date
오후 7:57:00: Execution finished ':test --tests "guide.newgateway.NewGatewayApplicationTests"'.</code></pre>
<hr>
<p>결과를 보면 테스트 시에 WireMock을 사용하여 더미 서버를 구동하고 있습니다. WireMock은 HTTP 요청을 대신 받아들이고, 정의된 응답을 반환하는 가짜 서버입니다. 이것을 사용하여 서킷 브레이커의 동작을 시뮬레이션하고 있습니다.</p>
<p>첫 번째 요청 <code>/get</code>은 성공적으로 수행되었고, 해당 요청에 대한 응답은 <code>{"headers":{"Hello":"World"}}</code>와 같이 잘 반환되었습니다.</p>
<p>두 번째 요청 <code>/delay/3</code>은 서킷 브레이커 시뮬레이션을 위해 지연을 시뮬레이션하는 요청입니다. 이 경우 서버에서 <code>no fallback</code>이라는 응답을 반환하여 서킷 브레이커의 동작을 확인하고 있습니다.</p>
<p>이 테스트는 서킷 브레이커의 기능을 테스트하기 위한 것으로 보입니다. WireMock을 사용하여 특정 요청에 대해 어떻게 응답하고, 서킷 브레이커가 작동하는지 확인하고 있는 것으로 보입니다.</p>