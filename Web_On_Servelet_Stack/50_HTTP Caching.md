<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/caching.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>HTTP 캐싱은 웹 애플리케이션의 성능을 크게 향상시킬 수 있습니다. HTTP 캐싱은 <code>Cache-Control</code> 응답 헤더와 그 이후의 조건부 요청 헤더(예: <code>Last-Modified</code> 및 <code>ETag</code>)를 중심으로 진행됩니다. <code>Cache-Control</code>은 private(예: 브라우저) 및 public(예: 프록시) 캐시에 응답을 캐시하고 재사용하는 방법을 advise합니다. <code>ETag</code> 헤더는 내용이 변경되지 않은 경우 본문 없이 304(NOT_MODIFIED)가 발생할 수 있는 조건부 요청을 만드는 데 사용됩니다. <code>ETag</code>는 <code>Last-Modified</code> 헤더의 보다 정교한 후속 버전으로 볼 수 있습니다.</p>
<p>이 섹션에서는 Spring Web MVC에서 사용할 수 있는 HTTP 캐싱 관련 옵션에 대해 설명합니다.</p>
<h1 id="cachecontrol"><code>CacheControl</code></h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/caching.html#webflux-caching-cachecontrol">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/http/CacheControl.html"><code>CacheControl</code></a>은 <code>Cache-Control</code> 헤더와 관련된 설정 구성을 지원하며 여러 위치에서 인수로 허용됩니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/mvc/WebContentInterceptor.html"><code>WebContentInterceptor</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/support/WebContentGenerator.html"><code>WebContentGenerator</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-caching.html#mvc-caching-etag-lastmodified">Controllers</a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-caching.html#mvc-caching-static-resources">Static Resources</a></p>
</li>
</ul>
<p><a href="https://datatracker.ietf.org/doc/html/rfc7234#section-5.2.2">RFC 7234</a>는 <code>Cache-Control</code> 응답 헤더에 대해 가능한 모든 지시문을 설명하지만 <code>CacheControl</code> 유형은 일반적인 시나리오에 초점을 맞춘 사용 사례 중심 접근 방식을 취합니다.</p>
<pre><code class="language-java"><span class="token comment">// Cache for an hour - "Cache-Control: max-age=3600"</span>
<span class="token class-name">CacheControl</span> ccCacheOneHour <span class="token operator">=</span> <span class="token class-name">CacheControl</span><span class="token punctuation">.</span><span class="token function">maxAge</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token class-name">TimeUnit</span><span class="token punctuation">.</span>HOURS<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Prevent caching - "Cache-Control: no-store"</span>
<span class="token class-name">CacheControl</span> ccNoStore <span class="token operator">=</span> <span class="token class-name">CacheControl</span><span class="token punctuation">.</span><span class="token function">noStore</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Cache for ten days in public and private caches,</span>
<span class="token comment">// public caches should not transform the response</span>
<span class="token comment">// "Cache-Control: max-age=864000, public, no-transform"</span>
<span class="token class-name">CacheControl</span> ccCustom <span class="token operator">=</span> <span class="token class-name">CacheControl</span><span class="token punctuation">.</span><span class="token function">maxAge</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">,</span> <span class="token class-name">TimeUnit</span><span class="token punctuation">.</span>DAYS<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">noTransform</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">cachePublic</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>WebContentGenerator</code>는 다음과 같이 작동하는 더 간단한 <code>cachePeriod</code> 속성(property)(초 단위로 정의)도 허용합니다.</p>
<ul>
<li>
<p><code>-1</code> 값은 `Cache-Control 응답 헤더를 생성하지 않습니다.</p>
</li>
<li>
<p>값이 <code>0</code>이면 <code>'Cache-Control: no-store'</code> 지시문을 사용하여 캐싱을 방지합니다.</p>
</li>
<li>
<p><code>n &gt; 0</code> 값은 <code>'Cache-Control: max-age=n'</code> 지시문을 사용하여 <code>n</code> 초 동안 지정된 응답을 캐시합니다.</p>
</li>
</ul>
<h1 id="contollers">Contollers</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/caching.html#webflux-caching-etag-lastmodified">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>컨트롤러는 HTTP 캐싱에 대한 명시적 지원을 추가할 수 있습니다. 조건부 요청 헤더와 비교하기 전에 리소스의 <code>lastModified</code> 또는 <code>ETag</code> 값을 계산해야 하므로 그렇게 하는 것이 좋습니다. 컨트롤러는 다음 예제와 같이 <code>ETag</code> 헤더와 <code>Cache-Control</code> 설정을 <code>ResponseEntity</code>에 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/book/{id}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Book</span><span class="token punctuation">&gt;</span></span> <span class="token function">showBook</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token class-name">Book</span> book <span class="token operator">=</span> <span class="token function">findBook</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">String</span> version <span class="token operator">=</span> book<span class="token punctuation">.</span><span class="token function">getVersion</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">return</span> <span class="token class-name">ResponseEntity</span>
			<span class="token punctuation">.</span><span class="token function">ok</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">cacheControl</span><span class="token punctuation">(</span><span class="token class-name">CacheControl</span><span class="token punctuation">.</span><span class="token function">maxAge</span><span class="token punctuation">(</span><span class="token number">30</span><span class="token punctuation">,</span> <span class="token class-name">TimeUnit</span><span class="token punctuation">.</span>DAYS<span class="token punctuation">)</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">eTag</span><span class="token punctuation">(</span>version<span class="token punctuation">)</span> <span class="token comment">// lastModified is also available</span>
			<span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span>book<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서는 조건부 요청 헤더와의 비교 결과 내용이 변경되지 않았음을 나타내는 경우 빈 본문과 함께 304(NOT_MODIFIED) 응답을 보냅니다. 그렇지 않으면 <code>ETag</code> 및 <code>Cache-Control</code> 헤더가 응답에 추가됩니다.</p>
<p>다음 예제와 같이 컨트롤러의 조건부 요청 헤더에 대해 확인할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RequestMapping</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">myHandleMethod</span><span class="token punctuation">(</span><span class="token class-name">WebRequest</span> request<span class="token punctuation">,</span> <span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token keyword">long</span> eTag <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token comment">// (1)</span>

	<span class="token keyword">if</span> <span class="token punctuation">(</span>request<span class="token punctuation">.</span><span class="token function">checkNotModified</span><span class="token punctuation">(</span>eTag<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>  <span class="token comment">// (2)</span>
	<span class="token punctuation">}</span>

	model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (3) </span>
	<span class="token keyword">return</span> <span class="token string">"myViewName"</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 애플리케이션별 계산.<br>
(2) 응답은 304(NOT_MODIFIED) — 추가 처리 없음으로 설정되었습니다.<br>
(3) 요청 처리를 계속하세요.</p>
<p><code>eTag</code> 값, <code>lastModified</code> 값 또는 둘 다를 기준으로 조건부 요청을 확인하는 세 가지 변형이 있습니다. 조건부 <code>GET</code> 및 <code>HEAD</code> 요청의 경우 응답을 304(NOT_MODIFIED)로 설정할 수 있습니다. 조건부 <code>POST</code>, <code>PUT</code> 및 <code>DELETE</code>의 경우 응답을 412(PRECONDITION_FAILED)로 설정하여 동시 수정을 방지할 수 있습니다.</p>
<h1 id="static-resources">Static Resources</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/caching.html#webflux-caching-static-resources">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>최적의 성능을 위해서는 <code>Cache-Control</code> 및 조건부 응답 헤더를 사용하여 정적 리소스를 제공해야 합니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/static-resources.html">Static Resources</a> 구성에 대한 섹션을 참조하세요.</p>
<h1 id="etag-filter"><code>ETag</code> Filter</h1>
<p><code>ShallowEtagHeaderFilter</code>를 사용하면 응답 콘텐츠에서 계산되는 "얕은" <code>eTag</code> 값을 추가할 수 있으므로 대역폭은 절약되지만 CPU 시간은 절약되지 않습니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-shallow-etag">얕은 ETag</a>를 참조하세요.</p>