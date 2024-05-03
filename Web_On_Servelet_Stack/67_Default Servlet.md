<p>Spring MVC는 <code>DispatcherServlet</code>을 <code>/</code>에 매핑(따라서 컨테이너의 기본 서블릿 매핑을 재정의함)하는 동시에 static 리소스 요청이 컨테이너의 기본 서블릿에 의해 처리되도록 허용합니다. <code>/**</code>의 URL 매핑과 다른 URL 매핑에 비해 우선순위가 가장 낮은 <code>DefaultServletHttpRequestHandler</code>를 구성합니다.</p>
<p>이 핸들러는 모든 요청을 기본 서블릿으로 전달합니다. 따라서 다른 모든 URL <code>HandlerMappings</code>의 순서에서 마지막에 남아 있어야 합니다. <code>&lt;mvc:annotation-driven&gt;</code>을 사용하는 경우가 그렇습니다. 또는 자체적으로 사용자 정의된 <code>HandlerMapping</code> 인스턴스를 설정하는 경우 <code>order</code> 속성(property)을 <code>DefaultServletHttpRequestHandler</code>의 값(<code>Integer.MAX_VALUE</code>)보다 낮은 값으로 설정해야 합니다.</p>
<p>다음 예에서는 기본 설정을 사용하여 기능을 활성화하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureDefaultServletHandling</span><span class="token punctuation">(</span><span class="token class-name">DefaultServletHandlerConfigurer</span> configurer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		configurer<span class="token punctuation">.</span><span class="token function">enable</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>default-servlet-handler</span><span class="token punctuation">/&gt;</span></span></code></pre>
<p><code>/</code> Servlet 매핑을 재정의할 때 주의할 점은 기본 Servlet에 대한 <code>RequestDispatcher</code>를 경로가 아닌 이름으로 검색해야 한다는 것입니다. <code>DefaultServletHttpRequestHandler</code>는 대부분의 주요 서블릿 컨테이너(Tomcat, Jetty, GlassFish, JBoss, Resin, WebLogic 및 WebSphere 포함)에 대해 알려진 이름 목록을 사용하여 시작 시 컨테이너의 기본 서블릿을 자동 감지하려고 시도합니다. 기본 서블릿이 다른 이름으로 사용자 정의 구성되었거나 기본 서블릿 이름을 알 수 없는 다른 서블릿 컨테이너가 사용되는 경우 다음 예와 같이 기본 서블릿 이름을 명시적으로 제공해야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureDefaultServletHandling</span><span class="token punctuation">(</span><span class="token class-name">DefaultServletHandlerConfigurer</span> configurer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		configurer<span class="token punctuation">.</span><span class="token function">enable</span><span class="token punctuation">(</span><span class="token string">"myCustomDefaultServlet"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>default-servlet-handler</span> <span class="token attr-name">default-servlet-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myCustomDefaultServlet<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>