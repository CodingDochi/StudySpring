<p><code>DispatcherServlet</code>은 자체 구성을 위해 <code>WebApplicationContext</code>(일반 <code>ApplicationContext</code>의 확장)를 기대합니다. <code>WebApplicationContext</code>에는 <code>ServletContext</code> 및 이와 연관된 <code>Servlet</code>에 대한 링크가 있습니다. 또한 애플리케이션이 <code>WebApplicationContext</code>에 액세스해야 하는 경우 <code>RequestContextUtils</code>의 정적 메소드를 사용하여 <code>WebApplicationContext</code>를 조회할 수 있도록 <code>ServletContext</code>에 바인딩됩니다.</p>
<p>많은 애플리케이션의 경우 단일 <code>WebApplicationContext</code>를 갖는 것만으로도 간단하고 충분합니다. 하나의 루트 <code>WebApplicationContext</code>가 각각 자체 하위 <code>WebApplicationContext</code> 구성을 갖는 여러 <code>DispatcherServlet</code>(또는 다른 <code>Servlet</code>) 인스턴스에서 공유되는 컨텍스트 계층 구조를 갖는 것도 가능합니다. 컨텍스트 계층 기능에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html"><code>ApplicationContext</code>의 추가 기능</a>을 참조하세요.</p>
<p>루트 <code>WebApplicationContext</code>에는 일반적으로 여러 <code>Servlet</code> 인스턴스에서 공유해야 하는 데이터 저장소 및 비즈니스 서비스와 같은 인프라 Bean이 포함되어 있습니다. 이러한 Bean은 효과적으로 상속되며 일반적으로 지정된 <code>Servlet</code>에 대한 로컬 Bean을 포함하는 <code>Servlet</code> 관련 하위 <code>WebApplicationContext</code>에서 재정의(즉, 다시 선언)될 수 있습니다. 다음 이미지는 이 관계를 보여줍니다.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/mvc-context-hierarchy.png"></p>
<p>다음 예에서는 <code>WebApplicationContext</code> 계층 구조를 구성합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyWebAppInitializer</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractAnnotationConfigDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getRootConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token class-name">RootConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token class-name">App1Config</span><span class="token punctuation">.</span><span class="token keyword">class</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletMappings</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span> <span class="token string">"/app1/*"</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
애플리케이션 컨텍스트 계층 구조가 필요하지 않은 경우 애플리케이션은 <code>getRootConfigClasses()</code>를 통해 모든 구성을 반환하고 <code>getServletConfigClasses()</code>에서 <code>null</code>을 반환할 수 있습니다. 다음 예에서는 <code>web.xml</code>에 해당하는 내용을 보여줍니다.</p>
</blockquote>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>web-app</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.context.ContextLoaderListener<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener-class</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>context-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>/WEB-INF/root-context.xml<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>context-param</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>app1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.servlet.DispatcherServlet<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-class</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>init-param</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>/WEB-INF/app1-context.xml<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>init-param</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>load-on-startup</span><span class="token punctuation">&gt;</span></span>1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>load-on-startup</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>servlet-name</span><span class="token punctuation">&gt;</span></span>app1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-name</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>url-pattern</span><span class="token punctuation">&gt;</span></span>/app1/*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>url-pattern</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>servlet-mapping</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>web-app</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
애플리케이션 컨텍스트 계층이 필요하지 않은 경우 애플리케이션은 "루트" 컨텍스트만 구성하고 <code>contextConfigLocation</code> Servlet 매개변수를 비워 둘 수 있습니다.</p>
</blockquote>