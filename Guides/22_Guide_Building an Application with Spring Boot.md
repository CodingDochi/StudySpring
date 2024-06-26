<p>이 가이드에서는 Spring Boot가 애플리케이션 개발을 가속화하는 데 어떻게 도움이 되는지에 대한 샘플을 제공합니다. Spring 시작하기 가이드를 더 많이 읽으면 Spring Boot에 대한 더 많은 사용 사례를 볼 수 있습니다. 이 가이드는 Spring Boot를 빠르게 맛보기 위한 것입니다. 자신만의 Spring Boot 기반 프로젝트를 생성하려면 Spring Initializr를 방문하여 프로젝트 세부 정보를 입력하고 옵션을 선택한 후 번들 프로젝트를 zip 파일로 다운로드하세요.</p>
<h2 id="what-you-will-build">What You Will Build</h2>
<p>Spring Boot를 사용하여 간단한 웹 애플리케이션을 구축하고 여기에 몇 가지 유용한 서비스를 추가합니다.</p>
<h2 id="spring-boot로-무엇을-할-수-있는지-알아보세요">Spring Boot로 무엇을 할 수 있는지 알아보세요</h2>
<p>Spring Boot는 애플리케이션을 구축하는 빠른 방법을 제공합니다. classpath와 구성한(configured) Bean을 살펴보고 누락된 항목에 대해 합리적인 가정을 하고 해당 항목을 추가합니다. Spring Boot를 사용하면 인프라에 대한 부담을 덜고 비즈니스 기능에 더 집중할 수 있습니다.</p>
<p>다음 예제에서는 Spring Boot가 수행할 수 있는 작업을 보여줍니다.</p>
<ul>
<li>
<p>Spring MVC가 classpath에 있습니까? 거의 항상 필요한 몇 가지 특정 Bean이 있으며 Spring Boot는 이를 자동으로 추가합니다. Spring MVC 애플리케이션에는 서블릿 컨테이너도 필요하므로 Spring Boot는 내장된 Tomcat을 자동으로 구성합니다.</p>
</li>
<li>
<p>Jetty가 classpath에 있나요? 그렇다면 아마도 Tomcat이 아니라 내장된 Jetty를 원할 것입니다. Spring Boot가 이를 처리합니다.</p>
</li>
<li>
<p>Thymeleaf가 classpath에 있나요? 그렇다면 항상 애플리케이션 컨텍스트에 추가해야 하는 몇 가지 빈이 있습니다. Spring Boot가 이를 추가합니다.</p>
</li>
</ul>
<p>이는 Spring Boot가 제공하는 자동 구성의 몇 가지 예입니다. 동시에 Spring Boot는 방해가 되지 않습니다. 예를 들어 Thymeleaf가 경로에 있으면 Spring Boot는 자동으로 SpringTemplateEngine을 애플리케이션 컨텍스트에 추가합니다. 그러나 자신만의 설정으로 SpringTemplateEngine을 정의하면 Spring Boot는 이를 추가하지 않습니다. 이렇게 하면 약간의 노력으로도 제어할 수 있습니다.</p>
<blockquote>
<p>Spring Boot는 코드를 생성하거나 파일을 편집하지 않습니다. 대신, 애플리케이션을 시작하면 Spring Boot가 beans과 settings을 동적으로 연결하고 이를 애플리케이션 컨텍스트에 적용합니다.</p>
</blockquote>
<h2 id="create-a-simple-web-application">Create a Simple Web Application</h2>
<p>이제 다음 목록(src/main/java/guides/springboot/HelloController.java)에 표시된 대로 간단한 웹 애플리케이션을 위한 웹 컨트롤러를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>springboot</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">GetMapping</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">RestController</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HelloController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">index</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Greetings from Spring Boot!"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
<span class="token punctuation">}</span></code></pre>
<p>클래스는 <code>@RestController</code>로 표시됩니다. 이는 Spring MVC에서 웹 요청을 처리하는 데 사용할 준비가 되었음을 의미합니다. <code>@GetMapping</code>은 <code>/</code>를 <code>index()</code> 메소드에 매핑합니다. 브라우저에서 호출하거나 명령줄에서 컬을 사용하여 메서드를 호출하면 순수 텍스트가 반환됩니다. 이는 <code>@RestController</code>가 뷰가 아닌 데이터를 반환하는 웹 요청을 발생시키는 두 개의 주석인 <code>@Controller</code>와 <code>@ResponseBody</code>를 결합하기 때문입니다.</p>
<h2 id="애플리케이션-클래스-생성">애플리케이션 클래스 생성</h2>
<p>Spring Initializr는 간단한 애플리케이션 클래스를 생성합니다. 그러나 이 경우에는 너무 단순하다. 다음 목록(src/main/java/guides/springboot/Application.java)과 일치하도록 애플리케이션 클래스를 수정해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>springboot</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">CommandLineRunner</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot</span><span class="token punctuation">.</span><span class="token class-name">SpringApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>autoconfigure</span><span class="token punctuation">.</span><span class="token class-name">SpringBootApplication</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Bean</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">Arrays</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Application</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">Application</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">commandLineRunner</span><span class="token punctuation">(</span><span class="token class-name">ApplicationContext</span> ctx<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
            <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Let's inspect the beans provided by Spring Boot:"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            
            <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> beanNames <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBeanDefinitionNames</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token class-name">Arrays</span><span class="token punctuation">.</span><span class="token function">sort</span><span class="token punctuation">(</span>beanNames<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">String</span> beanName <span class="token operator">:</span> beanNames<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>beanName<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            
        <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ApplicationContext</code>와 <code>CommandLineRunner</code>는 스프링 프레임워크에서 다른 용도로 사용되는 것들입니다.</p>
<ol>
<li>
<p><strong>ApplicationContext:</strong></p>
<ul>
<li><code>ApplicationContext</code>는 스프링 컨테이너의 핵심 인터페이스입니다.</li>
<li>스프링 애플리케이션의 빈들을 관리하고, 빈들 간의 의존성을 주입해주는 역할을 합니다.</li>
<li>애플리케이션의 설정 정보를 로드하고, 빈들을 생성하고 관리하는데 사용됩니다.</li>
<li>스프링 애플리케이션 컨텍스트는 XML 기반 또는 자바 기반의 설정을 통해 생성됩니다.</li>
</ul>
</li>
<li>
<p><strong>CommandLineRunner:</strong></p>
<ul>
<li><code>CommandLineRunner</code>는 스프링 부트에서 제공하는 인터페이스로, 애플리케이션이 실행될 때 특정한 작업을 수행할 수 있도록 하는 역할을 합니다.</li>
<li><code>CommandLineRunner</code>를 구현한 빈을 등록하면, 스프링 부트 애플리케이션이 실행될 때 해당 빈의 <code>run</code> 메서드가 실행됩니다.</li>
<li>주로 애플리케이션을 초기화하거나 특정한 데이터를 로드하는 등의 작업을 수행하는 데 활용됩니다.</li>
</ul>
</li>
</ol>
<p><code>ApplicationContext</code>는 스프링의 핵심 컨테이너로써 빈들을 생성하고 관리하는데 사용되는 반면에, <code>CommandLineRunner</code>는 스프링 부트에서 제공하는 인터페이스로 애플리케이션이 실행될 때 특정한 작업을 수행하는데 활용됩니다.</p>
<p>코드에서 정의된 <code>args</code>는 실제로 <code>CommandLineRunner</code>의 <code>run</code> 메서드에서 전달되는 명령행 인수(arguments)가 아닙니다. </p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">CommandLineRunner</span> <span class="token function">commandLineRunner</span><span class="token punctuation">(</span><span class="token class-name">ApplicationContext</span> ctx<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> args <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
        <span class="token comment">// 여기서의 args는 커맨드 라인 실행 시에 전달되는 명령행 인수가 아닌, 빈을 정의하는 메서드 내에서 사용되는 매개변수입니다.</span>
        <span class="token comment">// 실제 애플리케이션 실행 시 전달되는 명령행 인수(arguments)와는 직접적인 관련이 없습니다.</span>
        <span class="token comment">// 이 부분에서 args는 빈을 정의하는 메서드에서 사용할 수 있는 임의의 이름으로 정의된 매개변수입니다.</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>CommandLineRunner</code>를 반환하는 <code>commandLineRunner</code> 빈 메서드 내에서 사용된 <code>args</code>는 단지 람다 표현식에서 사용하는 매개변수 이름으로 정의된 것일 뿐이며, 이는 실제 애플리케이션 실행 시 전달되는 명령행 인수(arguments)와는 직접적인 관련이 없습니다. </p>
<p>즉, <code>args -&gt; { ... }</code>에서의 <code>args</code>는 단순히 해당 메서드 내에서 사용되는 매개변수 이름일 뿐이며, 커맨드 라인에서 전달되는 실제 명령행 인수와는 무관합니다.</p>
<h2 id="run-the-application">Run the Application</h2>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/9b22c5a1-a4eb-4005-a01a-a5cb4038e0e3/image.png"></p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/e6290fdb-02a1-4ba3-91e3-ba6bc1670399/image.png"></p>
<p><code>org.springframework.boot.autoconfigure</code> 빈을 명확하게 볼 수 있습니다. <code>tomcatEmbeddedServletContainerFactory</code>도 있습니다.</p>
<hr>
<p><code>ApplicationContext</code>가 빈을 생성하고 초기화하는 과정에서, 빈들이 독립적으로 초기화되므로 빈들 간의 호출 순서를 보장할 수 없습니다. </p>
<p>따라서 코드에서 보여주는 것처럼 빈의 이름이나 ID에 따라 정렬되어 출력될 수 있습니다. 하지만 스프링에서는 빈들 간의 의존성을 고려하여 빈을 초기화하는 경우가 많기 때문에, 특정한 의존성이 있는 경우에는 해당 의존성에 따라 호출 순서가 조금 다를 수도 있습니다. </p>
<p>빈들 간의 순서를 보장하려면 <code>@DependsOn</code> 어노테이션을 사용하거나, <code>Ordered</code> 인터페이스를 구현하거나, <code>@Order</code> 어노테이션을 활용하여 순서를 지정할 수 있습니다. 하지만 이러한 방식은 빈들 간의 직접적인 의존성이 있는 경우에만 해당되며, 전체적인 빈들의 호출 순서를 완벽하게 보장하지는 않습니다.</p>
<hr>
<p>이제 다음 명령(출력과 함께 표시됨)을 실행하여 별도의 터미널 창에서 컬을 사용하여 서비스를 실행합니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> http://localhost:8080

Greetings from Spring Boot<span class="token operator">!</span></code></pre>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/d30e73e4-0005-46fd-8a11-1c9f8dc4ab82/image.png"></p>
<h2 id="add-unit-tests">Add Unit Tests</h2>
<p>추가한 엔드포인트에 대한 테스트를 추가하고 싶을 것이며 Spring Test는 이를 위한 몇 가지 메커니즘을 제공합니다.</p>
<p>Gradle을 사용하는 경우 <code>build.gradle</code> 파일에 다음 종속성을 추가하세요.</p>
<pre><code class="language-null">testImplementation('org.springframework.boot:spring-boot-starter-test')</code></pre>
<p>이제 다음 목록(src/test/java/guides/springboot/HelloControllerTest.java)에 표시된 대로 엔드포인트를 통해 서블릿 요청 및 응답을 모의하는 간단한 단위 테스트를 작성합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">guides<span class="token punctuation">.</span>springboot</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>autoconfigure<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">AutoConfigureMockMvc</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">MediaType</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet</span><span class="token punctuation">.</span><span class="token class-name">MockMvc</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>request</span><span class="token punctuation">.</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>hamcrest</span><span class="token punctuation">.</span><span class="token class-name">Matchers</span><span class="token punctuation">.</span>equalTo<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>servlet<span class="token punctuation">.</span>result</span><span class="token punctuation">.</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span>status<span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span>
<span class="token annotation punctuation">@AutoConfigureMockMvc</span>
<span class="token keyword">class</span> <span class="token class-name">ApplicationTests</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">MockMvc</span> mvc<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">getHello</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        mvc<span class="token punctuation">.</span><span class="token function">perform</span><span class="token punctuation">(</span><span class="token class-name">MockMvcRequestBuilders</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">accept</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isOk</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token class-name">MockMvcResultMatchers</span><span class="token punctuation">.</span><span class="token function">content</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">string</span><span class="token punctuation">(</span><span class="token function">equalTo</span><span class="token punctuation">(</span><span class="token string">"Greetings from Spring Boot!"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>MockMvc</code>는 Spring Test에서 제공되며 편리한 빌더 클래스 세트를 통해 <code>DispatcherServlet</code>에 HTTP 요청을 보내고 결과에 대한 어설션을 만들 수 있습니다. </p>
<hr>
<p><code>DispatcherServlet</code>은 스프링 MVC 프레임워크의 핵심이 되는 컨트롤러 중 하나입니다. 웹 요청을 받고, 처리하는데 사용됩니다. 웹 애플리케이션의 특정 URL 패턴과 매핑된 컨트롤러를 찾아서 해당 요청을 처리하고 그 결과를 반환합니다.</p>
<p>일반적으로 <code>DispatcherServlet</code>은 웹 애플리케이션의 <code>web.xml</code> 또는 Java Config 설정에서 설정됩니다. 클라이언트로부터 들어오는 모든 요청은 이 서블릿으로 전달되며, 이 서블릿은 적절한 핸들러(Controller)를 찾아서 요청을 전달하고 응답을 생성합니다.</p>
<p><code>DispatcherServlet</code>의 주요 역할은 다음과 같습니다:</p>
<ol>
<li>
<p><strong>URL 매핑 및 요청 전달:</strong> 클라이언트의 HTTP 요청을 받고, 해당 요청을 처리할 적절한 컨트롤러(핸들러)에게 전달합니다. 이때 URL 패턴과 매핑된 컨트롤러를 찾아서 요청을 라우팅합니다.</p>
</li>
<li>
<p><strong>뷰(View) 선택:</strong> 핸들러(Controller)가 요청을 처리하고 나면, <code>DispatcherServlet</code>은 해당 요청에 대한 응답으로 사용할 뷰를 선택합니다. 일반적으로 뷰는 JSP, Thymeleaf, Freemarker 등과 같은 템플릿 엔진을 통해 생성됩니다.</p>
</li>
<li>
<p><strong>모델 데이터 전달:</strong> 뷰에 전달할 모델 데이터를 설정합니다. 컨트롤러가 반환한 데이터는 뷰에 전달되어 웹 페이지를 구성하는 데 사용됩니다.</p>
</li>
<li>
<p><strong>핸들러 어댑터(HandlerAdapter) 호출:</strong> <code>DispatcherServlet</code>은 핸들러 어댑터를 사용하여 실제 요청을 처리할 핸들러(Controller)를 실행하고, 요청을 처리한 결과를 반환합니다.</p>
</li>
</ol>
<p>이러한 과정을 거쳐 <code>DispatcherServlet</code>은 웹 요청을 처리하고 적절한 응답을 생성하여 클라이언트에게 반환합니다. 이는 스프링 MVC에서 웹 애플리케이션의 요청-응답 주기를 관리하고, MVC 아키텍처에서의 역할을 담당합니다.</p>
<hr>
<p>결과에 대한 어설션을 만든다는 것은 테스트에서 기대한 결과를 확인하기 위해 원하는 조건을 설정하고 확인하는 과정을 말합니다. </p>
<p><code>MockMvc</code>를 사용하면 실제 HTTP 요청을 보내지 않고도 가상의 환경에서 Spring MVC 컨트롤러를 실행하고, 그 결과를 검증할 수 있습니다. 테스트 시에 예상한 동작을 확인하고 검증하기 위해 다양한 어설션(assertion) 메서드들을 사용할 수 있습니다.</p>
<p>예를 들어, 다음과 같은 어설션을 만들 수 있습니다:</p>
<ul>
<li><strong>HTTP 상태 코드 검증:</strong> 예상하는 HTTP 상태 코드(200, 404, 500 등)를 확인하여 요청이 제대로 처리되었는지 확인합니다.</li>
<li><strong>응답 본문 내용 확인:</strong> JSON, XML, HTML 등의 응답 본문을 검증하여 특정 필드나 값이 예상과 일치하는지 확인합니다.</li>
<li><strong>모델 속성 확인:</strong> 모델에 설정된 속성 값을 검증하여 컨트롤러가 올바른 데이터를 모델에 담았는지 확인합니다.</li>
<li><strong>리다이렉션 확인:</strong> 리다이렉션된 URL이 예상한 대로인지 확인합니다.</li>
<li><strong>뷰 이름 확인:</strong> 반환된 뷰의 이름이 예상한 뷰 이름과 일치하는지 확인합니다.</li>
</ul>
<p>이런 어설션들을 사용하여 특정 URL에 요청을 보내고, 그 결과를 검증하여 응답이 예상대로 잘 동작하는지 확인할 수 있습니다. 이를 통해 애플리케이션의 특정 부분이 예상한 대로 작동하는지 확인할 수 있게 됩니다.</p>
<hr>
<p><code>MockMvc</code> 인스턴스를 주입하기 위해 <code>@AutoConfigureMockMvc</code> 및 <code>@SpringBootTest</code>를 사용하는 것에 주목하세요. <code>@SpringBootTest</code>를 사용하면 전체 애플리케이션 컨텍스트가 생성되도록 요청합니다. 대안은 <code>@WebMvcTest</code>를 사용하여 컨텍스트의 웹 레이어만 생성하도록 Spring Boot에 요청하는 것입니다. 두 경우 모두 Spring Boot는 자동으로 애플리케이션의 기본 애플리케이션 클래스를 찾으려고 시도하지만 다른 것을 빌드하려는 경우 이를 재정의하거나 범위를 좁힐 수 있습니다.</p>
<p>HTTP 요청 주기를 모의하는 것 외에도 Spring Boot를 사용하여 간단한 전체 스택 통합 테스트를 작성할 수도 있습니다. 예를 들어, 이전에 표시된 모의 테스트 대신(또는 이와 마찬가지로) 다음 테스트를 생성할 수 있습니다(src/test/java/guides/springboot/HelloControllerIT.java에서).</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>example<span class="token punctuation">.</span>springboot</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit<span class="token punctuation">.</span>jupiter<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Test</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Autowired</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">SpringBootTest</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>boot<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client</span><span class="token punctuation">.</span><span class="token class-name">TestRestTemplate</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>http</span><span class="token punctuation">.</span><span class="token class-name">ResponseEntity</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>assertj<span class="token punctuation">.</span>core<span class="token punctuation">.</span>api</span><span class="token punctuation">.</span><span class="token class-name">Assertions</span><span class="token punctuation">.</span>assertThat<span class="token punctuation">;</span>

<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span>webEnvironment <span class="token operator">=</span> <span class="token class-name">SpringBootTest</span><span class="token punctuation">.</span><span class="token class-name">WebEnvironment</span><span class="token punctuation">.</span>RANDOM_PORT<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HelloControllerIT</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">TestRestTemplate</span> template<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">getHello</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
        <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> response <span class="token operator">=</span> template<span class="token punctuation">.</span><span class="token function">getForEntity</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertThat</span><span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">getBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Greetings from Spring Boot!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT</code>로 인해 임베디드 서버는 임의의 포트에서 시작되며 실제 포트는 <code>TestRestTemplate</code>의 기본 URL에 자동으로 구성됩니다.</p>
<hr>
<p><code>RestTemplate</code>과 <code>TestRestTemplate</code>은 Spring에서 제공하는 HTTP 통신을 위한 두 가지 서로 다른 클라이언트입니다.</p>
<ol>
<li>
<p><strong>RestTemplate:</strong></p>
<ul>
<li><code>RestTemplate</code>은 기본적으로 실제 HTTP 요청을 보내는 데 사용됩니다.</li>
<li>주로 실제 애플리케이션 코드에서 사용되며, 외부 API 호출이나 서버 간 통신 등의 용도로 활용됩니다.</li>
<li>예를 들어, 외부 API에 GET, POST, PUT, DELETE 등의 HTTP 메서드를 사용하여 요청을 보내고, 해당 요청에 대한 응답을 받아올 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>TestRestTemplate:</strong></p>
<ul>
<li><code>TestRestTemplate</code>은 테스트를 위한 목적으로 제공됩니다.</li>
<li>실제 HTTP 요청을 보내는 대신, 내부적으로 Servlet 컨테이너에 연결하여 테스트용 HTTP 요청을 처리합니다. </li>
<li>테스트 시에는 실제 서버가 아닌 내장 서버를 실행하고 이 서버에 요청을 보내어 테스트하는 데 사용됩니다.</li>
<li>따라서 <code>TestRestTemplate</code>을 사용하면 실제로 HTTP 통신을 수행하지만, 내장된 서버 환경에서 테스트가 이루어집니다.</li>
</ul>
</li>
</ol>
<p>위의 코드에서 <code>HelloControllerIT</code> 클래스는 Spring Boot 테스트를 수행하고 있는 것으로 보입니다. <code>@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)</code>를 사용하여 내장 서버를 랜덤 포트로 실행하고, <code>TestRestTemplate</code>을 사용하여 해당 서버에 HTTP 요청을 보내고 응답을 검증하고 있습니다. 이러한 접근 방식은 통합 테스트를 수행할 때, 내장 서버를 활용하여 테스트하고 그 결과를 검증하는 데 사용됩니다.</p>
<hr>
<p>내장 서버와 별도로 구성된 외부 서버를 사용하는 것 사이에는 몇 가지 차이가 있습니다.</p>
<ol>
<li>
<p><strong>테스트 환경의 격리:</strong></p>
<ul>
<li><strong>내장 서버:</strong> 내장 서버를 사용하면 애플리케이션을 테스트할 때 외부 서버에 종속되지 않고 격리된 환경에서 테스트할 수 있습니다. 테스트 케이스를 실행할 때마다 내장 서버가 자체적으로 구동되어 테스트가 이루어집니다.</li>
<li><strong>외부 서버:</strong> 외부 서버를 사용할 경우 해당 서버가 구동 중이어야 하며, 테스트가 이루어지는 환경이 외부 서버에 종속됩니다. 테스트 환경을 완전히 격리하기 어려울 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>테스트 속도와 안정성:</strong></p>
<ul>
<li><strong>내장 서버:</strong> 내장 서버는 테스트 시에 빠르게 구동되며, 테스트의 반복적인 실행에 용이합니다. 또한 네트워크 지연이나 외부 서버의 부하 등으로 인한 영향을 최소화하여 안정적인 테스트를 수행할 수 있습니다.</li>
<li><strong>외부 서버:</strong> 외부 서버를 사용하는 경우 네트워크 지연이나 외부 서버의 상태 등이 영향을 줄 수 있어 테스트의 안정성이나 속도에 영향을 줄 수 있습니다.</li>
</ul>
</li>
<li>
<p><strong>테스트 설정의 용이성:</strong></p>
<ul>
<li><strong>내장 서버:</strong> 내장 서버는 주로 테스트 환경을 위해 사전 구성된 설정을 사용하므로 테스트용으로 적합한 구성을 갖추고 있습니다.</li>
<li><strong>외부 서버:</strong> 외부 서버를 사용할 경우 외부 서버의 구성이나 설정을 테스트 환경에 맞게 조정해야 할 수 있으며, 이로 인해 테스트 설정이 복잡해질 수 있습니다.</li>
</ul>
</li>
</ol>
<p>내장 서버를 사용하는 것은 테스트를 더욱 효과적으로 수행하고 속도를 높일 수 있는 방법 중 하나이며, 특히 통합 테스트 시에 유용합니다. 외부 서버를 사용하는 경우 테스트 환경의 완전한 격리가 어려울 수 있지만, 실제 프로덕션 환경과 더 유사한 상황에서 테스트를 수행할 수 있습니다.</p>
<hr>
<p>네, 일반적으로 <code>localhost:8080</code>을 사용하는 경우에는 내장 서버를 활용하는 것입니다. <code>localhost</code>는 현재 컴퓨터의 자기 자신을 가리키는 호스트명이며, <code>8080</code>은 일반적으로 내장 웹 서버가 기본적으로 사용하는 포트 번호 중 하나입니다.</p>
<p>내장 서버가 실행 중인 경우, 보통 <code>localhost:8080</code>이나 다른 호스트와 포트를 통해 서비스를 제공하며, 이는 외부에서 접속할 수 있는 서버가 아니라 내부에서만 동작하는 서버입니다. 이러한 내장 서버는 특정한 프레임워크나 언어에 내장되어 있거나 해당 프로젝트가 별도의 웹 서버 없이 독립적으로 실행되는 경우에 사용됩니다. </p>
<p>보통은 개발 및 테스트 목적으로 내장 서버가 사용되며, 실제 프로덕션 환경에서는 Apache, Nginx, Tomcat과 같은 별도의 웹 서버가 사용될 수 있습니다.</p>
<hr>
<p>별도의 웹 서버를 활용하는 경우에는 해당 웹 서버의 호스트와 포트 번호 또는 도메인 주소를 사용합니다. 일반적으로 실제 프로덕션 환경에서는 웹 서버를 통해 클라이언트가 서비스에 접근하게 됩니다. 이 때 사용되는 주소는 서버의 IP 주소나 도메인 주소에 해당하는 주소입니다.</p>
<p>예를 들어:</p>
<ul>
<li>
<p><strong>IP 주소 사용:</strong> 웹 서버의 실제 IP 주소를 사용하여 접근할 수 있습니다. 예를 들어, <code>http://192.168.0.1</code>과 같이 웹 서버의 IP 주소를 입력하여 접근할 수 있습니다.</p>
</li>
<li>
<p><strong>도메인 주소 사용:</strong> 웹 서버에 연결된 도메인 주소를 사용하여 접근할 수 있습니다. 예를 들어, <code>http://www.example.com</code>과 같이 도메인 이름을 입력하여 웹 서버에 접근할 수 있습니다.</p>
</li>
</ul>
<p>웹 서버를 활용하는 경우 실제 주소는 서버가 호스팅되는 위치와 관련이 있으며, 해당 위치에 따라 IP 주소나 도메인 주소가 다를 수 있습니다. 이러한 주소를 통해 클라이언트는 별도의 웹 서버에 접속하여 해당 서버에서 호스팅되는 웹 애플리케이션에 접근하게 됩니다.</p>
<hr>
<p>이 두 코드 모두 <code>HelloController</code>에 대한 테스트를 수행하고 있어서 비슷한 기능을 하고 있습니다. 그러나 두 가지 중요한 차이가 있습니다.</p>
<ol>
<li>
<p><strong>테스트 방식:</strong> </p>
<ul>
<li><code>HelloControllerTest</code>는 Spring MVC의 <code>MockMvc</code>를 사용하여 컨트롤러를 테스트합니다. 이는 실제로 애플리케이션을 시작하지 않고, 내부적으로 MockMvc를 사용하여 컨트롤러의 동작을 시뮬레이트합니다.</li>
<li><code>HelloControllerIT</code>는 내장 서버를 사용하여 컨트롤러를 테스트합니다. <code>TestRestTemplate</code>을 사용하여 애플리케이션의 실제 인스턴스를 시작하고, 실제 HTTP 요청을 보내고 결과를 받아옵니다.</li>
</ul>
</li>
<li>
<p><strong>테스트 환경:</strong></p>
<ul>
<li><code>HelloControllerTest</code>는 <code>@SpringBootTest</code>를 사용하지만, 별도의 내장 서버를 실행하지 않습니다. 대신 <code>MockMvc</code>를 사용하여 서버를 시작하지 않고 컨트롤러를 단위 테스트합니다.</li>
<li><code>HelloControllerIT</code>는 <code>@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)</code>를 사용하여 내장 서버를 랜덤 포트로 실행하고, 실제 HTTP 요청을 보내며 테스트를 합니다.</li>
</ul>
</li>
</ol>
<p>첫 번째 테스트인 <code>HelloControllerTest</code>는 컨트롤러의 동작을 시뮬레이트하여 단위 테스트하는 데 사용되며, 내부적으로 서버를 시작하지 않습니다. 두 번째 테스트인 <code>HelloControllerIT</code>는 실제 내장 서버를 사용하여 통합 테스트를 수행합니다.</p>
<p>이 두 가지 접근 방식은 각각 단위 테스트와 통합 테스트의 목적과 환경에 따라 선택됩니다. 단위 테스트에서는 컨트롤러의 동작을 격리된 환경에서 테스트하고, 통합 테스트에서는 실제 서버 환경에서 요청과 응답을 테스트합니다.</p>
<p>--</p>
<p><code>@MockBean</code>은 스프링 테스트에서 사용되며, 테스트 시에 스프링 애플리케이션 컨텍스트에 가짜(Mock) 빈을 등록할 때 사용됩니다. 이는 실제 빈을 대체하여 특정 테스트 시나리오를 시뮬레이션하거나 테스트하고자 하는 대상에 대한 의존성을 대체하는 데 사용됩니다.</p>
<p>보통 다음과 같은 상황에서 <code>@MockBean</code>을 사용합니다:</p>
<ol>
<li>
<p><strong>테스트 환경 설정:</strong> 실제 구현이 아직 없는 경우에, 해당 빈을 대체하여 테스트 환경을 구축할 때 사용됩니다. 예를 들어, 외부 서비스 호출을 대체하기 위해 가짜 객체를 주입하는 등의 경우에 활용될 수 있습니다.</p>
</li>
<li>
<p><strong>테스트 케이스의 의도된 동작 시뮬레이션:</strong> 특정 시나리오를 테스트하고자 할 때 사용됩니다. 예를 들어, 에러 조건, 특정 응답, 또는 예외적인 상황을 시뮬레이션할 때 해당 빈을 Mock으로 대체하여 의도된 동작을 시험할 수 있습니다.</p>
</li>
</ol>
<p>예를 들어, 외부 서비스 호출을 하는 클래스가 있고, 이를 테스트하고 싶지만 실제 서비스는 테스트 중에 호출되면 안 되는 경우가 있습니다. 이 때 <code>@MockBean</code>을 사용하여 실제 서비스 대신 가짜(Mock) 객체를 주입하여 해당 호출을 시뮬레이션하고 응답을 조작하여 테스트를 수행할 수 있습니다.</p>
<hr>
<p><code>MockMvc</code>와 <code>@MockBean</code>은 스프링 테스트에서 다른 역할을 수행하는 것입니다.</p>
<ol>
<li>
<p><strong>MockMvc:</strong></p>
<ul>
<li><code>MockMvc</code>는 스프링 MVC 컨트롤러의 테스트를 지원하는 클래스입니다.</li>
<li>HTTP 요청 및 응답을 시뮬레이션하여 컨트롤러의 동작을 테스트할 수 있습니다.</li>
<li>내장 서버를 시작하지 않고 컨트롤러를 테스트할 때 사용됩니다.</li>
<li>주로 컨트롤러의 동작 및 응답을 검증하고 테스트하는 데 사용됩니다.</li>
</ul>
</li>
<li>
<p><strong>@MockBean:</strong></p>
<ul>
<li><code>@MockBean</code>은 테스트 시에 스프링 애플리케이션 컨텍스트에 가짜(Mock) 빈을 등록할 때 사용됩니다.</li>
<li>실제 빈을 대체하여 의도된 동작을 시뮬레이션하거나 특정 테스트 시나리오를 테스트할 때 사용됩니다.</li>
<li>주로 서비스나 컴포넌트 등의 빈을 테스트할 때 해당 빈이 의존하는 빈을 대체하고자 할 때 사용됩니다.</li>
</ul>
</li>
</ol>
<p>간단히 말하면, <code>MockMvc</code>는 컨트롤러의 동작과 HTTP 요청 및 응답을 테스트하는 데 사용되고, <code>@MockBean</code>은 테스트 시에 스프링 컨텍스트에 등록된 빈을 가짜(Mock)로 대체하여 특정 시나리오를 테스트하는 데 사용됩니다. 두 가지는 서로 다른 목적을 가지고 있으며, 테스트 시에 필요한 상황에 따라 각각 선택하여 사용됩니다.</p>
<h2 id="프로덕션-등급-서비스-추가">프로덕션 등급 서비스 추가</h2>
<p>귀하의 비즈니스를 위한 웹 사이트를 구축하는 경우 일부 관리 서비스를 추가해야 할 수도 있습니다. Spring Boot는 actuator 모듈을 통해 이러한 여러 서비스(예: 상태(health), 감사(audit), Bean 등)를 제공합니다.</p>
<p>Gradle을 사용하는 경우 <code>build.gradle</code> 파일에 다음 종속성을 추가하세요.</p>
<pre><code class="language-null">implementation 'org.springframework.boot:spring-boot-starter-actuator'</code></pre>
<p>터미널에 아래 명령을 입력하여 실행합니다.</p>
<pre><code class="language-shell">./gradlew bootRun</code></pre>
<p>새로운 RESTful 엔드포인트 세트가 애플리케이션에 추가된 것을 볼 수 있습니다. Spring Boot에서 제공하는 관리 서비스입니다. 다음 목록은 일반적인 출력을 보여줍니다.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/fc343c95-e1a0-43d7-aac7-88941df97fe6/image.png"></p>
<p>액추에이터는 다음을 노출합니다.</p>
<ul>
<li>actuator/health</li>
<li>actuator</li>
</ul>
<p><code>/actuator/shutdown</code> 엔드포인트도 있지만 기본적으로 JMX를 통해서만 표시됩니다. 이를 HTTP 끝점으로 활성화하려면 <code>application.properties</code> 파일에 <code>Management.endpoint.shutdown.enabled=true</code>를 추가하고 <code>Management.endpoints.web.exposure.include=health,info,shutdown</code>을 사용하여 노출합니다. 그러나 공개적으로 사용 가능한 애플리케이션에 대해서는 종료 엔드포인트를 활성화하면 안 됩니다.</p>
<hr>
<p>종료(Shutdown) 엔드포인트는 애플리케이션을 종료시키는 기능을 제공합니다. 이 기능은 보안상 중요하며, 애플리케이션에 민감한 영향을 미칠 수 있습니다. 그래서 보통 공개적으로 사용 가능한 애플리케이션에서는 종료 엔드포인트를 활성화하지 않습니다.</p>
<p>만약 종료 엔드포인트가 공개적으로 노출되어 있다면, 외부에서 이 엔드포인트를 호출하여 애플리케이션을 종료시킬 수 있습니다. 이것은 악의적인 공격자들에 의해 애플리케이션을 해치는 데 이용될 수 있습니다. 때문에 보안상의 이유로 일반적으로 공개적으로 접근 가능한 애플리케이션에서는 종료 엔드포인트를 활성화하지 않습니다.</p>
<p>따라서, 기본적으로 보안을 위해 공개적으로 사용 가능한 애플리케이션에서는 종료 엔드포인트를 활성화하지 않는 것이 일반적인 Best Practice입니다. 만약 애플리케이션이 내부 네트워크나 보안이 강화된 환경에서만 사용되거나 관리자가 제어하는 환경에서만 사용된다면 종료 엔드포인트를 활성화하는 것이 덜 위험할 수 있습니다.</p>
<hr>
<p>다음 명령을 실행하여 애플리케이션의 상태를 확인할 수 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> http://localhost:8080/actuator/health
<span class="token punctuation">{</span><span class="token string">"status"</span><span class="token builtin class-name">:</span><span class="token string">"UP"</span><span class="token punctuation">}</span></code></pre>
<p>또한, curl을 통해 종료를 호출하여 <code>application.properties</code>에 필요한 line(이전 참고에 표시됨)을 추가하지 않았을 때 어떤 일이 발생하는지 확인할 수도 있습니다.</p>
<pre><code class="language-shell">$ <span class="token function">curl</span> -X POST http://localhost:8080/actuator/shutdown
<span class="token punctuation">{</span><span class="token string">"timestamp"</span>:1401820343710,<span class="token string">"error"</span><span class="token builtin class-name">:</span><span class="token string">"Not Found"</span>,<span class="token string">"status"</span>:404,<span class="token string">"message"</span><span class="token builtin class-name">:</span><span class="token string">""</span>,<span class="token string">"path"</span><span class="token builtin class-name">:</span><span class="token string">"/actuator/shutdown"</span><span class="token punctuation">}</span></code></pre>
<p>활성화하지 않았기 때문에 요청한 엔드포인트를 사용할 수 없습니다(엔드포인트가 존재하지 않기 때문).</p>
<p>각 REST 엔드포인트에 대한 자세한 내용과 <code>application.properties</code> 파일(src/main/resources)을 사용하여 설정을 조정하는 방법은 엔드포인트에 대한 설명서를 참조하세요.</p>
<p><img src="https://velog.velcdn.com/images/dev_hammy/post/39a9c6b7-3e06-4a6f-ae34-a6e11701ba37/image.png"></p>