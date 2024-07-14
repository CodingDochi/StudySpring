<h2 id="로드밸런서">로드밸런서</h2>
<p>로드 밸런서는 네트워크 트래픽을 여러 서버 또는 리소스에 균등하게 분산시켜주는 기능을 합니다. 주로 높은 가용성과 성능 향상을 위해 사용됩니다.</p>
<p>로드 밸런서는 대개 서버 측에서 구성되며, 클라이언트는 로드 밸런서의 IP 주소 또는 호스트명을 통해 요청을 전송합니다. 로드 밸런서는 이 요청을 받아서 여러 서버로 분산시키는 역할을 수행합니다. 이를 통해 특정 서버에 너무 많은 부하가 걸리는 것을 방지하고, 여러 서버 간의 부하를 균형있게 분산시켜줍니다.</p>
<p>게이트웨이와 로드 밸런서는 비슷한 역할을 수행하는 것처럼 보일 수 있지만, 주요한 차이점이 있습니다. 게이트웨이는 주로 마이크로서비스 아키텍처에서 서비스 간의 통신을 관리하고, 보안, 라우팅, 프로토콜 변환 등을 수행하는 데 중점을 둡니다. 반면 로드 밸런서는 트래픽을 여러 서버로 분산시켜 성능을 최적화하는 데 중점을 둡니다.</p>
<p>서킷 브레이커는 다르게 작동합니다. 이는 주로 장애나 지연된 서비스에서 요청을 차단하여 전체 시스템의 성능을 보호하는 데 사용됩니다. 서킷 브레이커는 일시적으로 서비스에 대한 요청을 차단하고, 대신에 대체 동작이나 에러를 반환하여 네트워크 문제로부터 보호합니다. 로드 밸런서는 트래픽을 균등하게 분산시켜 성능을 최적화하는 반면, 서킷 브레이커는 서비스의 안정성과 신뢰성을 유지하는 데 집중합니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Cloud LoadBalancer를 사용하여 다른 마이크로서비스 호출 시 클라이언트 측 로드 밸런싱을 제공하는 마이크로서비스 애플리케이션을 구축합니다.</p>
<h2 id="create-a-root-project">Create a Root Project</h2>
<p>이 가이드에서는 두 개의 프로젝트를 빌드하는 과정을 안내하며, 그 중 하나는 다른 프로젝트에 종속됩니다. 따라서 루트 프로젝트 아래에 두 개의 하위 프로젝트를 만들어야 합니다. 먼저 최상위 수준에서 빌드 구성을 만듭니다.</p>
<p>Gradle의 경우 동일한 디렉터리를 포함하는 <code>settings.gradle</code>이 필요합니다.</p>
<pre><code class="language-null">rootProject.name = 'spring-cloud-lodadbalancer'

include 'say-hello'
include 'user'</code></pre>
<h3 id="buildgradle-say-hello">build.gradle [say-hello]</h3>
<pre><code class="language-groovy">plugins {
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

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h3 id="buildgradle-user">build.gradle [user]</h3>
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
	implementation 'org.springframework.cloud:spring-cloud-starter-loadbalancer'
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
}</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5facacb5-4076-4240-8745-30f70c5a463c/image.png"></p>
<h2 id="implement-the-say-hello-service">Implement the "Say Hello" service</h2>
<p>우리의 "서버" 서비스는 <code>Say Hello</code>라고 합니다. <code>/greeting</code>에서 액세스할 수 있는 엔드포인트에서 무작위 인사말(3개의 static 목록에서 선택)을 반환합니다.</p>
<p><code>src/main/java/hello</code>에서 <code>SayHelloApplication.java</code> 파일을 생성합니다.</p>
<p>다음 목록은 <code>say-hello/src/main/java/hello/SayHelloApplication.java</code>의 내용을 보여줍니다.</p>
<pre><code class="language-java"></code></pre>
<p>이는 <code>/greeting</code>에 대한 <code>@RequestMapping</code> 메소드와 루트 경로 <code>/</code>에 대한 <code>@RequestMapping</code> 메소드가 있는 간단한 <code>@RestController</code>입니다.</p>
<p>우리는 클라이언트 서비스 애플리케이션과 함께 이 애플리케이션의 여러 인스턴스를 로컬로 실행할 것입니다. 시작하려면:</p>
<ol>
<li>
<p><code>src/main/resources</code> 디렉터리를 만듭니다.</p>
</li>
<li>
<p>디렉터리 내에 <code>application.yml</code> 파일을 만듭니다.</p>
</li>
<li>
<p>해당 파일에서 <code>server.port</code>의 기본값을 설정하십시오.</p>
</li>
</ol>
<p>(우리는 응용 프로그램의 다른 인스턴스가 다른 포트에서 실행되도록 지시하여 해당 응용 프로그램을 실행할 때 <code>Say Hello</code> 인스턴스 중 어느 것도 클라이언트와 충돌하지 않도록 할 것입니다.) 이 파일에 있는 동안 서비스에 대한 <code>spring.application.name</code>도 설정할 수 있습니다.</p>
<p>다음 목록은 <code>say-hello/src/main/resources/application.yml</code>의 내용을 보여줍니다.</p>
<pre><code class="language-yaml"><span class="token key atrule">spring</span><span class="token punctuation">:</span>
  <span class="token key atrule">application</span><span class="token punctuation">:</span>
    <span class="token key atrule">name</span><span class="token punctuation">:</span> say<span class="token punctuation">-</span>hello

<span class="token key atrule">server</span><span class="token punctuation">:</span>
  <span class="token key atrule">port</span><span class="token punctuation">:</span> <span class="token number">8090</span></code></pre>
<hr>
<h4 id="별동의-스레드-또는-프로세스-vs-별도의-인스턴스">별동의 스레드 또는 프로세스 vs 별도의 인스턴스</h4>
<p>HTTP 요청마다 별도의 스레드 또는 프로세스를 사용하는 것과 여러 개의 애플리케이션 인스턴스를 실행하는 것 사이에는 몇 가지 구조적인 차이가 있습니다.</p>
<ol>
<li>
<p><strong>단일 인스턴스 내에서 스레드 또는 프로세스 처리</strong>:</p>
<ul>
<li>단일 인스턴스 내에서 요청을 처리하는 경우, 해당 인스턴스 내에서 스레드 또는 프로세스로 요청을 동시에 처리합니다. 이는 다중 스레드 또는 비동기 프로그래밍 기법을 사용하여 동시성을 달성할 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>여러 인스턴스 간 부하 분산</strong>:</p>
<ul>
<li>여러 개의 인스턴스를 실행하면 각각의 인스턴스는 독립적으로 실행되며, 서로 다른 포트에서 동작합니다. 이는 로드 밸런서 또는 다른 부하 분산 기술을 사용하여 들어오는 요청을 여러 인스턴스로 분산시킬 수 있습니다. 이는 서비스의 확장성을 높이고, 특정 인스턴스에 발생한 장애의 영향을 최소화할 수 있습니다.</li>
</ul>
</li>
</ol>
<p>단일 인스턴스 내에서 다중 스레드 또는 비동기 방식으로 요청을 처리하는 것과 여러 인스턴스를 실행하여 부하를 분산하는 것은 각각의 장단점이 있습니다.</p>
<ul>
<li>
<p><strong>단일 인스턴스 내에서 다중 스레드 또는 비동기 처리의 장점</strong>:</p>
<ul>
<li>메모리 공유와 같은 자원 접근이 용이합니다.</li>
<li>컨텍스트 전환의 오버헤드가 적습니다.</li>
<li>간단한 구현이 가능합니다.</li>
</ul>
</li>
<li>
<p><strong>여러 인스턴스 실행의 장점</strong>:</p>
<ul>
<li>부하 분산과 확장성이 용이합니다.</li>
<li>고가용성과 장애 격리가 가능합니다.</li>
<li>각각의 인스턴스가 서로 영향을 받지 않고 별도로 관리될 수 있습니다.</li>
</ul>
</li>
</ul>
<p>따라서, 각각의 방식은 서로 다른 상황에서 유용할 수 있습니다. 요청마다 별도의 스레드 또는 프로세스를 사용하여 처리할 때도 로드 밸런싱과 같은 방식을 통해 부하를 분산할 수 있지만, 여러 인스턴스를 실행함으로써 이를 더 쉽게 관리하고 효과적으로 확장할 수 있습니다.</p>
<hr>
<h2 id="access-from-a-client-service">Access from a Client Service</h2>
<p>사용자에게는 <code>User</code> 애플리케이션이 표시됩니다. 인사말을 받기 위해 <code>Say Hello</code> 애플리케이션을 호출(call)한 다음 사용자가 <code>/hi</code> 및 <code>/hello</code>의 엔드포인트를 방문할 때 해당 인사말을 사용자에게 보냅니다.</p>
<p>사용자 애플리케이션 디렉터리의 <code>src/main/java/hello</code> 아래에 <code>UserApplication.java</code> 파일을 추가합니다.</p>
<p>다음 목록은 <code>user/src/main/java/hello/UserApplication.java</code>의 내용을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token class-name">Builder</span> loadBalancedWebClientBuilder<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">ReactorLoadBalancerExchangeFilterFunction</span> lbFunction<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">UserApplication</span><span class="token punctuation">(</span><span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token class-name">Builder</span> webClientBuilder<span class="token punctuation">,</span>
						   <span class="token class-name">ReactorLoadBalancerExchangeFilterFunction</span> lbFunction<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>loadBalancedWebClientBuilder <span class="token operator">=</span> webClientBuilder<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>lbFunction <span class="token operator">=</span> lbFunction<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">UserApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/hi"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">hi</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"Mary"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> loadBalancedWebClientBuilder<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://say-hello/greeting"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">bodyToMono</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>greeting <span class="token operator">-&gt;</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"%s, %s!"</span><span class="token punctuation">,</span> greeting<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Mono</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">hello</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"name"</span><span class="token punctuation">,</span> defaultValue <span class="token operator">=</span> <span class="token string">"John"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>lbFunction<span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uri</span><span class="token punctuation">(</span><span class="token string">"http://say-hello/greeting"</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">bodyToMono</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>greeting <span class="token operator">-&gt;</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token function">format</span><span class="token punctuation">(</span><span class="token string">"%s, %s!"</span><span class="token punctuation">,</span> greeting<span class="token punctuation">,</span> name<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	
<span class="token punctuation">}</span></code></pre>
<hr>
<ol>
<li><strong>WebClient</strong>:<ul>
<li>Spring WebFlux 프레임워크에서 비동기적인 HTTP 통신을 위해 사용됩니다.</li>
<li>기존의 RestTemplate과 달리 논블로킹(non-blocking) 방식으로 동작하며, Reactor의 Publisher를 이용하여 리액티브 스트림(Reactive Streams)을 처리합니다.</li>
<li>네트워크 호출을 비동기적으로 처리하고, Reactor 프로젝트의 Mono 또는 Flux로 결과를 반환합니다.<ul>
<li>예를 들어, 단일 HTTP 요청에 대한 응답은 단일 값이므로 Mono를 사용하여 처리할 수 있습니다. 반면에 여러 개의 데이터를 가져오거나 스트림으로 처리해야 할 경우에는 Flux를 사용할 수 있습니다.</li>
</ul></li>
</ul></li>
</ol>
<p>1.1 Mono:</p>
<ul>
<li>0 또는 1개의 결과를 반환할 때 사용됩니다.</li>
<li>예를 들어, 단일 객체의 반환이나 비동기 작업의 단일 결과를 처리할 때 Mono를 사용합니다.</li>
</ul>
<p>1.2 Flux:</p>
<ul>
<li>0부터 N개의 결과를 반환할 때 사용됩니다.</li>
<li>여러 값을 스트림으로 처리하고, 비동기 작업이 여러 값을 반환하는 경우에 Flux를 사용합니다.</li>
</ul>
<ol start="2">
<li>
<p><strong>WebClient.Builder</strong>:</p>
<ul>
<li>WebClient 인스턴스를 생성하기 위한 빌더 클래스입니다.</li>
<li>Spring Boot 애플리케이션에서 WebClient를 사용할 때 사용됩니다.</li>
<li>기본적으로 제공되는 메서드를 이용하여 WebClient를 구성하고 빌드합니다.</li>
</ul>
</li>
<li>
<p><strong>ReactorLoadBalancerExchangeFilterFunction</strong>:</p>
<ul>
<li>이 클래스는 WebClient에 적용되는 ExchangeFilterFunction으로, 리액티브 WebClient를 사용하는 동안 서비스 디스커버리와 부하 분산을 위해 사용됩니다.</li>
<li>별도의 서비스 디스커버리 미들웨어를 사용하지 않더라도, 클라이언트 측에서는 주로 프록시를 사용하여 서비스 디스커버리를 수행합니다. 이는 클라이언트가 직접적으로 서비스 위치를 알 필요 없이, 프록시를 통해 요청을 보내고 해당 프록시에서 적절한 서비스로 라우팅하게 됩니다.</li>
<li>로드 밸런서로의 라우팅은 서비스 디스커버리에서 선택된 서비스 인스턴스 중 하나를 선택하여 요청을 보내는 것을 의미합니다. 이는 로드 밸런서에게 요청을 보내는 것이며, 각 서비스 인스턴스는 고유한 IP를 가지게 됩니다. 로드 밸런서는 이러한 다수의 서비스 인스턴스 중 하나를 선택하여 요청을 보내는 역할을 합니다.</li>
</ul>
</li>
</ol>
<hr>
<p><code>hi</code> 메서드와 <code>hello</code> 메서드에서 모두 부하 분산 기능이 설정되어 있습니다. <code>WebClient.Builder</code>는 이미 부하 분산이 설정된 WebClient를 빌드하기 위한 빌더이고, <code>WebClient.builder()</code>는 새로운 WebClient를 생성하고 거기에 부하 분산 기능을 추가하는 것입니다.</p>
<ol>
<li>
<p><strong>WebClient.Builder</strong>:</p>
<ul>
<li><code>WebClient.Builder</code>를 사용하여 부하 분산이 설정되면, 이는 <code>Ribbon</code>과 같은 라이브러리를 이용하여 서비스 디스커버리와 부하 분산 기능을 활용하고 있는 것으로 이해할 수 있습니다.</li>
<li>Spring Cloud Netflix는 <code>Ribbon</code>을 통합하여 부하 분산을 수행할 수 있도록 지원하며, 이를 위해 <code>@LoadBalanced</code> 어노테이션이나 <code>ReactorLoadBalancerExchangeFilterFunction</code>과 같은 구성을 제공합니다.</li>
</ul>
</li>
<li>
<p><strong>WebClient.builder()</strong>:</p>
<ul>
<li><code>WebClient.builder()</code>는 새로운 WebClient를 생성하기 위한 정적 팩토리 메서드입니다. 이 메서드를 통해 새로운 WebClient를 만들 수 있으며, 해당 WebClient에는 특정한 구성이 없습니다.</li>
<li><code>filter(lbFunction)</code>을 사용하여 여기에서도 부하 분산 기능을 추가하고 있습니다. 이것이 부하 분산 설정을 하는 부분입니다.</li>
</ul>
</li>
</ol>
<hr>
<p>또한 로드 밸런싱된 <code>WebClient.Builder</code> 인스턴스를 설정하는 <code>@Configuration</code> 클래스도 필요합니다. 다음 목록은 <code>user/src/main/java/hello/WebClientConfig.java</code>의 내용을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client<span class="token punctuation">.</span>loadbalancer</span><span class="token punctuation">.</span><span class="token class-name">LoadBalanced</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>loadbalancer<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">LoadBalancerClient</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>reactive<span class="token punctuation">.</span>function<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">WebClient</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@LoadBalancerClient</span><span class="token punctuation">(</span>name <span class="token operator">=</span> <span class="token string">"say-hello"</span><span class="token punctuation">,</span> configuration <span class="token operator">=</span> <span class="token class-name">SayHelloConfiguration</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebClientConfig</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@LoadBalanced</span>
  <span class="token annotation punctuation">@Bean</span>
  <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token class-name">Builder</span> <span class="token function">webClientBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>구성(configuration)은 누군가 <code>UserApplication.java</code>의 <code>hi</code> 엔드포인트에 도달할 때 사용하는 <code>@LoadBalanced WebClient.Builder</code> 인스턴스를 제공합니다. <code>hi</code> 엔드포인트에 도달하면 이 빌더를 사용하여 <code>Say Hello</code> 서비스의 URL에 대한 HTTP <code>GET</code> 요청을 만들고 결과를 <code>String</code>로 제공하는 <code>WebClient</code> 인스턴스를 생성합니다.</p>
<p><code>UserApplication.java</code>에는 동일한 작업을 수행하는 <code>/hello</code> 엔드포인트도 추가했습니다. 그러나 <code>@LoadBalanced</code> 주석을 사용하는 대신 프로그래밍 방식으로 구축한 <code>WebClient</code> 인스턴스에 <code>filter()</code> 메서드를 사용하여 전달하는 <code>@Autowired</code> 로드 밸런서 교환 필터 함수(<code>lbFunction</code>)를 사용합니다.</p>
<blockquote>
<p>두 엔드포인트에 대해 로드 밸런싱된 <code>WebClient</code> 인스턴스를 약간 다르게 설정하더라도 두 엔드포인트의 최종 동작은 완전히 동일합니다. Spring Cloud LoadBalancer는 <code>Say Hello</code> 서비스의 적절한 인스턴스를 선택하는 데 사용됩니다.</p>
</blockquote>
<p><code>spring.application.name</code> 및 <code>server.port</code> 속성을 <code>src/main/resources/application.properties</code> 또는 <code>src/main/resources/application.yml</code>에 추가합니다.</p>
<p>다음 목록은 <code>user/src/main/resources/application.yml</code> 내용을 보여줍니다.</p>
<pre><code class="language-yml"><span class="token key atrule">spring</span><span class="token punctuation">:</span>
  <span class="token key atrule">application</span><span class="token punctuation">:</span>
    <span class="token key atrule">name</span><span class="token punctuation">:</span> user

<span class="token key atrule">server</span><span class="token punctuation">:</span>
  <span class="token key atrule">port</span><span class="token punctuation">:</span> <span class="token number">8888</span></code></pre>
<h2 id="say-hello-configuration">Say Hello Configuration</h2>
<pre><code class="language-java"><span class="token keyword">package</span> hello<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Arrays</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">List</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">reactor<span class="token punctuation">.</span>core<span class="token punctuation">.</span>publisher</span><span class="token punctuation">.</span><span class="token class-name">Flux</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">DefaultServiceInstance</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">ServiceInstance</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>loadbalancer<span class="token punctuation">.</span>core</span><span class="token punctuation">.</span><span class="token class-name">ServiceInstanceListSupplier</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Primary</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * @author Olga Maciaszek-Sharma
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SayHelloConfiguration</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token annotation punctuation">@Primary</span>
  <span class="token class-name">ServiceInstanceListSupplier</span> <span class="token function">serviceInstanceListSupplier</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">DemoServiceInstanceListSuppler</span><span class="token punctuation">(</span><span class="token string">"say-hello"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

<span class="token punctuation">}</span>

<span class="token keyword">class</span> <span class="token class-name">DemoServiceInstanceListSuppler</span> <span class="token keyword">implements</span> <span class="token class-name">ServiceInstanceListSupplier</span> <span class="token punctuation">{</span>

  <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> serviceId<span class="token punctuation">;</span>

  <span class="token class-name">DemoServiceInstanceListSuppler</span><span class="token punctuation">(</span><span class="token class-name">String</span> serviceId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>serviceId <span class="token operator">=</span> serviceId<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getServiceId</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> serviceId<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Override</span>
  <span class="token keyword">public</span> <span class="token class-name">Flux</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">List</span><span class="token punctuation">&lt;</span><span class="token class-name">ServiceInstance</span><span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span></span> <span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token class-name">Flux</span><span class="token punctuation">.</span><span class="token function">just</span><span class="token punctuation">(</span><span class="token class-name">Arrays</span>
        <span class="token punctuation">.</span><span class="token function">asList</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">DefaultServiceInstance</span><span class="token punctuation">(</span>serviceId <span class="token operator">+</span> <span class="token string">"1"</span><span class="token punctuation">,</span> serviceId<span class="token punctuation">,</span> <span class="token string">"localhost"</span><span class="token punctuation">,</span> <span class="token number">8090</span><span class="token punctuation">,</span> <span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
            <span class="token keyword">new</span> <span class="token class-name">DefaultServiceInstance</span><span class="token punctuation">(</span>serviceId <span class="token operator">+</span> <span class="token string">"2"</span><span class="token punctuation">,</span> serviceId<span class="token punctuation">,</span> <span class="token string">"localhost"</span><span class="token punctuation">,</span> <span class="token number">9092</span><span class="token punctuation">,</span> <span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
            <span class="token keyword">new</span> <span class="token class-name">DefaultServiceInstance</span><span class="token punctuation">(</span>serviceId <span class="token operator">+</span> <span class="token string">"3"</span><span class="token punctuation">,</span> serviceId<span class="token punctuation">,</span> <span class="token string">"localhost"</span><span class="token punctuation">,</span> <span class="token number">9999</span><span class="token punctuation">,</span> <span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>해당 클래스에서는 <code>Say Hello</code> 서비스를 호출하는 동안 Spring Cloud LoadBalancer가 선택하는 세 개의 하드 코딩된 인스턴스가 있는 사용자 정의 <code>ServiceInstanceListSupplier</code>를 제공합니다.</p>
<blockquote>
<p>이 단계는 Spring Cloud LoadBalancer에 사용자 정의 구성을 전달하는 방법을 설명하기 위해 추가되었습니다. 그러나 <code>@LoadBalancerClient</code> 주석을 사용하고 LoadBalancer에 대한 자체 구성을 생성할 필요는 없습니다. 가장 일반적인 방법은 서비스 검색(discovery)과 함께 Spring Cloud LoadBalancer를 사용하는 것입니다. classpath에 <code>DiscoveryClient</code>가 있는 경우 기본 Spring Cloud LoadBalancer 구성은 이를 사용하여 서비스 인스턴스를 확인합니다. 결과적으로 실행 중인 인스턴스 중에서만 선택하게 됩니다.</p>
</blockquote>