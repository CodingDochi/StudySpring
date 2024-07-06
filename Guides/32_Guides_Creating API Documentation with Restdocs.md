<p>이 가이드는 Spring 애플리케이션에서 HTTP 엔드포인트에 대한 문서를 생성하는 프로세스를 안내합니다.</p>
<h2 id="무엇을-구축할-것인가">무엇을 구축할 것인가</h2>
<p>API를 노출하는 일부 HTTP 엔드포인트를 사용하여 간단한 Spring 애플리케이션을 구축합니다. JUnit과 Spring의 <code>MockMvc</code>를 사용하여 웹 계층만 테스트합니다. 그런 다음 동일한 테스트를 사용하여 Spring REST Docs를 사용하여 API에 대한 문서를 생성합니다.</p>
<h2 id="starting-with-spring-initializr">Starting with Spring Initializr</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9f49d559-80bc-4424-a405-6f5c23af6467/image.png"></p>
<h2 id="간단한-애플리케이션-만들기">간단한 애플리케이션 만들기</h2>
<p>Spring 애플리케이션을 위한 새 컨트롤러를 만듭니다. 다음 목록(src/main/java/guides/testingrestdocs/HomeController.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingrestdocs</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Collections</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Map</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HomeController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> <span class="token function">greeting</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">Collections</span><span class="token punctuation">.</span><span class="token function">singletonMap</span><span class="token punctuation">(</span><span class="token string">"message"</span><span class="token punctuation">,</span> <span class="token string">"Hello, World"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="애플리케이션-실행">애플리케이션 실행</h2>
<p>Spring Initializr는 애플리케이션을 시작하는 데 사용할 수 있는 기본 클래스를 생성합니다. 다음 목록(src/main/java/guides/testingrestdocs/TestingRestdocsApplication.java)은 Spring Initializr가 생성한 애플리케이션 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingrestdocs</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TestingRestdocsApplication</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">TestingRestdocsApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<h2 id="애플리케이션-테스트">애플리케이션 테스트</h2>
<p>이제 애플리케이션이 실행되었으므로 테스트할 수 있습니다. <code>http://localhost:8080</code>에서 홈 페이지를 로드할 수 있습니다. 그러나 변경 사항을 적용할 때 애플리케이션이 작동한다는 확신을 더 가지려면 테스트를 자동화하려고 합니다. 또한 HTTP 끝점(endpoint)에 대한 설명서를 게시하려고 합니다. Spring REST Docs를 사용하여 테스트의 일부로 해당 테스트의 동적 부분을 생성할 수 있습니다.</p>
<p>가장 먼저 할 수 있는 일은 애플리케이션 컨텍스트를 시작할 수 없는 경우 실패하는 간단한 온전성 검사 테스트(sanity check)를 작성하는 것입니다. 이렇게 하려면 테스트 범위에서 프로젝트에 대한 종속성으로 Spring Test 및 Spring REST Docs를 추가하세요. </p>
<p>다음 목록은 Gradle을 사용하는 경우 추가할 항목을 보여줍니다.</p>
<pre><code class="language-groovy">testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc'</code></pre>
<h3 id="buildgradle">build.gradle</h3>
<pre><code class="language-java">plugins <span class="token punctuation">{</span>
    id <span class="token string">'java'</span>
    id <span class="token string">'org.springframework.boot'</span> version <span class="token string">'3.2.1'</span>
    id <span class="token string">'io.spring.dependency-management'</span> version <span class="token string">'1.1.4'</span>
    id <span class="token string">'org.asciidoctor.jvm.convert'</span> version <span class="token string">'3.3.2'</span>
<span class="token punctuation">}</span>

group <span class="token operator">=</span> <span class="token string">'guides'</span>
version <span class="token operator">=</span> <span class="token string">'0.0.1-SNAPSHOT'</span>

java <span class="token punctuation">{</span>
    sourceCompatibility <span class="token operator">=</span> <span class="token string">'17'</span>
<span class="token punctuation">}</span>

repositories <span class="token punctuation">{</span>
    <span class="token function">mavenCentral</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>

ext <span class="token punctuation">{</span>
    <span class="token function">set</span><span class="token punctuation">(</span><span class="token string">'snippetsDir'</span><span class="token punctuation">,</span> <span class="token function">file</span><span class="token punctuation">(</span><span class="token string">"build/generated-snippets"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>

dependencies <span class="token punctuation">{</span>
    implementation <span class="token string">'org.springframework.boot:spring-boot-starter-web'</span>
    testImplementation <span class="token string">'org.springframework.boot:spring-boot-starter-test'</span>
    testImplementation <span class="token string">'org.springframework.restdocs:spring-restdocs-mockmvc'</span>
<span class="token punctuation">}</span>

tasks<span class="token punctuation">.</span><span class="token function">named</span><span class="token punctuation">(</span><span class="token string">'test'</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    outputs<span class="token punctuation">.</span>dir snippetsDir
    <span class="token function">useJUnitPlatform</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>

tasks<span class="token punctuation">.</span><span class="token function">named</span><span class="token punctuation">(</span><span class="token string">'asciidoctor'</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    inputs<span class="token punctuation">.</span>dir snippetsDir
    dependsOn test
<span class="token punctuation">}</span></code></pre>
<hr>
<ol>
<li>
<p><code>ext { set('snippetsDir', file("build/generated-snippets")) }</code>:</p>
<ul>
<li><code>ext</code> 블록은 Gradle 빌드 스크립트의 확장 속성을 정의하는 곳입니다.</li>
<li><code>snippetsDir</code>이라는 이름의 확장 속성을 설정하고, 해당 디렉토리를 <code>build/generated-snippets</code>로 지정합니다.</li>
</ul>
</li>
<li>
<p><code>tasks.named('test') { outputs.dir snippetsDir useJUnitPlatform() }</code>:</p>
<ul>
<li><code>test</code> task에 대한 설정입니다.</li>
<li><code>outputs.dir snippetsDir</code>는 <code>snippetsDir</code> 디렉토리가 <code>test</code> task의 출력 디렉토리로 지정됨을 나타냅니다.</li>
<li><code>useJUnitPlatform()</code>은 JUnit 테스트를 실행하기 위해 JUnit Platform을 사용하도록 설정합니다.</li>
</ul>
</li>
<li>
<p><code>tasks.named('asciidoctor') { inputs.dir snippetsDir dependsOn test }</code>:</p>
<ul>
<li><code>asciidoctor</code> task에 대한 설정입니다.</li>
<li><code>inputs.dir snippetsDir</code>는 <code>snippetsDir</code> 디렉토리를 <code>asciidoctor</code> task의 입력 디렉토리로 설정합니다.</li>
<li><code>dependsOn test</code>는 <code>asciidoctor</code> task가 <code>test</code> task에 의존한다는 것을 나타냅니다. 즉, <code>asciidoctor</code> task가 실행되기 전에 <code>test</code> task가 먼저 실행됩니다.</li>
</ul>
</li>
<li>
<p><code>asciidoctor { sourceDir 'src/main/asciidoc' attributes('snippets': file('target/snippets')) }</code>:</p>
<ul>
<li><code>asciidoctor</code> 블록은 Asciidoctor 플러그인의 설정을 담고 있습니다.</li>
<li><code>sourceDir 'src/main/asciidoc'</code>는 Asciidoctor 플러그인이 AsciiDoc 파일을 찾을 디렉토리를 설정합니다.</li>
<li><code>attributes('snippets': file('target/snippets'))</code>는 Asciidoctor 속성 중 하나로, AsciiDoc 파일에서 <code>{snippets}</code>를 사용할 때 이를 <code>target/snippets</code> 디렉토리로 치환하도록 설정합니다.</li>
</ul>
</li>
</ol>
<hr>
<blockquote>
<p>빌드 파일의 주석은 무시해도 됩니다. 이 가이드에 포함할 파일의 일부를 선택할 수 있도록 도와줍니다.</p>
</blockquote>
<blockquote>
<p>Spring MockMvc를 사용하여 HTTP 콘텐츠를 캡처하는 REST Docs의 <code>mockmvc</code> 버전을 포함했습니다. 자신의 애플리케이션이 Spring MVC를 사용하지 않는 경우 전체 스택 통합 테스트와 함께 작동하는 <code>restassured</code> 버전을 사용할 수도 있습니다.</p>
</blockquote>
<p>이제 다음 예제(src/test/java/guides/testingrestdocs/TestingRestdocsApplicationTests.java)에서 볼 수 있듯이 <code>@RunWith</code> 및 <code>@SpringBootTest</code> 주석과 빈(empty) 테스트 메서드를 사용하여 테스트 사례를 만듭니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingrestdocs</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token keyword">class</span> <span class="token class-name">TestingRestdocsApplicationTests</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">void</span> <span class="token function">contextLoads</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span><span class="token punctuation">{</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>온전한지 확인하는 것은 좋지만 애플리케이션의 동작을 확인하는 몇 가지 테스트도 작성해야 합니다. 유용한 접근 방식은 Spring이 들어오는 HTTP 요청을 처리하고 이를 컨트롤러에 전달하는 MVC 계층만 테스트하는 것입니다. 이를 위해 Spring의 <code>MockMvc</code>를 사용하고 테스트 케이스의 <code>@WebMvcTest</code> 주석을 사용하여 주입을 요청할 수 있습니다. 다음 예제(src/test/java/guides/testingrestdocs/WebLayerTest.java)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingrestdocs</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api<span class="token punctuation">.</span>extension</span><span class="token punctuation">.</span><span class="token class-name">ExtendWith</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">WebMvcTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter</span><span class="token punctuation">.</span><span class="token class-name">SpringExtension</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">.</span>containsString<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>get<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>content<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>

<span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">SpringExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span> <span class="token comment">//@RunWith(SpringRunner.class) JUnit 4</span>
<span class="token annotation punctuation">@WebMvcTest</span><span class="token punctuation">(</span><span class="token class-name">HomeController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebLayerTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mockMvc<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldReturnDefaultMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mockMvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">content</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token function">containsString</span><span class="token punctuation">(</span><span class="token string">"Hello, World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<h2 id="generate-snippets-for-documents-문서용-조각-생성">Generate Snippets for Documents 문서용 조각 생성</h2>
<p>이전 섹션의 테스트는 HTTP 요청을 만들고(모의) 응답을 어설션합니다. 여러분이 만든 HTTP API에는 (적어도 원칙적으로는) 동적 콘텐츠가 있으므로 테스트를 감시하고 문서에 사용하기 위해 HTTP 요청을 빼낼 수 있다면 정말 좋을 것입니다. Spring REST Docs를 사용하면 "스니펫"을 생성하여 이를 수행할 수 있습니다. 테스트에 주석과 추가 "어설션"을 추가하여 이 작업을 수행할 수 있습니다. 다음 예제(src/test/java/guides/testingrestdocs/WebLayerTest.java)는 전체 테스트를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>testingrestdocs</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>restdocs</span><span class="token punctuation">.</span><span class="token class-name">AutoConfigureRestDocs</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">WebMvcTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">.</span>containsString<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>restdocs<span class="token punctuation">.</span>mockmvc</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRestDocumentation</span><span class="token punctuation">.</span>document<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span>get<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultHandlers</span><span class="token punctuation">.</span>print<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>content<span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>


<span class="token annotation punctuation">@WebMvcTest</span><span class="token punctuation">(</span><span class="token class-name">HomeController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@AutoConfigureRestDocs</span><span class="token punctuation">(</span>outputDir <span class="token operator">=</span> <span class="token string">"target/snippets"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebLayerTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mockMvc<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">shouldReturnDefaultMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>mockMvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andDo</span><span class="token punctuation">(</span><span class="token function">print</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">content</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token function">containsString</span><span class="token punctuation">(</span><span class="token string">"Hello, World"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andDo</span><span class="token punctuation">(</span><span class="token function">document</span><span class="token punctuation">(</span><span class="token string">"home"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>새로운 주석은 <code>@AutoConfigureRestDocs</code>(Spring Boot의)이며 생성된 조각의 디렉터리 위치에 대한 인수를 사용합니다. 그리고 새로운 주장은 조각의 문자열 식별자에 대한 인수를 취하는 <code>MockMvcRestDocumentation.document</code>입니다.</p>
<blockquote>
<p>Gradle 사용자는 출력(output) 디렉터리로 <code>target</code> 대신 <code>build</code>를 사용하는 것을 선호할 수 있습니다. 그러나 그것은 중요하지 않습니다. 원하는 것을 사용하십시오.</p>
</blockquote>
<p>테스트를 실행한 다음 <code>target/snippet</code>을 살펴보세요. 다음과 같이 Asciidoctor 조각이 포함된 <code>home</code>(식별자)이라는 디렉터리를 찾아야 합니다.<br>
<img src="https://velog.velcdn.com/images/dev_hammy/post/da885b2a-e8ec-4fda-809d-1f0f076fee99/image.png"></p>
<pre><code class="language-null">└── target
    └── snippets
        └── home
            └── curl-request.adoc
            └── http-request.adoc
            └── http-response.adoc
            └── httpie-request.adoc
            └── request-body.adoc
            └── response-body.adoc</code></pre>
<p>기본 조각은 HTTP 요청 및 응답에 대한 Asciidoctor 형식입니다. 또한 <code>curl</code> 및 <code>httpie</code>(두 가지 일반적이고 널리 사용되는 명령줄 HTTP 클라이언트)에 대한 명령줄 예제도 있습니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/72d897c2-6395-4b08-99c3-bf8a57d098cf/image.png"></p>
<p>테스트에서 <code>document()</code> 어설션에 인수를 추가하여 추가 조각을 생성할 수 있습니다. 예를 들어 다음 예제(src/test/java/guides/testingrestdocs/WebLayerTest.java에서)에 표시된 것처럼 <code>PayloadDocumentation.responseFields()</code> 조각을 사용하여 JSON 응답의 각 필드를 문서화할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">this</span><span class="token punctuation">.</span>mockMvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
    <span class="token punctuation">.</span><span class="token function">andDo</span><span class="token punctuation">(</span><span class="token function">document</span><span class="token punctuation">(</span><span class="token string">"home"</span><span class="token punctuation">,</span> <span class="token function">responseFields</span><span class="token punctuation">(</span>
        <span class="token function">fieldWithPath</span><span class="token punctuation">(</span><span class="token string">"message"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">description</span><span class="token punctuation">(</span><span class="token string">"The welcome message for the user."</span><span class="token punctuation">)</span>
    <span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/ca2a98eb-5a46-4f1b-8e30-2f7d80aaa1b9/image.png"></p>
<p>테스트를 실행하면 <code>response-fields.adoc</code>라는 추가 조각 파일을 찾아야 합니다. 여기에는 필드 이름과 설명이 포함된 테이블이 포함되어 있습니다. 필드를 생략하거나 이름이 잘못되면 테스트가 실패합니다. 이것이 REST Docs의 힘입니다.</p>
<blockquote>
<p>사용자 정의 스니펫을 생성하고 스니펫의 형식을 변경하며 호스트 이름과 같은 값을 사용자 정의할 수 있습니다. 자세한 내용은 Spring REST Docs 문서를 참조하세요.</p>
</blockquote>
<h2 id="스니펫-사용">스니펫 사용</h2>
<p>생성된 코드 조각을 사용하려면 프로젝트에 Asciidoctor 콘텐츠를 일부 포함하고 빌드 시 코드 조각을 포함해야 합니다. 이 작업을 보려면 <code>src/main/asciidoc/index.adoc</code>라는 새 파일을 만들고 원하는 대로 스니펫을 포함하세요. 다음 예제(<code>src/main/asciidoc/index.adoc</code>)에서는 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-asciidoc">= Getting Started With Spring REST Docs

This is an example output for a service running at http://localhost:8080:

.request
include::{snippets}/home/http-request.adoc[]

.response
include::{snippets}/home/http-response.adoc[]

As you can see the format is very simple, and in fact you always get the same message.</code></pre>
<p>이 Asciidoc 파일의 주요 특징은 Asciidoctor <code>include</code> 지시문(directive)을 사용하여 두 개의 스니펫을 포함한다는 것입니다(콜론과 후행 괄호는 파서에게 해당 행에 대해 특별한 작업을 수행하도록 지시합니다). 포함된 조각에 대한 경로는 <code>{snippets}</code>라는 자리 표시자(Asciidoctor의 <code>attribute</code>)로 표현됩니다. 이 간단한 경우에 유일한 다른 마크업은 상단의 <code>=</code>(레벨 1 섹션 제목)와 스니펫의 캡션('요청' 및 '응답') 앞에<code>.</code>입니다.<code>.</code>는 해당 줄의 텍스트를 캡션으로 바꿉니다.</p>
<p>그런 다음 빌드 구성에서 이 소스 파일을 선택한 문서 형식으로 처리해야 합니다. </p>
<p>Gradle을 사용하는 경우 <code>./gradlew asciidoctor</code>를 실행하면 <code>build/asciidoc</code>이 생성됩니다. 다음 목록은 <code>build.gradle</code> 파일의 Asciidoctor 관련 부분을 보여줍니다.</p>
<hr>
<p><code>index.adoc</code>의 include 문에서 <code>{snippets}</code>는 <code>build.gradle</code>에 의해 <code>build/generated-snippets</code>로 지정되고 있었습니다.  </p>
<pre><code class="language-null">// 중략
ext {
    set('snippetsDir', file("build/generated-snippets"))
}

tasks.named('test') {
    outputs.dir snippetsDir
    useJUnitPlatform()
}

tasks.named('asciidoctor') {
    inputs.dir snippetsDir
    dependsOn test
}</code></pre>
<p>build 설정에 따르면 <code>test</code> 태스크의 출력물은 <code>snippetsDir</code>로 향하고, <code>asciidoctor</code> 태스크의 입력물은 <code>snippetsDir</code>로부터 옵니다. 그리고 이 태스크는 <code>test</code> 태스크를 실행한 후 수행됩니다. </p>
<p>그러나 튜토리얼 원문에서 <code>@AutoConfigureRestDocs</code>의 <code>outputDir</code>의 인수를 <code>target/snippets</code>로 지정하는 바람에 <code>build/generated-snippets</code> 폴더로 <code>.adoc</code> 파일들이 모이지 못했고, 이 때문에 <code>src/main/asciidoc/index.adoc</code>의 <code>include</code> 지시문은 제대로 작동하지 않고 있었습니다.</p>
<p>결국 <code>@AutoConfigureRestDocs</code>의 <code>outputDir</code>의 인수를 <code>build/generated-snippets</code>로 수정한 후에야 제대로 된 결과를 볼 수 있었습니다.</p>
<hr>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/94fb3a3f-f288-48f6-a280-56fc88de37d3/image.png"></p>