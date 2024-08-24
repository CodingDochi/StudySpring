<p>이 가이드는 Spring Cloud Config Server에서 구성을 시작하고 사용하는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>구성 서버(Config Server)를 설정하고 시작 시 구성을 사용한(consume) 다음 클라이언트를 다시 시작하지 않고 구성을 새로 고치는(refresh) 클라이언트를 구축합니다.</p>
<h3 id="요약">요약</h3>
<p>이 코드와 설정 파일은 Spring Cloud Config를 사용하여 외부 Git 저장소에서 설정을 가져와 클라이언트 애플리케이션에 제공하는 예제입니다.</p>
<ol>
<li>
<p><strong>configuration-service/resources/application.properties</strong>:</p>
<ul>
<li>Config 서비스의 포트를 8888로 설정합니다.</li>
<li>Spring Cloud Config 서버가 사용할 Git 저장소의 URI를 <code>${HOME}/Desktop/config</code>로 지정합니다.</li>
</ul>
</li>
<li>
<p><strong>configuration-client/resources/application.properties</strong>:</p>
<ul>
<li>이 클라이언트 애플리케이션의 이름을 <code>a-bootiful-client</code>로 설정합니다.</li>
<li>외부 구성 서버에서 설정을 가져오기 위해 <code>spring.config.import</code> 속성을 사용합니다. 여기서는 <code>configserver</code>의 HTTP 엔드포인트인 <code>http://localhost:8888/</code>을 사용하여 설정을 가져오도록 설정합니다.</li>
<li>모든 management 엔드포인트를 노출하도록 설정됩니다.</li>
</ul>
</li>
<li>
<p><strong>home/config/a-bootiful-client.properties</strong>:</p>
<ul>
<li>외부 Git 저장소에 위치한 <code>a-bootiful-client.properties</code> 파일입니다.</li>
<li>이 파일은 실제 설정을 포함하며, 여기서는 <code>message=Hello world</code>로 정의된 간단한 메시지를 가지고 있습니다.</li>
</ul>
</li>
<li>
<p><strong>configuration-service/ConfigurationServiceApplication.java</strong>:</p>
<ul>
<li>Config 서비스를 구성하기 위한 메인 애플리케이션 클래스입니다.</li>
<li><code>@EnableConfigServer</code> 어노테이션을 사용하여 Config 서버를 활성화합니다.</li>
</ul>
</li>
<li>
<p><strong>configuration-client/ConfigurationClientApplication.java</strong>:</p>
<ul>
<li>클라이언트 애플리케이션의 메인 애플리케이션 클래스입니다.</li>
<li><code>@RefreshScope</code>를 사용하여 새로운 설정을 동적으로 적용할 수 있도록 설정됩니다.</li>
<li><code>MessageRestController</code> 클래스는 <code>/message</code> 엔드포인트를 제공하며, 해당 엔드포인트로 요청이 오면 <code>message</code> 값을 반환합니다.</li>
</ul>
</li>
</ol>
<p>이 설정은 <code>configuration-service</code>가 외부 Git 저장소에서 설정을 읽어와 <code>configuration-client</code>에게 제공하고, <code>configuration-client</code>는 해당 설정을 사용하여 애플리케이션을 구성합니다. 클라이언트 애플리케이션에서 <code>/message</code> 엔드포인트에 접근하면 <code>message</code> 값을 반환하는데, 여기서는 외부 설정에 정의된 <code>Hello world</code> 메시지를 반환할 것입니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/266c8a9e-d4f8-41bc-9078-a375a6e9fa1e/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1da48e25-9b8a-46ad-91e7-ca91d17ac90d/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9baed73f-88d7-4258-9194-993afc52a427/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/7708a805-64c8-432d-ab9c-b4693bed24f2/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1d71bf38-b4a2-4074-ba0c-7a0ba0f14659/image.png"></p>
<h3 id="buildgradle-configuration-client">build.gradle (configuration-client)</h3>
<pre><code class="language-null">plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.1'
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

ext {
	set('springCloudVersion', "2023.0.0")
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-actuator'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.cloud:spring-cloud-starter-config'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}</code></pre>
<h3 id="buildgradle-configuration-service">build.gradle (configuration-service)</h3>
<pre><code class="language-null">plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.1'
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

ext {
    set('springCloudVersion', "2023.0.0")
}

dependencies {
    implementation 'org.springframework.cloud:spring-cloud-config-server'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h2 id="stand-up-a-config-server-구성-서버-구축">Stand up a Config Server 구성 서버 구축</h2>
<p>먼저 Spring 애플리케이션과 (일반적으로) 버전 제어 구성 파일 저장소 사이에서 일종의 중개자 역할을 하는 구성 서비스가 필요합니다. Spring Cloud의 <code>@EnableConfigServer</code>를 사용하여 다른 애플리케이션과 통신할 수 있는 구성 서버를 가동할 수 있습니다. 이는 구성 서버를 활성화하기 위해 하나의 주석이 추가된 일반 Spring Boot 애플리케이션입니다. 다음 목록(configuration-service/src/main/java/guides/configurationservice/ConfigurationServiceApplication.java의)은 이러한 애플리케이션을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>configurationservice</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>config<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">EnableConfigServer</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@EnableConfigServer</span>
<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConfigurationServiceApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ConfigurationServiceApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>구성 서버는 관리할 저장소를 알아야 합니다. 여기에는 몇 가지 선택 사항이 있지만 Git 기반 파일 시스템 저장소로 시작하십시오. 구성 서버를 Github 또는 GitLab 저장소로 쉽게 지정할 수 있습니다. </p>
<p>파일 시스템에서 새 디렉터리를 만들고 그 디렉터리에서 <code>git init</code>을 실행합니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/51183d76-ac43-4c20-8b84-fd4ba8349da7/image.png"></p>
<p>그런 다음 <code>a-bootiful-client.properties</code>라는 파일을 Git 리포지토리에 추가합니다. </p>
<p>그런 다음 <code>git commit</code>을 실행하십시오. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/90f64466-eedb-483c-980e-60f656357f98/image.png"></p>
<p>나중에 <code>spring.application.name</code> 속성이 Config Server에 대한 <code>a-bootiful-client</code>로 식별되는 Spring Boot 애플리케이션을 사용하여 Config Server에 연결합니다. 이는 구성 서버가 특정 클라이언트에 보낼 구성 세트를 아는 방법(어떤 구성 세트를 보내야 할 지)입니다. </p>
<p>또한 Git 리포지토리에 있는 <code>application.properties</code> 또는 <code>application.yml</code>이라는 파일의 모든 값을 보냅니다. 보다 구체적으로 명명된 파일(예: <code>a-bootiful-client.properties</code>)의 속성 키는 <code>application.properties</code> 또는 <code>application.yml</code>의 속성 키를 재정의합니다.</p>
<p>새로 생성된 <code>a-bootiful-client.properties</code> 파일에 간단한 속성과 값(<code>message = Hello world</code>)을 추가한 다음 <code>git commit</code>이 변경 사항을 커밋합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/0d133f6f-8de0-4ebe-a81e-f8d835f51dfa/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c9683b71-fb66-4e8d-9a1d-8e0d1bec281d/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/74f9991f-2ec6-43e8-8077-b1d870a22616/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c730fa33-c34e-46ce-8123-402f7a37872c/image.png"></p>
<p><code>configuration-service/src/main/resources/application.properties</code>에서 <code>spring.cloud.config.server.git.uri</code> 속성을 지정하여 Git 리포지토리 경로를 지정합니다.</p>
<p>또한 이 서버와 다른 Spring Boot 애플리케이션을 동일한 시스템에서 실행할 때 포트 충돌을 방지하려면 다른 <code>server.port</code> 값을 지정해야 합니다. 다음 목록(<code>configuration-service/src/main/resources/application.properties</code>)은 그러한 <code>application.properties</code> 파일을 보여줍니다.</p>
<pre><code class="language-null">server.port=8888

spring.cloud.config.server.git.uri=${HOME}/Desktop/config</code></pre>
<p>이 예에서는 <code>${HOME}/Desktop/config</code>에 있는 파일 기반 <code>git</code> 저장소를 사용합니다. 새 디렉터리를 만들고 그 안에 있는 속성과 YAML 파일에 대해 <code>git commit</code>을 실행하여 쉽게 만들 수 있습니다. 다음 명령 세트가 이 작업을 수행합니다.</p>
<hr>
<p>저는 <code>spring.cloud.config.server.git.uri=https://github.com/Dev-Hammy/config</code> 라고 입력했습니다.</p>
<hr>
<pre><code class="language-null">$ cd ~/Desktop/config
$ find .
./.git
...
./application.yml</code></pre>
<p>또는 애플리케이션의 구성 파일(Configuration file)을 대신 가리키도록 변경하는 경우 원격 Git 저장소(예: Github)를 사용할 수 있습니다.</p>
<h2 id="reading-configuration-from-the-config-server-by-using-the-config-client">Reading Configuration from the Config Server by Using the Config Client</h2>
<p>이제 구성 서버를 구축했으므로 구성 서버를 사용하여 자체 구성을 로드하고 JVM을 다시 시작하지 않고 필요에 따라 구성 서버에 대한 변경 사항을 반영하도록 구성을 새로 고치는(refresh) 새로운 Spring Boot 애플리케이션을 구축해야 합니다. 이렇게 하려면 <code>org.springframework.cloud:spring-cloud-starter-config</code> 종속성을 추가하여 구성 서버에 연결하세요. Spring은 <code>application.properties</code>나 <code>application.yml</code> 또는 다른 <code>PropertySource</code>에서 로드된 속성 파일과 마찬가지로 구성 속성 파일을 봅니다.</p>
<p>Config Client를 구성하는 속성은 Spring Boot 애플리케이션에 대한 일반적인 방법으로 설정할 수 있습니다. 클라이언트의 <code>spring.application.name</code>을 <code>a-bootiful-client</code>로 지정하고 <code>configuration-client/src/main/resources/application.properties</code>에 구성 서버(<code>spring.config.import</code>)의 위치를 지정합니다. 다음 목록은 해당 파일을 보여줍니다.</p>
<p><code>configuration-client/src/main/resources/application.properties</code></p>
<pre><code class="language-null">spring.application.name=a-bootiful-client
spring.config.import=optional:configserver:http://localhost:8888/
management.endpoints.web.exposure.include=*</code></pre>
<p>또한 동적 구성 변경을 보여주기 위해 <code>/refresh</code> 엔드포인트를 활성화하려고 합니다. 위 목록은 <code>management.endpoints.web.exposure.include</code> 속성을 통해 이를 수행하는 방법을 보여줍니다.</p>
<p>클라이언트는 기존 메커니즘(예: <code>@ConfigurationProperties</code> 또는 <code>@Value("${…​}")</code> 또는 <code>Environment</code> 추상화를 통해)을 사용하여 구성 서버의 모든 값에 액세스할 수 있습니다. 이제 해결된(resolved) 메시지 속성 값을 반환하는 Spring MVC REST 컨트롤러를 생성해야 합니다. Spring MVC 및 Spring Boot를 사용하여 REST 서비스를 구축하는 방법에 대해 자세히 알아보려면 RESTful 웹 서비스 구축 가이드를 참조하세요.</p>
<p>기본적으로 구성 값은 클라이언트 시작 시 읽혀지며 다시 읽혀지지 않습니다. Spring Cloud Config <code>@RefreshScope</code>로 <code>MessageRestController</code>에 주석을 추가한 다음 새로 고침(refresh) 이벤트를 트리거하여 Bean이 구성을 새로 고치도록(즉, 구성 서버에서 업데이트된 값을 가져오도록) 강제할 수 있습니다. 다음 목록(<code>configuration-client/src/main/java/guides/configurationclient/ConfigurationClientApplication.java</code>)은 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>cofigurationclient</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Value</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>context<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RefreshScope</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CofigurationClientApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">CofigurationClientApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span>

<span class="token annotation punctuation">@RefreshScope</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">class</span> <span class="token class-name">MessageRestController</span> <span class="token punctuation">{</span>
	
	<span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${message:Hello default}"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> message<span class="token punctuation">;</span>
	
	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/message"</span><span class="token punctuation">)</span>
	<span class="token class-name">String</span> <span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>message<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>먼저 구성 서비스(Config-service)를 시작한 다음 서비스가 실행되면 클라이언트(Config-client)를 시작하여 엔드투엔드 결과를 테스트할 수 있습니다. </p>
<ul>
<li>
<p>config-service 시작<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/b0eb579c-2e1e-45e8-befe-ad488652717a/image.png"></p>
</li>
<li>
<p>config-client 시작<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/860cf0d7-e536-4920-9612-7f62bdd03bdb/image.png"></p>
</li>
</ul>
<p><code>http://localhost:8080/message</code>의 브라우저에서 클라이언트 앱을 방문하세요. 거기에서 응답에 <code>Hello world</code>가 표시되어야 합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c968c43a-8df2-4fdc-b30b-679b4215815e/image.png"></p>
<p>Git 저장소에 있는 <code>a-bootiful-client.properties</code> 파일의 메시지 키를 다른 것으로 변경합니다(<code>Hello Spring!</code>, 아마도?). </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1a484f31-44d3-4971-9b96-c9fdd89bf935/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/30175251-a408-4cd5-b1a1-541486199807/image.png"></p>
<p><code>http://localhost:8888/a-bootiful-client/default</code>를 방문하여 구성 서버에 변경 사항이 표시되는지 확인할 수 있습니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/15f1889e-b2f9-4ab7-a18d-c0f5fe6ee733/image.png"></p>
<pre><code class="language-json">{"name":"a-bootiful-client","profiles":["default"],"label":null,"version":"9e1d3524aa84d5cf9f3a662d34a4286064178756","state":null,"propertySources":[{"name":"https://github.com/Dev-Hammy/config/a-bootiful-client.properties","source":{"message":"Hello Spring!"}}]}</code></pre>
<p>클라이언트가 강제로 자체를 새로 고치고 새 값을 가져오도록 하려면 새로 고침(<code>refresh</code>) Spring Boot Actuator 엔드포인트를 호출(invoke)해야 합니다. Spring Boot의 Actuator는 애플리케이션에 대한 운영(operational) 엔드포인트(예: 상태 확인 및 환경 정보)를 노출합니다. 이를 사용하려면 클라이언트 애플리케이션의 classpath에 <code>org.springframework.boot:spring-boot-starter-actuator</code>를 추가해야 합니다. </p>
<p>다음 명령은 액추에이터의 새로 고침 명령을 호출합니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> localhost:8080/actuator/refresh -d <span class="token punctuation">{</span><span class="token punctuation">}</span> -H <span class="token string">"Content-Type: application/json"</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e0b86388-e2ef-4720-850d-c34db2711867/image.png"></p>
<p>빈(empty) HTTP <code>POST</code>를 클라이언트의 새로 고침 (<code>refresh</code>) 엔드포인트(<code>http://localhost:8080/actuator/refresh</code>)으로 보내 새로 고침 액추에이터 끝점을 호출할 수 있습니다.</p>
<hr>
<p>제 경우는 이 작업 실행시 브라우저에서 white label을 출력합니다.</p>
<hr>
<blockquote>
<p>테스트하기 쉽도록 클라이언트 애플리케이션에서 <code>management.endpoints.web.exposure.include=*</code>를 설정했습니다(Spring Boot 2.0부터 Actuator 엔드포인트는 기본적으로 노출되지 않습니다). 기본적으로 플래그를 설정하지 않으면 JMX를 통해 계속 액세스할 수 있습니다.</p>
</blockquote>
<p>그런 다음 <code>http://localhost:8080/message</code> 엔드포인트를 방문하여 제대로 작동하는지 확인할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fbf0d887-7c42-4a7f-9a99-d9cdc82983c2/image.png"></p>