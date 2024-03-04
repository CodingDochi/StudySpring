<p>서블릿 환경에서는 대안으로 또는 <code>web.xml</code> 파일과 함께 프로그래밍 방식으로 서블릿 컨테이너를 구성하는 옵션이 있습니다. 다음 예에서는 <code>DispatcherServlet</code>을 등록합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web</span><span class="token punctuation">.</span><span class="token class-name">WebApplicationInitializer</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebApplicationInitializer</span> <span class="token keyword">implements</span> <span class="token class-name">WebApplicationInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onStartup</span><span class="token punctuation">(</span><span class="token class-name">ServletContext</span> container<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">XmlWebApplicationContext</span> appContext <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">XmlWebApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		appContext<span class="token punctuation">.</span><span class="token function">setConfigLocation</span><span class="token punctuation">(</span><span class="token string">"/WEB-INF/spring/dispatcher-config.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token class-name">ServletRegistration</span><span class="token punctuation">.</span><span class="token class-name">Dynamic</span> registration <span class="token operator">=</span> container<span class="token punctuation">.</span><span class="token function">addServlet</span><span class="token punctuation">(</span><span class="token string">"dispatcher"</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">DispatcherServlet</span><span class="token punctuation">(</span>appContext<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registration<span class="token punctuation">.</span><span class="token function">setLoadOnStartup</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registration<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>WebApplicationInitializer</code>는 Spring MVC에서 제공하는 인터페이스이며, 당신의 구현(implementation)이 감지되고 자동으로 모든 Servlet 3 컨테이너를 초기화하는 데 사용되도록 보장합니다. <code>AbstractDispatcherServletInitializer</code>라는 <code>WebApplicationInitializer</code>의 추상 기본 클래스 구현을 사용하면, 서블릿 매핑과 <code>DispatcherServlet</code> 구성의 위치를 지정하는 메서드를 재정의하여 <code>DispatcherServlet</code>을 훨씬 쉽게 등록할 수 있습니다.</p>
<p>이는 다음 예제와 같이 Java 기반 Spring 구성을 사용하는 애플리케이션에 권장됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebAppInitializer</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractAnnotationConfigDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getRootConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token class-name">MyWebConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletMappings</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token string">"/"</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>XML 기반 Spring 구성을 사용하는 경우 다음 예제와 같이 <code>AbstractDispatcherServletInitializer</code>에서 직접 확장해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebAppInitializer</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">WebApplicationContext</span> <span class="token function">createRootApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">WebApplicationContext</span> <span class="token function">createServletApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">XmlWebApplicationContext</span> cxt <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">XmlWebApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		cxt<span class="token punctuation">.</span><span class="token function">setConfigLocation</span><span class="token punctuation">(</span><span class="token string">"/WEB-INF/spring/dispatcher-config.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> cxt<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletMappings</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token string">"/"</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>AbstractDispatcherServletInitializer</code>는 또한 다음 예제와 같이 <code>Filter</code> 인스턴스를 추가하고 자동으로 <code>DispatcherServlet</code>에 매핑되도록 하는 편리한 방법을 제공합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebAppInitializer</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Filter</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletFilters</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Filter</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span>
			<span class="token keyword">new</span> <span class="token class-name">HiddenHttpMethodFilter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">CharacterEncodingFilter</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>각 필터는 구체적인 유형에 따라 기본 이름으로 추가되고 자동으로 <code>DispatcherServlet</code>에 매핑됩니다.</p>
<p><code>AbstractDispatcherServletInitializer</code>의 <code>isAsyncSupported</code> protected 메소드는 <code>DispatcherServlet</code> 및 여기에 매핑된 모든 필터에 대한 비동기 지원을 활성화할 수 있는 단일 위치를 제공합니다. 기본적으로 이 플래그는 <code>true</code>로 설정됩니다.</p>
<p>마지막으로 <code>DispatcherServlet</code> 자체를 추가로 사용자 정의해야 하는 경우 <code>createDispatcherServlet</code> 메서드를 재정의할 수 있습니다.</p>