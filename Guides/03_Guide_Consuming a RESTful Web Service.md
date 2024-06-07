<p>RESTful 웹 서비스를 consume 하는 application을 생성해보겠습니다.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p><code>RestTemplate</code>을 사용하여 Spring Boot 랜덤 quatation을 <a href="http://localhost:8080/api/random">http://localhost:8080/api/random</a> 으로 반환하는 애플리케이션을 build 해보겠습니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p>스프링 이니셜라이저를 이용하여 Spring Web을 의존성으로 추가하고 자바 17, Gradle-Groovy, Jar 설정으로 생성했다.</p>
<h2 id="fetching-a-rest-resource">Fetching a REST Resource</h2>
<p>프로젝트 설정이 완료되면 RESTful 서비스를 사용하는(consume) 간단한 애플리케이션을 생성할 수 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/17554920-bde6-42b7-87b7-65b07d02d335/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/b5228ef0-1ca1-4d91-b1cf-6314bb133374/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e604a7b8-fe8f-4cc4-b86c-94c88e5f9f18/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ccfe6673-269f-4f6b-9d97-936e8ded13a8/image.png"></p>
<p>그렇게 하려면 먼저 REST 리소스(resource) 소스가 필요합니다. 우리는 <code>https://github.com/spring-guides/quoters</code>에서 그러한 서비스의 예를 제공했습니다. 해당 애플리케이션을 별도의 터미널에서 실행하고 <code>http://localhost:8080/api/random</code>에서 결과에 액세스할 수 있습니다. 해당 주소는 Spring Boot에 대한 인용문을 무작위로 가져와서 JSON 문서로 반환합니다. 다른 유효한 주소로는 <code>http://localhost:8080/api/</code>(모든 인용문의 경우) 및 <code>http://localhost:8080/api/1</code>(첫 번째 인용문의 경우), <code>http://localhost:8080/api/2</code>가 있습니다. (두 번째 인용의 경우) 등(현재 최대 10개).</p>
<p>웹 브라우저나 <code>curl</code>을 통해 해당 <code>URL</code>을 요청하면 다음과 같은 JSON 문서를 받게 됩니다.</p>
<pre><code class="language-json">{
   type: "success",
   value: {
      id: 10,
      quote: "Really loving Spring Boot, makes stand alone Spring apps easy."
   }
}</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9ef7890a-1e78-4971-a86a-15908374a6af/image.png"></p>
<p>이는 충분히 쉽지만 브라우저나 <code>curl</code>을 통해 가져올 때 별로 유용하지 않습니다.</p>
<p>REST 웹 서비스를 사용하는 더 유용한 방법은 프로그래밍 방식입니다. 해당 작업을 돕기 위해 Spring은 <code>RestTemplate</code>이라는 편리한 템플릿 클래스를 제공합니다. <code>RestTemplate</code>은 대부분의 RESTful 서비스와 한 줄의 주문으로 상호 작용합니다. 또한 해당 데이터를 사용자 정의 도메인 유형에 바인딩할 수도 있습니다.</p>
<p>먼저 필요한 데이터를 포함할 도메인 클래스를 만들어야 합니다. 다음 목록은 도메인 클래스로 사용할 수 있는 <code>Quote</code> record 클래스를 보여줍니다.</p>
<p><code>src/main/java/guides/consumingrest/Quote.java</code></p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>consumingrest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JsonIgnoreProperties</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@JsonIgnoreProperties</span><span class="token punctuation">(</span>ignoreUnknown <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> record <span class="token class-name">Quote</span><span class="token punctuation">(</span><span class="token class-name">String</span> type<span class="token punctuation">,</span> <span class="token class-name">Value</span> value<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">}</span></code></pre>
<p>이 간단한 Java 레코드 클래스에는 Jackson JSON 처리(processing) 라이브러리의 <code>@JsonIgnoreProperties</code> 어노테이션이 추가되어 이 유형에 바인딩되지 않은 모든 속성은 무시되어야 함을 나타냅니다.</p>
<p>데이터를 사용자 정의 유형에 직접 바인딩하려면 API에서 반환된 JSON 문서의 키와 정확히 동일하도록 변수 이름을 지정해야 합니다. JSON 문서의 변수 이름과 키가 일치하지 않는 경우 <code>@JsonProperty</code> 어노테이션을 사용하여 JSON 문서의 정확한 키를 지정할 수 있습니다. (이 예에서는 각 변수 이름을 JSON 키와 일치시키므로 여기서는 해당 주석이 필요하지 않습니다.)</p>
<p>내부 인용문 자체를 포함하려면 추가 클래스도 필요합니다. <code>Value</code> 레코드 클래스는 이러한 요구를 충족하며 다음 목록(<code>src/main/java/guides/consumerrest/Value.java</code>)에 표시됩니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>consumingrest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">com<span class="token punctuation">.</span>fasterxml<span class="token punctuation">.</span>jackson<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">JsonIgnoreProperties</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@JsonIgnoreProperties</span><span class="token punctuation">(</span>ignoreUnknown <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> record <span class="token class-name">Value</span><span class="token punctuation">(</span><span class="token class-name">Long</span> id<span class="token punctuation">,</span> <span class="token class-name">String</span> quote<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">}</span></code></pre>
<h2 id="finishing-the-application">Finishing the Application</h2>
<p>이니셜라이저가 자동으로 생성한 <code>main()</code> 메서드가 있는 클래스(<code>src/main/java/com/example/consumingrest/ConsumingRestApplication.java</code>)에 아래 요소들을 추가하여 RESTful 소스 인용문을 표시할 수 있도록 합니다. </p>
<ul>
<li>출력을 로그(이 예에서는 콘솔)로 보내기 위한 로거.</li>
<li>Jackson JSON 처리 라이브러리를 사용하여 들어오는(incoming) 데이터를 처리하는 <code>RestTemplate</code></li>
<li>시작 시 <code>RestTemplate</code>을 실행하고 결과적으로 quotation을 가져오는(fetch) <code>CommandLineRunner</code></li>
</ul>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>consumingrest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">Logger</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>slf4j</span><span class="token punctuation">.</span><span class="token class-name">LoggerFactory</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">RestTemplateBuilder</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Profile</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">RestTemplate</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConsumingRestApplication</span> <span class="token punctuation">{</span>
    
    <span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token class-name">Logger</span> log <span class="token operator">=</span>
            <span class="token class-name">LoggerFactory</span><span class="token punctuation">.</span><span class="token function">getLogger</span><span class="token punctuation">(</span><span class="token class-name">ConsumingRestApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">ConsumingRestApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">RestTemplate</span> <span class="token function">restTemplate</span><span class="token punctuation">(</span><span class="token class-name">RestTemplateBuilder</span> builder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Bean</span>
    <span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"!test"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token class-name">Quote</span> quote <span class="token operator">=</span> restTemplate<span class="token punctuation">.</span><span class="token function">getForObject</span><span class="token punctuation">(</span>
                    <span class="token string">"http://localhost:8080/api/random"</span><span class="token punctuation">,</span>
                    <span class="token class-name">Quote</span><span class="token punctuation">.</span><span class="token keyword">class</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
            log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span>quote<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<ol>
<li><code>LoggerFactory</code>: 로깅에 사용되는 SLF4J(Logger Facade for Java)의 Logger를 가져오는 팩토리 메서드입니다.</li>
</ol>
<ul>
<li>일반적으로 팩토리 메서드나 팩토리 패턴을 사용하여 객체를 생성하면, 팩토리에 따라 객체의 생성 여부나 방법이 결정됩니다. 이 때, 팩토리에 의해 생성된 객체는 팩토리 구현에 따라 여러 번 호출되더라도 동일한 인스턴스를 반환할 수 있습니다.</li>
<li><code>final</code>로 선언된 로거는 한 번 생성되고 할당된 이후에는 변경되지 않으므로 일관성 있는 로깅 방식을 유지할 수 있습니다</li>
</ul>
<ol start="2">
<li>
<p><code>RestTemplate</code>: Spring에서 제공하는 HTTP 통신을 위한 클라이언트 라이브러리로, HTTP 요청을 보내고 응답을 받을 수 있게 해줍니다.</p>
</li>
<li>
<p><code>RestTemplateBuilder</code>: <code>RestTemplate</code>을 생성하기 위한 빌더 클래스로, 다양한 구성 옵션을 제공하며 <code>RestTemplate</code>의 인스턴스를 생성합니다.</p>
</li>
</ol>
<ul>
<li>빌더 패턴은 객체 생성 과정을 단순화하고, 유연하게 생성자 인자를 다루며, 객체의 속성을 설정하기 위한 패턴입니다.</li>
<li>일반적으로 빌더 패턴을 사용하여 단일 인스턴스를 생성하게 되면, 그 인스턴스는 일반적으로 싱글턴과 유사한 형태를 가질 수 있습니다. 하지만 빌더 패턴 자체가 싱글턴을 만들기 위한 목적은 아닙니다.</li>
</ul>
<ol start="4">
<li>
<p><code>@Bean</code>: Spring 애플리케이션 컨텍스트에 빈(Bean)을 추가하기 위한 어노테이션으로, 해당 메서드가 반환하는 객체를 빈으로 등록합니다.</p>
</li>
<li>
<p><code>@Profile</code>: Spring에서 활성화될 프로파일을 지정하는 어노테이션으로, 특정 프로파일에만 적용되는 설정을 지정할 수 있습니다.</p>
</li>
</ol>
<ul>
<li>
<p>특정한 환경 또는 프로파일에 따라 다른 빈을 로드하고 싶을 때 사용됩니다.</p>
</li>
<li>
<p>예를 들어, 개발용과 프로덕션용 설정을 분리하고 싶을 때 @Profile을 사용하여 각각의 프로파일에 맞는 빈을 로드합니다.</p>
</li>
<li>
<p><code>@Import</code> </p>
<ul>
<li>어노테이션은 다른 설정 클래스나 구성을 현재 설정 클래스로 가져오도록 지정합니다.</li>
<li>이를 통해 여러 설정을 조합하거나, 설정을 분리하여 재사용할 수 있습니다.</li>
<li>주로 여러 개의 구성 클래스를 한 곳에서 묶어서 사용하고 싶을 때 활용됩니다.</li>
</ul>
</li>
<li>
<p><code>@Scope</code> </p>
<ul>
<li>빈의 범위(scope)를 지정하는데 사용됩니다. 기본적으로 스프링에서는 싱글톤(scope="singleton")이 기본값입니다. 따라서 스프링 애플리케이션 컨텍스트에서 해당 빈은 기본적으로 싱글톤으로 동작합니다. </li>
<li><code>@Scope</code> 어노테이션을 사용하여 프로토타입(scope="prototype")이나 다른 커스텀 범위로 빈을 정의할 수 있습니다.</li>
<li>@Profile은 어노테이션을 붙이는 빈에 제한이 없지만, @Scope는 빈의 범위를 설정하는 데 사용되므로 일부 빈에만 적용됩니다.</li>
</ul>
</li>
</ul>
<ol start="6">
<li><code>CommandLineRunner</code>: Spring Boot 애플리케이션을 시작할 때 실행되는 인터페이스입니다. <code>run</code> 메서드를 구현하여 애플리케이션 실행 시 원하는 동작을 정의할 수 있습니다.</li>
</ol>
<ul>
<li>
<p><code>CommandLineRunner</code>의 <code>run</code> 메서드의 <code>args</code>: Java 애플리케이션을 실행할 때 전달되는 커맨드 라인 인수들을 나타내는 문자열 배열입니다.</p>
</li>
<li>
<p><code>main</code> 메서드의 <code>args</code>: Java 애플리케이션의 진입점으로, 커맨드 라인에서 전달되는 인수들을 나타내는 문자열 배열입니다. <code>SpringApplication.run</code> 메서드에 전달되는 인수로 사용됩니다.</p>
</li>
</ul>
<p>마지막으로 서버 포트를 설정해야 합니다. <code>quoters</code> 애플리케이션은 기본 서버 포트인 8080을 사용하므로 이 애플리케이션도 동일한 포트를 사용할 수 없습니다. <code>application.properties</code>(Initializr가 생성한)에 다음 줄을 추가하여 서버 포트를 8081로 설정할 수 있습니다.</p>
<pre><code class="language-null">server.port=8081</code></pre>
<h2 id="running-the-application">Running the Application</h2>
<p>Gradle 또는 Maven을 사용하여 명령줄에서 애플리케이션을 실행할 수 있습니다. 필요한 모든 종속성, 클래스 및 리소스가 포함된 단일 실행 가능 JAR 파일을 빌드하고 실행할 수도 있습니다. 실행 가능한 jar을 구축하면 개발 수명 주기 전반에 걸쳐 다양한 환경 등에서 서비스를 애플리케이션으로 쉽게 제공, 버전 지정 및 배포할 수 있습니다.</p>
<p>Gradle을 사용하는 경우 <code>./gradlew bootRun</code>을 사용하여 애플리케이션을 실행할 수 있습니다. 또는 다음과 같이 <code>./gradlew build</code>를 사용하여 JAR 파일을 빌드한 후 JAR 파일을 실행할 수 있습니다.</p>
<pre><code class="language-null">java -jar target/gs-consuming-rest-0.1.0.jar</code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e409cf7b-2dc4-49e4-8fdb-3f8bd312662a/image.png"></p>
<pre><code class="language-null">2023-12-09T19:08:31.439+09:00  INFO 12025 --- [           main] g.c.ConsumingRestApplication             : Started ConsumingRestApplication in 1.932 seconds (process running for 2.51)
2023-12-09T19:08:31.786+09:00  INFO 12025 --- [           main] g.c.ConsumingRestApplication             : Quote[type=success, value=Value[id=12, quote=@springboot with @springframework is pure productivity! Who said in #java one has to write double the code than in other langs? #newFavLib]]</code></pre>
<p>프로그램을 실행하면 로그를 통해 restTemplate으로 받은 random quote가 출력된다. </p>