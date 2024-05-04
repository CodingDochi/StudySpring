<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-path-matching">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>path matching 및 URL 처리와 관련된 옵션을 사용자 정의할 수 있습니다. 개별 옵션에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/config/annotation/PathMatchConfigurer.html"><code>PathMatchConfigurer</code></a> javadoc를 참조하세요.</p>
<p>다음 예에서는 Java 구성에서 path matching를 사용자 정의하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configurePathMatch</span><span class="token punctuation">(</span><span class="token class-name">PathMatchConfigurer</span> configurer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		configurer<span class="token punctuation">.</span><span class="token function">addPathPrefix</span><span class="token punctuation">(</span><span class="token string">"/api"</span><span class="token punctuation">,</span> <span class="token class-name">HandlerTypePredicate</span><span class="token punctuation">.</span><span class="token function">forAnnotation</span><span class="token punctuation">(</span><span class="token class-name">RestController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">private</span> <span class="token class-name">PathPatternParser</span> <span class="token function">patternParser</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML 구성에서 path matching를 사용자 정의하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>path-matching</span>
		<span class="token attr-name">path-helper</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>pathHelper<span class="token punctuation">"</span></span>
		<span class="token attr-name">path-matcher</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>pathMatcher<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>annotation-driven</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>pathHelper<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.app.MyPathHelper<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>pathMatcher<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.app.MyPathMatcher<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>