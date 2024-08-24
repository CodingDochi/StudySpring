<p>다음 섹션에서는 Spring 3.0에 도입된 Spring의 <code>nnotationConfigApplicationContext</code>를 문서화합니다. 이 다재다능한 <code>ApplicationContext</code> 구현은 <code>@Configuration</code> 클래스뿐만 아니라 일반 <code>@Component 클래스</code>와 JSR-330 메타데이터로 어노테이션이 달린 클래스도 입력으로 허용할 수 있습니다.</p>
<p><code>@Configuration </code>클래스가 입력으로 제공되면 <code>@Configuration</code> 클래스 자체가 Bean 정의로 등록되고 클래스 내에서 선언된 모든 <code>@Bean</code> 메소드도 Bean 정의로 등록됩니다.</p>
<p><code>@Component</code> 및 JSR-330 클래스가 제공되면 Bean 정의로 등록되며, 필요한 경우 해당 클래스 내에서<code> @Autowired</code> 또는 <code>@Inject</code>와 같은 DI 메타데이터가 사용된다고 가정합니다.</p>
<h1 id="simple-construction">Simple Construction</h1>
<p><code>ClassPathXmlApplicationContext</code>를 인스턴스화할 때 Spring XML 파일을 입력으로 사용하는 것과 거의 같은 방식으로 <code>AnnotationConfigApplicationContext</code>를 인스턴스화할 때 <code>@Configuration</code> 클래스를 입력으로 사용할 수 있습니다. 이는 다음 예제에서 볼 수 있듯이 Spring 컨테이너를 XML 없이 완전히 사용할 수 있게 해줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">AppConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">MyService</span> myService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">MyService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	myService<span class="token punctuation">.</span><span class="token function">doStuff</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>앞서 언급했듯이 <code>AnnotationConfigApplicationContext</code>는 <code>@Configuration</code> 클래스에서만 작동하도록 제한되지 않습니다. 다음 예제와 같이 <code>@Component</code> 또는 JSR-330 어노테이션이 달린 클래스를 생성자에 대한 입력으로 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">MyServiceImpl</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">Dependency1</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">Dependency2</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">MyService</span> myService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">MyService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	myService<span class="token punctuation">.</span><span class="token function">doStuff</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예제에서는 <code>MyServiceImpl</code>, <code>Dependency1</code> 및 <code>Dependency2</code>가 <code>@Autowired</code>와 같은 Spring 종속성 주입 주석을 사용한다고 가정합니다.</p>
<h1 id="building-the-container-programmatically-by-using-registerclass">Building the Container Programmatically by Using <code>register(Class&lt;?&gt;...)</code></h1>
<p>인수가 없는 생성자(no-arg construdtor)를 사용하여 <code>AnnotationConfigApplicationContext</code>를 인스턴스화한 다음, <code>register()</code> 메서드를 사용하여 구성할 수 있습니다. 이 접근 방식은 <code>AnnotationConfigApplicationContext</code>를 프로그래밍 방식으로 구축할 때 특히 유용합니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AnnotationConfigApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ctx<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span><span class="token class-name">AppConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">OtherConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ctx<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span><span class="token class-name">AdditionalConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ctx<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">MyService</span> myService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">MyService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	myService<span class="token punctuation">.</span><span class="token function">doStuff</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="enabling-component-scanning-with-scanstring">Enabling Component Scanning with <code>scan(String...)</code></h1>
<p>component scanning을 활성화하려면 다음과 같이 <code>@Configuration</code> 클래스에 어노테이션을 추가하면 됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"com.acme"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span>  <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 이 어노테이션을 사용하면 component scanning이 가능해집니다. </p>
<blockquote>
<p><strong>[Tip]</strong><br>
숙련된 Spring 사용자는 다음 예제에 표시된 Spring의 <code>context:</code> 네임스페이스와 동등한 XML 선언에 익숙할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
</blockquote>
<p>이전 예제에서는 <code>@Component</code> 어노테이션이 달린 클래스를 찾기 위해 <code>com.acme</code> 패키지를 스캔하고 해당 클래스는 컨테이너 내에 Spring Bean 정의로 등록됩니다. <code>AnnotationConfigApplicationContext</code>는 다음 예제와 같이 동일한 component-scanning 기능을 허용하기 위해 <code>scan(String…​)</code> 메서드를 노출합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AnnotationConfigApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ctx<span class="token punctuation">.</span><span class="token function">scan</span><span class="token punctuation">(</span><span class="token string">"com.acme"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	ctx<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">MyService</span> myService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">MyService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Configuration</code> 클래스는 <code>@Component</code>로 메타 어노테이션을 달았으므로 컴포넌트 검색의 후보라는 점을 기억하세요. 앞의 예에서는 <code>AppConfig</code>가 <code>com.acme</code> 패키지(또는 그 아래의 모든 패키지) 내에서 선언되었다고 가정하고 이는 <code>scan()</code> 호출 중에 선택됩니다. <code>refresh()</code> 시 모든 <code>@Bean</code> 메소드가 처리되어 컨테이너 내에서 Bean 정의로 등록됩니다.</p>
</blockquote>
<h1 id="support-for-web-applications-with-annotationconfigwebapplicationcontext">Support for Web Applications with <code>AnnotationConfigWebApplicationContext</code></h1>
<p><code>AnnotationConfigApplicationContext</code>의 <code>WebApplicationContext</code> 변형은 <code>AnnotationConfigWebApplicationContext</code>와 함께 사용할 수 있습니다. Spring <code>ContextLoaderListener</code> 서블릿 리스너, Spring MVC <code>DispatcherServlet</code> 등을 구성(configure)할 때 이 구현을 사용할 수 있습니다. 다음 <code>web.xml</code> 조각은 일반적인 Spring MVC 웹 애플리케이션을 구성합니다(<code>contextClass</code> context-param 및 init-param 사용에 유의하세요).</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- Configure ContextLoaderListener to use AnnotationConfigWebApplicationContext
		instead of the default XmlWebApplicationContext --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>context-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextClass<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>
			org.springframework.web.context.support.AnnotationConfigWebApplicationContext
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>context-param</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- Configuration locations must consist of one or more comma- or space-delimited
		fully-qualified @Configuration classes. Fully-qualified packages may also be
		specified for component-scanning --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>context-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>com.acme.AppConfig<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>context-param</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- Bootstrap the root application context as usual using ContextLoaderListener --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.context.ContextLoaderListener<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener-class</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- Declare a Spring MVC DispatcherServlet as usual --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>dispatcher<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.servlet.DispatcherServlet<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-class</span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext
			instead of the default XmlWebApplicationContext --&gt;</span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>init-param</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextClass<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>
				org.springframework.web.context.support.AnnotationConfigWebApplicationContext
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>init-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- Again, config locations must consist of one or more comma- or space-delimited
			and fully-qualified @Configuration classes --&gt;</span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>init-param</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>com.acme.web.MvcConfig<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>init-param</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- map all requests for /app/* to the dispatcher servlet --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>dispatcher<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>url-pattern</span><span class="token punctuation">&gt;</span></span>/app/*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>url-pattern</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
프로그래밍 방식 사용 사례의 경우 <code>GenericWebApplicationContext</code>를 <code>AnnotationConfigWebApplicationContext</code> 대신 사용할 수 있습니다. 자세한 내용은 <code>GenericWebApplicationContext</code> javadoc을 참조하세요.</p>
</blockquote>