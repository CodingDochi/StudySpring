<p>이 가이드는 Spring 애플리케이션을 생성한 다음 JUnit으로 테스트하는 과정을 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>간단한 Spring 애플리케이션을 빌드하고 JUnit을 사용하여 테스트합니다. 아마도 애플리케이션에서 개별 클래스의 단위 테스트를 작성하고 실행하는 방법을 이미 알고 있을 것입니다. 따라서 이 가이드에서는 Spring 테스트 및 Spring Boot 기능을 사용하여 Spring과 코드 간의 상호 작용을 테스트하는 데 중점을 둘 것입니다. 애플리케이션 컨텍스트가 성공적으로 로드되는지 간단한 테스트로 시작하고 Spring의 <code>MockMvc</code>를 사용하여 웹 계층만 계속 테스트합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9b79346c-536f-49f2-b7ac-fab775035401/image.png"></p>
<h2 id="간단한-애플리케이션-만들기">간단한 애플리케이션 만들기</h2>
<p>Spring 애플리케이션을 위한 새 컨트롤러를 만듭니다. 다음 목록(src/main/java/guides/testingweb/HomeController.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ResponseBody</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HomeController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token annotation punctuation">@ResponseBody</span> <span class="token class-name">String</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Hello, World"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HomeController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token annotation punctuation">@ResponseBody</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Hello, World"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>첫 번째 코드에서는 <code>@ResponseBody</code>를 메서드의 리턴 타입 바로 앞에 위치시켰고, 두 번째 코드에서는 <code>@ResponseBody</code> 어노테이션을 메서드 위에 따로 명시했지만, 두 가지 방법 모두 메서드가 반환하는 값을 HTTP 응답의 본문으로 사용하도록 지정합니다.</p>
<hr>
<blockquote>
<p>앞의 예에서는 <code>GET</code>과 <code>PUT</code>, <code>POST</code> 등을 지정하지 않습니다. 기본적으로 <code>@RequestMapping</code>은 모든 HTTP 작업을 매핑합니다. <code>@GetMapping</code> 또는 <code>@RequestMapping(method=GET)</code>을 사용하여 이 매핑 범위를 좁힐 수 있습니다.</p>
</blockquote>
<hr>
<p>일반적으로 Spring의 <code>@Controller</code>에서 메서드는 뷰 템플릿 이름을 반환하여 해당 뷰를 렌더링합니다. 그러나 <code>@ResponseBody</code> 어노테이션을 사용하면 메서드의 반환 값이 HTTP 응답 본문으로 직접 전송되어야 함을 나타냅니다. 즉, 메서드가 반환하는 객체나 데이터가 직렬화되어 HTTP 응답으로 전송됩니다.</p>
<hr>
<h2 id="애플리케이션-실행">애플리케이션 실행</h2>
<p>Spring Initializr는 애플리케이션 클래스(<code>main()</code> 메소드가 있는 클래스)를 생성합니다. 이 가이드에서는 이 클래스를 수정할 필요가 없습니다. 다음 목록(src/main/java/guides/testingweb/TestingWebApplication.java)은 Spring Initializr가 생성한 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TestingWebApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">TestingWebApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>이제 애플리케이션이 실행되었으므로 테스트할 수 있습니다. <code>http://localhost:8080</code>에서 홈 페이지를 로드할 수 있습니다. 그러나 변경 사항을 적용할 때 애플리케이션이 작동한다는 확신을 더 가지려면 테스트를 자동화하려고 합니다.</p>
<blockquote>
<p>Spring Boot는 사용자가 애플리케이션을 테스트할 계획이라고 가정하므로 빌드 파일(<code>build.gradle</code> 또는 <code>pom.xml</code>)에 필요한 종속성을 추가합니다.</p>
</blockquote>
<p>가장 먼저 할 수 있는 일은 애플리케이션 컨텍스트를 시작할 수 없는 경우 실패하는 간단한 온전성 검사(sanity check) 테스트를 작성하는 것입니다. 다음 목록(src/test/java/guides/testingweb/TestingWebApplicationTest.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">class</span> <span class="token class-name">TestingWebApplicationTests</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">contextLoads</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/5f372296-575d-403f-8b74-a23903c7798e/image.png"></p>
<p><code>@SpringBootTest</code> 주석은 Spring Boot에 기본 구성 클래스(예: <code>@SpringBootApplication</code>이 포함된 클래스)를 찾고 이를 사용하여 Spring 애플리케이션 컨텍스트를 시작하도록 지시합니다. IDE나 명령줄(<code>./mvnw test</code> 또는 <code>./gradlew test</code> 실행)에서 이 테스트를 실행할 수 있으며, 통과해야 합니다. 컨텍스트가 컨트롤러를 생성하고 있음을 확인하려면 다음 예제(src/test/java/guides/testingweb/SmokeTest.java에서)에 표시된 대로 어설션(assertion)을 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">class</span> <span class="token class-name">TestingWebApplicationTests</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">HomeController</span> controller<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">contextLoads</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>controller<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotNull</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>Spring은 <code>@Autowired</code> 주석을 해석하고 테스트 메서드가 실행되기 전에 컨트롤러가 주입됩니다. 우리는 테스트 주장을 표현하기 위해 AssertJ(<code>assertThat()</code> 및 기타 메소드를 제공)를 사용합니다.</p>
<blockquote>
<p>Spring 테스트 지원의 좋은 기능은 애플리케이션 컨텍스트가 테스트 간에 캐시된다는 것입니다. 이렇게 하면 테스트 케이스에 여러 메소드가 있거나 동일한 구성을 가진 여러 테스트 케이스가 있는 경우 애플리케이션을 한 번만 시작하는 데 드는 비용이 발생합니다. <code>@DirtiesContext</code> 주석을 사용하여 캐시를 제어할 수 있습니다.</p>
</blockquote>
<p>온전한 상태를 확인하는 것은 좋지만 애플리케이션의 동작을 확인하는 몇 가지 테스트도 작성해야 합니다. 이를 수행하려면 애플리케이션을 시작하고 연결을 수신(listen)한 다음(프로덕션에서와 마찬가지로) HTTP 요청을 보내고 응답을 어설션할 수 있습니다(assert the response). 다음 목록(src/test/java/guides/testingweb/HttpRequestTest.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>


<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">TestRestTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>server</span><span class="token punctuation">.</span><span class="token class-name">LocalServerPort</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span>webEnvironment <span class="token operator">=</span> <span class="token class-name">SpringBootTest</span><span class="token punctuation">.</span><span class="token class-name">WebEnvironment</span><span class="token punctuation">.</span>RANDOM_PORT<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HttpRequestTest</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@LocalServerPort</span>
    <span class="token keyword">private</span> <span class="token keyword">int</span> port<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">TestRestTemplate</span> restTemplate<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">greetingShouldReturnDefaultMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>restTemplate<span class="token punctuation">.</span><span class="token function">getForObject</span><span class="token punctuation">(</span><span class="token string">"http://localhost:"</span><span class="token operator">+</span>port<span class="token operator">+</span><span class="token string">"/"</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span><span class="token string">"Hello, World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/8faf87d9-6209-4010-9c28-13731c64e143/image.png"></p>
<p><code>webEnvironment=RANDOM_PORT</code>를 사용하여 임의의 포트로 서버를 시작하고(테스트 환경에서 충돌을 피하는 데 유용함) <code>@LocalServerPort</code>를 사용하여 포트를 삽입합니다. 또한 Spring Boot는 자동으로 <code>TestRestTemplate</code>을 제공했습니다. <code>@Autowired</code>를 추가하기만 하면 됩니다.</p>
<p>또 다른 유용한 접근 방식은 서버를 전혀 시작하지 않고 그 아래 계층만 테스트하는 것입니다. 여기서 Spring은 들어오는 HTTP 요청을 처리하고 이를 컨트롤러에 전달합니다. 이렇게 하면 거의 모든 전체 스택이 사용되며 실제 HTTP 요청을 처리하는 것과 똑같은 방식으로 코드가 호출되지만 서버를 시작하는 비용은 들지 않습니다. 그렇게 하려면 Spring의 <code>MockMvc</code>를 사용하고 테스트 케이스에 <code>@AutoConfigureMockMvc</code> 주석을 사용하여 주입되도록 요청하세요. 다음 목록(src/test/java/guides/testingweb/TestingWebApplicationTest.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">AutoConfigureMockMvc</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">.</span>containsString<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>get<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultHandlers</span><span class="token punctuation">.</span>print<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>content<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token annotation punctuation">@AutoConfigureMockMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TestingWebApplicationTest</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mockMvc<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">shouldReturnDefaultMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mockMvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andDo</span><span class="token punctuation">(</span><span class="token function">print</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">content</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token function">containsString</span><span class="token punctuation">(</span><span class="token string">"Hello, World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/15f134a5-2a61-46e2-a500-9d7af2480fd5/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/62457726-432e-49e1-a3ff-bff9a38760cd/image.png"></p>
<p>이 테스트에서는 전체 Spring 애플리케이션 컨텍스트가 시작되지만 서버는 없습니다. 다음 목록(src/test/java/guides/testingweb/WebLayerTest.java)에 표시된 대로 <code>@WebMvcTest</code>를 사용하여 테스트 범위를 웹 계층으로만 좁힐 수 있습니다.</p>
<pre><code class="language-null">@WebMvcTest
include::src/test/java/guides/testingweb/WebLayerTest.java</code></pre>
<p>테스트 어설션은 이전 사례와 동일합니다. 그러나 이 테스트에서 Spring Boot는 전체 컨텍스트가 아닌 웹 계층만 인스턴스화합니다. 여러 컨트롤러가 있는 애플리케이션에서는 예를 들어 <code>@WebMvcTest(HomeController.class)</code>를 사용하여 컨트롤러 하나만 인스턴스화하도록 요청할 수도 있습니다.</p>
<p>지금까지 <code>HomeController</code>는 간단하고 종속성이 없습니다. 인사말을 저장하기 위한 추가 구성 요소를 도입하면(아마도 새 컨트롤러에) 이를 더욱 현실적으로 만들 수 있습니다. 다음 예제(src/main/java/guides/testingweb/GreetingController.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Controller</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RequestMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">ResponseBody</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingController</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">GreetingService</span> service<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">GreetingController</span><span class="token punctuation">(</span><span class="token class-name">GreetingService</span> service<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>service <span class="token operator">=</span> service<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token annotation punctuation">@ResponseBody</span> <span class="token class-name">String</span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> service<span class="token punctuation">.</span><span class="token function">greet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 다음 목록(src/main/java/guides/testingweb/GreetingService.java)에 표시된 대로 인사말 서비스를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype</span><span class="token punctuation">.</span><span class="token class-name">Service</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">GreetingService</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">greet</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Hello World"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>Spring은 (생성자 signature으로 인해) 서비스 종속성을 컨트롤러에 자동으로 주입합니다. 다음 목록(src/test/java/guides/testingweb/WebMockTest.java)은 <code>@WebMvcTest</code>를 사용하여 이 컨트롤러를 테스트하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingweb</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">WebMvcTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>mock<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">MockBean</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">.</span>containsString<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">Mockito</span><span class="token punctuation">.</span>when<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>get<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultHandlers</span><span class="token punctuation">.</span>print<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>content<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>

<span class="token annotation punctuation">@WebMvcTest</span><span class="token punctuation">(</span><span class="token class-name">GreetingController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebMockTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mockMvc<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@MockBean</span>
    <span class="token keyword">private</span> <span class="token class-name">GreetingService</span> service<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">greetingShouldReturnMessageFromService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token function">when</span><span class="token punctuation">(</span>service<span class="token punctuation">.</span><span class="token function">greet</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"Hello, Mock"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">this</span><span class="token punctuation">.</span>mockMvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/greeting"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andDo</span><span class="token punctuation">(</span><span class="token function">print</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">content</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token function">containsString</span><span class="token punctuation">(</span><span class="token string">"Hello, Mock"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/798722ea-084f-4870-bc28-addb074bdd61/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/dbee5b99-ec55-484b-beed-b1cfa9460abe/image.png"></p>
<p><code>@MockBean</code>을 사용하여 <code>GreetingService</code>에 대한 모의 객체를 생성 및 주입하고(그렇지 않으면 애플리케이션 컨텍스트를 시작할 수 없음) <code>Mockito</code>를 사용하여 기대치(expectation)를 설정합니다.</p>