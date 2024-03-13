<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-logging">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC의 DEBUG 수준 로깅은 컴팩트하고 최소한이며 인간 친화적으로 설계되었습니다. 이는 특정 문제를 디버깅할 때만 유용한 다른 정보에 비해 계속해서 유용한 고가치 정보에 중점을 둡니다.</p>
<p>TRACE 수준 로깅은 일반적으로 DEBUG와 동일한 원칙을 따르지만(예를 들어 소방 호스도 아니어야 함) 모든 문제를 디버깅하는 데 사용할 수 있습니다. 또한 일부 로그 메시지는 TRACE와 DEBUG에서 서로 다른 수준의 세부정보를 표시할 수 있습니다.</p>
<p>좋은 로깅은 로그 사용 경험에서 비롯됩니다. 명시된 목표를 달성하지 못하는 부분을 발견하면 알려주시기 바랍니다.</p>
<h1 id="sensitive-data">Sensitive Data</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/reactive-spring.html#webflux-logging-sensitive-data">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>DEBUG 및 TRACE 로깅은 민감한 정보를 기록할 수 있습니다. 이것이 요청 매개변수와 헤더가 기본적으로 마스크되어 있고 전체 로깅이 <code>DispatcherServlet</code>의 <code>enableLoggingRequestDetails</code> 속성을 통해 명시적으로 활성화되어야 하는 이유입니다.</p>
<p>다음 예에서는 Java 구성을 사용하여 이를 수행하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyInitializer</span>
		<span class="token keyword">extends</span> <span class="token class-name">AbstractAnnotationConfigDispatcherServletInitializer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getRootConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">Class</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletConfigClasses</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token function">getServletMappings</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">customizeRegistration</span><span class="token punctuation">(</span><span class="token class-name">ServletRegistration</span><span class="token punctuation">.</span><span class="token class-name">Dynamic</span> registration<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registration<span class="token punctuation">.</span><span class="token function">setInitParameter</span><span class="token punctuation">(</span><span class="token string">"enableLoggingRequestDetails"</span><span class="token punctuation">,</span> <span class="token string">"true"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>