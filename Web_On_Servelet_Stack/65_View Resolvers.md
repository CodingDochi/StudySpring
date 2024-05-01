<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-view-resolvers">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>MVC 구성은 뷰 resolver의 등록을 단순화합니다.</p>
<p>다음 Java 구성 예에서는 JSP 및 Jackson을 JSON 렌더링의 기본 <code>View</code>로 사용하여 콘텐츠 협상 <code>View</code> resolution을 구성합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureViewResolvers</span><span class="token punctuation">(</span><span class="token class-name">ViewResolverRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">enableContentNegotiation</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MappingJackson2JsonView</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registry<span class="token punctuation">.</span><span class="token function">jsp</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>view-resolvers</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>content-negotiation</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>default-views</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.view.json.MappingJackson2JsonView<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>default-views</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>content-negotiation</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>jsp</span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>view-resolvers</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>그러나 FreeMarker, Groovy Markup 및 스크립트 템플릿에도 기본 뷰 기술의 구성이 필요합니다. MVC 네임스페이스는 전용 요소를 제공합니다. 다음 예는 FreeMarker에서 작동합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>view-resolvers</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>content-negotiation</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>default-views</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.servlet.view.json.MappingJackson2JsonView<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>default-views</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>content-negotiation</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>freemarker</span> <span class="token attr-name">cache</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>false<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>view-resolvers</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>freemarker-configurer</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>template-loader-path</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/freemarker<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>freemarker-configurer</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>Java 구성에서는 다음 예제와 같이 해당 <code>Configurer</code> 빈을 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureViewResolvers</span><span class="token punctuation">(</span><span class="token class-name">ViewResolverRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		registry<span class="token punctuation">.</span><span class="token function">enableContentNegotiation</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MappingJackson2JsonView</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registry<span class="token punctuation">.</span><span class="token function">freeMarker</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">cache</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">FreeMarkerConfigurer</span> <span class="token function">freeMarkerConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">FreeMarkerConfigurer</span> configurer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">FreeMarkerConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		configurer<span class="token punctuation">.</span><span class="token function">setTemplateLoaderPath</span><span class="token punctuation">(</span><span class="token string">"/freemarker"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> configurer<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>