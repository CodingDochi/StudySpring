<h2 id="getting-started-with-spring-cloud-stream">Getting Started with Spring Cloud Stream</h2>
<p>이 가이드는 Spring Cloud Stream의 개요와 이벤트 기반 스트리밍 애플리케이션을 생성하는 프로세스를 안내합니다.</p>
<h3 id="buildgradle">build.gradle</h3>
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
    implementation 'org.springframework.boot:spring-boot-starter-amqp'
    implementation 'org.springframework.cloud:spring-cloud-stream'
    implementation 'org.springframework.cloud:spring-cloud-stream-binder-rabbit'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.amqp:spring-rabbit-test'
    testImplementation 'org.springframework.cloud:spring-cloud-stream-test-binder'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}</code></pre>
<h3 id="스프링-클라우드-스트림이란">스프링 클라우드 스트림이란</h3>
<p>실시간 스트림 처리를 위한 이벤트 기반 Spring Boot 마이크로서비스를 구축하기 위한 프레임워크입니다.</p>
<h3 id="스트림-처리-애플리케이션-개요">스트림 처리 애플리케이션 개요</h3>
<p>이 가이드는 Spring Cloud Stream의 기능을 보여줍니다. Spring Cloud Stream의 다양한 기능을 보여주기 위해 세 가지 애플리케이션을 만듭니다.</p>
<ul>
<li>
<p><code>name-source</code>: 정기적으로 <code>String</code>을 게시(publish)하여 스트림을 시작합니다. 이 예에서는 이름을 <code>String</code>로 게시합니다.</p>
</li>
<li>
<p><code>name-processor</code>: <code>name-source</code>로 게시된 <code>String</code>을 사용하고 어떤 방식으로든 데이터를 변환합니다. 결과를 다른 거래소(<code>exchange</code>)에 게시합니다. 이 예에서는 <code>String</code>이라는 이름을 사용하여 레코드를 생성하고 타임스탬프를 추가합니다.</p>
</li>
<li>
<p><code>name-sink</code>: 네임 프로세서의 결과를 사용하고 작업을 수행(perform the action)합니다. 이 경우 결과를 표준 출력으로 인쇄하십시오.</p>
</li>
</ul>
<p>이 예에서 애플리케이션 이름은 Spring Cloud Stream 개념(<code>Source</code>, <code>Processor</code>, <code>Sink</code>)을 따릅니다. 이러한 개념은 Java 8 함수(각각 <code>Supplier</code>, <code>Function</code>, <code>Consumer</code>)와 논리적으로 동등한(equivalent) 개념에 매핑됩니다. Spring Cloud Stream은 <code>Source</code>와 <code>Sink</code>(함수 합성을 통해)에서 하나 이상의 <code>Function</code> 인스턴스를 지원할 수 있지만, 각각이 독립적인 애플리케이션으로 작동하는 방법을 보여주는 세 가지 별도의 애플리케이션이 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9396e436-904f-45b9-99b2-43f87c2aa0fa/image.png"></p>
<p>이 가이드에서는 뒤에서 앞으로 작업합니다. 즉, 먼저 <code>Sink</code> 애플리케이션을 빌드한 다음 <code>Processor</code>를 빌드하고 마지막으로 <code>Source</code>를 빌드합니다. 우리는 <code>RabbitMQ</code> 대시보드 UI를 사용하여 진행하면서 각 구성 요소를 테스트합니다.</p>
<h3 id="필수-구성-요소-설치---rabbitmq">필수 구성 요소 설치 - RabbitMQ</h3>
<p>Spring Cloud Stream 기능을 사용하려면 메시지 브로커에 액세스할 수 있는지 확인해야 합니다. 이 가이드에서는 RabbitMQ를 사용합니다. 로컬 Docker 환경이 발견되면 다음 명령으로 RabbitMQ를 시작할 수 있습니다.</p>
<pre><code class="language-shell">docker run -d --hostname my-rabbit --name some-rabbit -p <span class="token number">15672</span>:15672 -p <span class="token number">5672</span>:5672 rabbitmq:3-management</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9ad3f38c-24ce-4801-94d5-b323450c2d8c/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e5bb18f9-247f-41eb-863a-466979a8c8e1/image.png"></p>
<p>결과적으로 RabbitMQ는 사용자 이름/비밀번호 (<code>guest</code>/<code>guest</code>)를 사용하여 로컬에서 액세스할 수 있어야 합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c49cece1-61cd-4976-8152-b4f57033d7bc/image.png"></p>
<hr>
<h2 id="프로젝트-구조-만들기">프로젝트 구조 만들기</h2>
<h3 id="project-structure-모듈-추가">Project Structure 모듈 추가</h3>
<p><code>name-source</code>, <code>name-processor</code>, <code>name-sink</code> 등 예제에서 사용할 애플리케이션 모듈을 추가한다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/82271419-429b-487a-bdc6-898b1fe67ad6/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/2f33cb01-c304-4a21-ab94-08fa6d070e98/image.png"></p>
<h3 id="name-sink-모듈">name-sink 모듈</h3>
<h4 id="person">Person</h4>
<pre><code class="language-java"><span class="token keyword">public</span> record <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token class-name">Long</span> processedTimestamp<span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="namesinkconfiguration">NameSinkConfiguration</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>function</span><span class="token punctuation">.</span><span class="token class-name">Consumer</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameSinkConfiguration</span> <span class="token punctuation">{</span>

	<span class="token comment">// tag::namesink[]</span>
	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">Consumer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">nameSink</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> person <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
			<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">name</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">processedTimestamp</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token comment">// end::namesink[]</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="namesinkapplication">NameSinkApplication</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameSinkApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">NameSinkApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h4 id="applicationproperties">application.properties</h4>
<pre><code class="language-null"># Spring Boot will automatically assign an unused http port
server.port=0

# tag::sinkexchangeconfig[]
spring.cloud.stream.function.bindings.nameSink-in-0=sinkinput
# end::sinkexchangeconfig[]</code></pre>
<h3 id="name-processor-모듈">name-processor 모듈</h3>
<h4 id="person-1">Person</h4>
<pre><code class="language-java"><span class="token keyword">public</span> record <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token class-name">Long</span> processedTimestamp<span class="token punctuation">)</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="nameprocessorconfiguration">NameProcessorConfiguration</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Date</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>function</span><span class="token punctuation">.</span><span class="token class-name">Function</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameProcessorConfiguration</span> <span class="token punctuation">{</span>

	<span class="token comment">// tag::processname[]</span>
	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">Function</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">processName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> name <span class="token operator">-&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">Date</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getTime</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token comment">// end::processname[]</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="nameprocessorapplication">NameProcessorApplication</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameProcessorApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">NameProcessorApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h4 id="applicationproperties-1">application.properties</h4>
<pre><code class="language-null"># Spring Boot will automatically assign an unused http port
server.port=0
# tag::processorexchangeconfig[]
spring.cloud.stream.function.bindings.processName-in-0=processorinput
spring.cloud.stream.function.bindings.processName-out-0=sinkinput
# end::processorexchangeconfig[]</code></pre>
<h3 id="name-source-모듈">name-source 모듈</h3>
<h4 id="namesourceconfiguration">NameSourceConfiguration</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>function</span><span class="token punctuation">.</span><span class="token class-name">Supplier</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Configuration</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameSourceConfiguration</span> <span class="token punctuation">{</span>

	<span class="token comment">// tag::supplyname[]</span>
	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">Supplier</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">supplyName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token string">"Christopher Pike"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token comment">// end::supplyname[]</span>
<span class="token punctuation">}</span></code></pre>
<h4 id="namesourceapplication">NameSourceApplication</h4>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameSourceApplication</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">NameSourceApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h4 id="applicationproperties-2">application.properties</h4>
<pre><code class="language-null"># Spring Boot will automatically assign an unused http port
server.port=0

# tag::supplierexchangeconfig[]
spring.cloud.stream.function.bindings.supplyName-out-0=processorinput
# end::supplierexchangeconfig[]
</code></pre>
<hr>
<h2 id="sink-application">Sink Application</h2>
<p><code>Sink</code>(<code>java.util.function.Consumer</code>)는 <code>NameSinkConfiguration</code>에서 다음과 같이 정의됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Consumer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">nameSink</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">return</span> person <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
    <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">name</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>person<span class="token punctuation">.</span><span class="token function">processedTimestamp</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/49e688d6-27f4-40c6-a910-b2aa096bd4ee/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fb40e871-4a4a-4350-9b31-009c409ecf9e/image.png"></p>
<p>교환 이름(exchange name)을 구성(configure)하지 않고 이 애플리케이션을 시작하면 RabbitMQ에 <code>nameSink-in-0</code>이라는 교환(exchange)이 자동으로 생성됩니다. 나중에 프로세서를 싱크에 연결(hook)할 수 있도록 이 교환을 사용자 정의(customize)하고 싶습니다.</p>
<pre><code class="language-java">spring<span class="token punctuation">.</span>cloud<span class="token punctuation">.</span>stream<span class="token punctuation">.</span>function<span class="token punctuation">.</span>bindings<span class="token punctuation">.</span>nameSink<span class="token operator">-</span>in<span class="token operator">-</span><span class="token number">0</span><span class="token operator">=</span>sinkinput</code></pre>
<p>싱크를 테스트하기 위해 <code>Person</code> 레코드를 나타내는 JSON 메시지를 새로 생성된 교환(exchange)에 수동(manually)으로 게시합니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c089779b-b8d8-4e19-b857-bb930969b03f/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/077f0c91-dd5c-4af4-ad2c-cde1976ea244/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fbbd1dff-0e19-48cd-be3f-5cf47281742a/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/baaa9139-750f-49f6-a138-7926459da16d/image.png"></p>
<p>"message input published but not routed"</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/da11e422-1da1-4b46-bdf0-f50967dde0b7/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/235f8e0f-c703-46c6-97ed-4641cbb502cd/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c9154119-271b-4821-b746-6d3dc1bef608/image.png"></p>
<p>publish 된 메시지가 정상적으로 라우트 되어 콘솔 로그에 출력됩니다. </p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/1badfccb-ee14-412f-842a-73573baa3827/image.png"></p>
<hr>
<p>"Hook"이라는 용어는 소프트웨어 개발에서 다양한 의미로 사용됩니다. 여기서 "hook"은 두 개의 시스템 또는 컴포넌트를 연결하거나 상호 작용할 수 있도록 만드는 메커니즘을 의미합니다.</p>
<p>RabbitMQ에서의 "hook"은 두 개의 exchange를 연결하여 메시지를 교환할 수 있도록 만드는 것을 의미합니다. 일반적으로 RabbitMQ에서는 메시지를 producer(데이터를 생성하는 애플리케이션)가 exchange로 보내고, 해당 exchange는 메시지를 consumer(데이터를 소비하는 애플리케이션)에게 라우팅합니다.</p>
<p>exchange 사이의 "hook"은 메시지의 흐름을 조정하고, 원하는 방식으로 메시지를 필터링하거나 라우팅할 수 있게 해줍니다. 예를 들어, 하나의 exchange에서 받은 메시지를 처리한 후 다른 exchange로 보내거나, 여러 개의 exchange로부터 메시지를 수신하여 특정한 비즈니스 규칙에 따라 처리할 수 있습니다.</p>
<p>즉, "hook"을 설정함으로써 데이터의 흐름을 제어하고 다양한 시스템 또는 컴포넌트 간에 메시지를 교환하고 처리할 수 있습니다. 이를 통해 애플리케이션 간의 통합을 더욱 유연하게 구현할 수 있습니다.</p>
<hr>
<h2 id="processor-application">Processor Application</h2>
<p>프로세서(<code>java.util.function.Function</code>)는 <code>NameProcessorConfiguration</code>에서 다음과 같이 정의됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Function</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> <span class="token function">processName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">return</span> name <span class="token operator">-&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">Date</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getTime</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 함수는 문자열(String) 값을 입력(input)으로 사용하고 데이터가 처리된 시점의 타임스탬프를 추가하는 새 <code>Person</code> 레코드를 생성합니다. 이 애플리케이션을 실행하면 RabbitMQ에 <code>processName-in-0</code> 및 <code>processName-out-0</code>이라는 두 개의 새로운 교환이 생성됩니다. 싱크 애플리케이션에 적용한 구성과 유사하게 이러한 교환 이름을 변경하여 싱크와 supplier(source)에 연결할 수 있도록 하려고 합니다.</p>
<pre><code class="language-null">spring.cloud.stream.function.bindings.processName-in-0=processorinput
spring.cloud.stream.function.bindings.processName-out-0=sinkinput</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/801ae968-cd95-4779-9d6e-dd4158d7dc03/image.png"></p>
<blockquote>
<p>프로세서의 출력은 싱크의 입력과 일치합니다.</p>
</blockquote>
<p>이제 RabbitMQ 대시보드를 사용하여 문자열(이름)을 프로세서 입력 교환으로 보내고 연결된 싱크로 흐르는 것을 확인할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/921e1429-aa50-449a-ac4a-9605502dba7b/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/86bbbb0d-8f5a-4716-aff7-70e1d40db35d/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/c16a88ed-cc89-46df-9b43-b475cb2b838e/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9b5fdd3c-2ca1-4e2a-814b-e6886d2161d1/image.png"></p>
<p>프로세서와 싱크가 제대로 연결되면 실행 중인 싱크의 출력이 표시됩니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/90eb8583-486d-4708-ae9d-e43cf1294363/image.png"></p>
<p>2번 보내서 NameSinkApplication 로그에 두번 출력됨.</p>
<h2 id="source-application">Source Application</h2>
<p>소스(<code>java.util.function.Supplier</code>)는 <code>NameSourceConfiguration</code>에서 다음과 같이 정의됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Supplier</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">supplyName</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token string">"Christopher Pike"</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>프로세서 출력을 싱크 입력에 연결한 방법과 유사하게 동일한 작업을 수행하고 소스 출력을 프로세서 입력에 연결해야 합니다.</p>
<pre><code class="language-null">spring.cloud.stream.function.bindings.supplyName-out-0=processorinput</code></pre>
<blockquote>
<p>소스의 출력은 프로세서의 입력과 일치해야 합니다.</p>
</blockquote>
<p><code>name-processor</code>와 <code>name-sink</code>가 이미 실행 중인 경우 <code>name-source</code>를 시작하면 시스템을 통해 메시지 흐름이 즉시 시작됩니다. 메시지가 프로세서를 통과할 때 약간 다른 타임스탬프와 함께 <code>name-sink</code>에 의해 지속적으로 생성된 동일한 이름이 표시되어야 합니다. 더 이상 RabbitMQ 대시보드 테스트가 필요하지 않습니다! 이제 완벽하게 작동하는 스트림 애플리케이션이 생겼습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8839089d-3aa9-4723-8adf-cad627388db0/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/952fa401-114f-40ca-91cd-eb55334a9735/image.png"></p>
<h2 id="요약">요약</h2>
<p>축하해요! Spring Cloud Stream의 상위 수준 개요를 완료했으며 RabbitMQ와 통신하는 Spring Cloud Stream 애플리케이션을 빌드하고 테스트할 수 있었습니다.</p>