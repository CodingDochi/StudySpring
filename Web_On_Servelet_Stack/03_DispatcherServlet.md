<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/dispatcher-handler.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>다른 많은 웹 프레임워크와 마찬가지로 Spring MVC는 중앙 <code>Servlet</code>인 <code>DispatcherServlet</code>이 요청 처리를 위한 공유 알고리즘을 제공하는 전면 컨트롤러 패턴을 중심으로 설계되었으며 실제 작업은 구성 가능한 위임 컴포넌트에 의해 수행됩니다. 이 모델은 유연하며 다양한 워크플로를 지원합니다.</p>
<p><code>DispatcherServlet</code>은 모든 <code>Servlet</code>과 마찬가지로 Java 구성을 사용하거나 <code>web.xml</code>에서 서블릿 사양에 따라 선언되고 매핑되어야 합니다. 차례로 <code>DispatcherServlet</code>은 Spring 구성을 사용하여 요청 매핑, 뷰 해결, 예외 처리 등에 필요한 위임 컴포넌트를 검색합니다.</p>
<p>다음 Java 구성 예는 Servlet 컨테이너에 의해 자동 감지되는 <code>DispatcherServlet</code>을 등록하고 초기화합니다(<a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/container-config.html">Servlet Config</a> 참조).</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebApplicationInitializer</span> <span class="token keyword">implements</span> <span class="token class-name">WebApplicationInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onStartup</span><span class="token punctuation">(</span><span class="token class-name">ServletContext</span> servletContext<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token comment">// Load Spring web application configuration</span>
		<span class="token class-name">AnnotationConfigWebApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigWebApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		context<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span><span class="token class-name">AppConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Create and register the DispatcherServlet</span>
		<span class="token class-name">DispatcherServlet</span> servlet <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DispatcherServlet</span><span class="token punctuation">(</span>context<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">ServletRegistration</span><span class="token punctuation">.</span><span class="token class-name">Dynamic</span> registration <span class="token operator">=</span> servletContext<span class="token punctuation">.</span><span class="token function">addServlet</span><span class="token punctuation">(</span><span class="token string">"app"</span><span class="token punctuation">,</span> servlet<span class="token punctuation">)</span><span class="token punctuation">;</span>
		registration<span class="token punctuation">.</span><span class="token function">setLoadOnStartup</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registration<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/app/*"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
ServletContext API를 직접 사용하는 것 외에도 <code>AbstractAnnotationConfigDispatcherServletInitializer</code>를 확장하고 특정 메서드를 재정의할 수도 있습니다(<a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/context-hierarchy.html">컨텍스트 계층</a> 아래의 예 참조).</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
프로그래밍 방식 사용 사례의 경우 <code>GenericWebApplicationContext</code>를 <code>AnnotationConfigWebApplicationContext</code> 대신 사용할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/web/context/support/GenericWebApplicationContext.html"><code>GenericWebApplicationContext</code></a> javadoc을 참조하세요.</p>
</blockquote>
<p>다음 <code>web.xml</code> 구성 예는 <code>DispatcherServlet</code>을 등록하고 초기화합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.context.ContextLoaderListener<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener-class</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>context-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>/WEB-INF/app-context.xml<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>context-param</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>app<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.servlet.DispatcherServlet<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-class</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>init-param</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>init-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>load-on-startup</span><span class="token punctuation">&gt;</span></span>1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>load-on-startup</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>app<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>url-pattern</span><span class="token punctuation">&gt;</span></span>/app/*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>url-pattern</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Boot는 다른 초기화 순서를 따릅니다. Spring Boot는 서블릿 컨테이너의 라이프사이클에 연결(hooking into)하는 대신 Spring 구성을 사용하여 자체 및 내장된 서블릿 컨테이너를 부트스트랩합니다. <code>Filter</code> 및 <code>Servlet</code> 선언은 Spring 구성에서 감지되고 서블릿 컨테이너에 등록됩니다. 자세한 내용은 <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/web.html#web.servlet.embedded-container">Spring Boot 설명서</a>를 참조하세요.</p>
</blockquote>