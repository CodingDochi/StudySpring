<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-content-negotiation">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC가 요청에서 요청된 미디어 유형(예: <code>Accept</code> 헤더, URL 경로 확장, 쿼리 매개변수 등)을 결정하는 방법을 구성할 수 있습니다.</p>
<p>기본적으로 <code>Accept</code> 헤더만 선택됩니다.</p>
<p>URL 기반 콘텐츠 유형 확인을 사용해야 하는 경우 경로 확장에 대한 쿼리 매개 변수 전략을 사용하는 것이 좋습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-suffix-pattern-match">접미사 matching</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html#mvc-ann-requestmapping-rfd">접미사 matching과 RFD</a>를 참조하세요.</p>
<p>Java 구성에서는 다음 예제와 같이 요청된 콘텐츠 유형 확인을 사용자 정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureContentNegotiation</span><span class="token punctuation">(</span><span class="token class-name">ContentNegotiationConfigurer</span> configurer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		configurer<span class="token punctuation">.</span><span class="token function">mediaType</span><span class="token punctuation">(</span><span class="token string">"json"</span><span class="token punctuation">,</span> <span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span><span class="token punctuation">;</span>
		configurer<span class="token punctuation">.</span><span class="token function">mediaType</span><span class="token punctuation">(</span><span class="token string">"xml"</span><span class="token punctuation">,</span> <span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_XML<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span> <span class="token attr-name">content-negotiation-manager</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>contentNegotiationManager<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>contentNegotiationManager<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.web.accept.ContentNegotiationManagerFactoryBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>mediaTypes<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>
			json=application/json
			xml=application/xml
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>