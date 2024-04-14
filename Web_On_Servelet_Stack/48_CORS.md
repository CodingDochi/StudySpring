<p>Spring MVC를 사용하면 CORS(Cross-Origin Resource Sharing)를 처리할 수 있습니다. 이 섹션에서는 이를 수행하는 방법을 설명합니다.</p>
<h1 id="introduction">Introduction</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-intro">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>보안상의 이유로 브라우저는 현재 출처 외부의 리소스에 대한 AJAX 호출을 금지합니다. 예를 들어, 한 탭에는 은행 계좌를 두고 다른 탭에는 evil.com을 둘 수 있습니다. evil.com의 스크립트는 귀하의 자격 증명을 사용하여 은행 API에 AJAX 요청을 할 수 없어야 합니다. 예를 들어 귀하의 계좌에서 돈을 인출하는 경우입니다!</p>
<p>CORS(Cross-Origin Resource Sharing)는 IFRAME 또는 JSONP를 기반으로 하는 덜 안전하고 덜 강력한 해결 방법을 사용하는 대신 어떤 종류의 도메인 간 요청이 승인되는지 지정할 수 있도록 <a href="https://caniuse.com/#feat=cors">대부분의 브라우저</a>에서 구현되는 <a href="https://www.w3.org/TR/cors/">W3C 사양</a>입니다.</p>
<h1 id="credentialed-requests">Credentialed Requests</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-credentialed-requests">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>자격 증명 요청과 함께 CORS를 사용하려면 <code>allowedCredentials</code>를 활성화해야 합니다. 이 옵션은 구성된 도메인에 대해 높은 수준의 신뢰를 설정하고 쿠키 및 CSRF 토큰과 같은 민감한 사용자별 정보를 노출하여 웹 애플리케이션의 공격 표면을 증가시킵니다.</p>
<p>자격 증명을 활성화하면 구성된 <code>"*"</code> CORS 와일드카드가 처리되는 방식에도 영향을 줍니다.</p>
<ul>
<li>
<p><code>allowOrigins</code>에서는 와일드카드가 승인되지 않지만, 대신에 <code>allowOriginPatterns</code> 속성을 사용하여 동적 원본 집합과 일치시킬 수 있습니다.</p>
</li>
<li>
<p><code>allowedHeaders</code> 또는 <code>allowedMethods</code>에 설정된 경우 <code>Access-Control-Allow-Headers</code> 및 <code>Access-Control-Allow-Methods</code> 응답 헤더는 CORS 실행 전 요청에 지정된 관련 헤더 및 메서드를 복사하여 처리됩니다.</p>
</li>
<li>
<p><code>exposedHeaders</code>에 설정된 경우 <code>Access-Control-Expose-Headers</code> 응답 헤더는 구성된 헤더 목록이나 와일드카드 문자로 설정됩니다. <code>Access-Control-Allow-Credentials</code>가 <code>true</code>로 설정된 경우 CORS 사양에서는 와일드카드 문자를 허용하지 않지만 대부분의 브라우저는 이를 지원하며 CORS 처리 중에 응답 헤더를 모두 사용할 수 있는 것은 아니므로 결과적으로 와일드카드 문자가 헤더가 됩니다. <code>allowCredentials</code> 속성 값에 관계없이 지정된 경우 사용되는 값입니다.</p>
</li>
</ul>
<blockquote>
<p><strong>[Warning]</strong><br>
이러한 와일드카드 구성은 편리할 수 있지만 가능하면 더 높은 수준의 보안을 제공하기 위해 유한한 값 집합을 구성하는 것이 좋습니다.</p>
</blockquote>
<h1 id="processing">Processing</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-processing">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>CORS 사양은 실행 전 요청, 단순 요청, 실제 요청을 구분합니다. CORS의 작동 방식을 알아보려면 <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS">이 기사</a> 등을 읽어보거나 사양에서 자세한 내용을 확인하세요.</p>
<p>Spring MVC <code>HandlerMapping</code> 구현은 CORS에 대한 기본 지원을 제공합니다. 요청을 핸들러에 성공적으로 매핑한 후 <code>HandlerMapping</code> 구현은 지정된 요청 및 핸들러에 대한 CORS 구성을 확인하고 추가 조치를 취합니다. 실행 전 요청은 직접 처리되는 반면 단순하고 실제 CORS 요청은 가로채서 검증되고 필수 CORS 응답 헤더가 설정됩니다.</p>
<p>원본 간 요청을 활성화하려면(즉, <code>Origin</code> 헤더가 존재하고 요청 호스트와 다름) 명시적으로 선언된 CORS 구성이 있어야 합니다. 일치하는 CORS 구성이 없으면 실행 전 요청이 거부됩니다. 단순 CORS 요청과 실제 CORS 요청의 응답에는 CORS 헤더가 추가되지 않으므로 브라우저는 이를 거부합니다.</p>
<p>각 <code>HandlerMapping</code>은 URL 패턴 기반 <code>CorsConfiguration</code> 매핑을 사용하여 개별적으로 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/handler/AbstractHandlerMapping.html#setCorsConfigurations-java.util.Map-">구성할 수 있습니다</a>. 대부분의 경우 애플리케이션은 MVC Java 구성 또는 XML 네임스페이스를 사용하여 이러한 매핑을 선언하며, 이로 인해 단일 전역 맵이 모든 <code>HandlerMapping</code> 인스턴스에 전달됩니다.</p>
<p><code>HandlerMapping</code> 수준의 전역 CORS 구성을 보다 세분화된 handler 수준 CORS 구성과 결합할 수 있습니다. 예를 들어 주석이 달린 컨트롤러는 클래스 또는 메서드 수준 <code>@CrossOrigin</code> 주석을 사용할 수 있습니다(다른 핸들러는 <code>CorsConfigurationSource</code>를 구현할 수 있음).</p>
<p>글로벌 구성과 로컬 구성을 결합하는 규칙은 일반적으로 추가적입니다. 예를 들어 모든 글로벌 원본과 모든 로컬 원본이 있습니다. 단일 값만 허용되는 속성의 경우. <code>allowCredentials</code> 및 <code>maxAge</code>, 로컬이 전역 값을 재정의합니다. 자세한 내용은 <code>CorsConfiguration#combine(CorsConfiguration)</code>을 참조하세요.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
소스에서 자세히 알아보거나 고급 사용자 정의를 수행하려면 뒤에 있는 코드를 확인하세요.</p>
<ul>
<li><code>CorsConfiguration</code></li>
<li><code>CorsProcessor</code>, <code>DefaultCorsProcessor</code></li>
<li><code>AbstractHandlerMapping</code></li>
</ul>
</blockquote>
<h1 id="crossorigin"><code>@CrossOrigin</code></h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-controller">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/bind/annotation/CrossOrigin.html"><code>@CrossOrigin</code></a> annotation은 다음 예제와 같이 주석이 달린 컨트롤러 메서드에 대한 교차 출처 요청을 활성화합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/account"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccountController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@CrossOrigin</span>
	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@DeleteMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">remove</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>기본적으로 <code>@CrossOrigin</code>은 다음을 허용합니다.</p>
<ul>
<li>
<p>모든 origins.</p>
</li>
<li>
<p>모든 헤더.</p>
</li>
<li>
<p>컨트롤러 메서드가 매핑되는 모든 HTTP 메서드입니다.</p>
</li>
</ul>
<p><code>allowCredentials</code>는 기본적으로 활성화되어 있지 않습니다. 이는 민감한 사용자별 정보(예: 쿠키 및 CSRF 토큰)를 노출하는 신뢰 수준을 설정하고 적절한 경우에만 사용해야 하기 때문입니다. 활성화되면 <code>allowOrigins</code>를 하나 이상의 특정 도메인(특수 값 <code>"*"</code>은 아님)으로 설정해야 하거나, 대신에 <code>allowOriginPatterns</code> 속성을 사용하여 동적 원본 집합과 일치시킬 수 있습니다.</p>
<p><code>maxAge</code>는 30분으로 설정됩니다.</p>
<p><code>@CrossOrigin</code>은 클래스 수준에서도 지원되며 다음 예제와 같이 모든 메서드에서 상속됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@CrossOrigin</span><span class="token punctuation">(</span>origins <span class="token operator">=</span> <span class="token string">"https://domain2.com"</span><span class="token punctuation">,</span> maxAge <span class="token operator">=</span> <span class="token number">3600</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/account"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccountController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@DeleteMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">remove</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 클래스 수준과 메서드 수준 모두에서 <code>@CrossOrigin</code>을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@CrossOrigin</span><span class="token punctuation">(</span>maxAge <span class="token operator">=</span> <span class="token number">3600</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/account"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AccountController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@CrossOrigin</span><span class="token punctuation">(</span><span class="token string">"https://domain2.com"</span><span class="token punctuation">)</span>
	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">retrieve</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@DeleteMapping</span><span class="token punctuation">(</span><span class="token string">"/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">remove</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="global-configuration">Global Configuration</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-global">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>세분화된 컨트롤러 메서드 수준 구성 외에도 일부 전역 CORS 구성도 정의하고 싶을 수도 있습니다. 모든 <code>HandlerMapping</code>에서 URL 기반 <code>CorsConfiguration</code> 매핑을 개별적으로 설정할 수 있습니다. 그러나 대부분의 애플리케이션은 이를 수행하기 위해 MVC Java 구성 또는 MVC XML 네임스페이스를 사용합니다.</p>
<p>기본적으로 전역 구성은 다음을 활성화합니다.</p>
<ul>
<li>
<p>모든 기원.</p>
</li>
<li>
<p>모든 헤더.</p>
</li>
<li>
<p><code>GET</code>, <code>HEAD</code> 및 <code>POST</code> 메소드.</p>
</li>
</ul>
<p><code>allowCredentials</code>는 기본적으로 활성화되어 있지 않습니다. 이는 민감한 사용자별 정보(예: 쿠키 및 CSRF 토큰)를 노출하는 신뢰 수준을 설정하고 적절한 경우에만 사용해야 하기 때문입니다. 활성화되면 AllowOrigins를 하나 이상의 특정 도메인(특수 값 <code>"*"</code>은 아님)으로 설정해야 하거나, 대신에 <code>allowOriginPatterns</code> 속성을 사용하여 동적 원본 집합과 일치시킬 수 있습니다.</p>
<p><code>maxAge</code>는 30분으로 설정됩니다.</p>
<h2 id="java-configuration">Java Configuration</h2>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-global">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>MVC Java 구성에서 CORS를 활성화하려면 다음 예제와 같이 <code>CorsRegistry</code> 콜백을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addCorsMappings</span><span class="token punctuation">(</span><span class="token class-name">CorsRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		registry<span class="token punctuation">.</span><span class="token function">addMapping</span><span class="token punctuation">(</span><span class="token string">"/api/**"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">allowedOrigins</span><span class="token punctuation">(</span><span class="token string">"https://domain2.com"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">allowedMethods</span><span class="token punctuation">(</span><span class="token string">"PUT"</span><span class="token punctuation">,</span> <span class="token string">"DELETE"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">allowedHeaders</span><span class="token punctuation">(</span><span class="token string">"header1"</span><span class="token punctuation">,</span> <span class="token string">"header2"</span><span class="token punctuation">,</span> <span class="token string">"header3"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">exposedHeaders</span><span class="token punctuation">(</span><span class="token string">"header1"</span><span class="token punctuation">,</span> <span class="token string">"header2"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">allowCredentials</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">maxAge</span><span class="token punctuation">(</span><span class="token number">3600</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Add more mappings...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="xml-configuration">XML Configuration</h2>
<p>XML 네임스페이스에서 CORS를 활성화하려면 다음 예제와 같이 <code>&lt;mvc:cors&gt;</code> 요소를 사용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>cors</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>mapping</span> <span class="token attr-name">path</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/api/**<span class="token punctuation">"</span></span>
		<span class="token attr-name">allowed-origins</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://domain1.com, https://domain2.com<span class="token punctuation">"</span></span>
		<span class="token attr-name">allowed-methods</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>GET, PUT<span class="token punctuation">"</span></span>
		<span class="token attr-name">allowed-headers</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>header1, header2, header3<span class="token punctuation">"</span></span>
		<span class="token attr-name">exposed-headers</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>header1, header2<span class="token punctuation">"</span></span> <span class="token attr-name">allow-credentials</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span>
		<span class="token attr-name">max-age</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>123<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>mapping</span> <span class="token attr-name">path</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/resources/**<span class="token punctuation">"</span></span>
		<span class="token attr-name">allowed-origins</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>https://domain1.com<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>cors</span><span class="token punctuation">&gt;</span></span></code></pre>
<h1 id="cors-filter">CORS Filter</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux-cors.html#webflux-cors-webfilter">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>내장된 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/filter/CorsFilter.html"><code>CorsFilter</code></a>를 통해 CORS 지원을 적용할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Security와 함께 <code>CorsFilter</code>를 사용하려는 경우 Spring Security에는 CORS에 대한 <a href="https://docs.spring.io/spring-security/reference/servlet/integrations/cors.html">지원이 내장되어 있다</a>는 점을 명심하세요.</p>
</blockquote>
<p>필터를 구성하려면 다음 예제와 같이 <code>CorsConfigurationSource</code>를 해당 생성자에 전달합니다.</p>
<pre><code class="language-java"><span class="token class-name">CorsConfiguration</span> config <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">CorsConfiguration</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Possibly...</span>
<span class="token comment">// config.applyPermitDefaultValues()</span>

config<span class="token punctuation">.</span><span class="token function">setAllowCredentials</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
config<span class="token punctuation">.</span><span class="token function">addAllowedOrigin</span><span class="token punctuation">(</span><span class="token string">"https://domain1.com"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
config<span class="token punctuation">.</span><span class="token function">addAllowedHeader</span><span class="token punctuation">(</span><span class="token string">"*"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
config<span class="token punctuation">.</span><span class="token function">addAllowedMethod</span><span class="token punctuation">(</span><span class="token string">"*"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">UrlBasedCorsConfigurationSource</span> source <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UrlBasedCorsConfigurationSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
source<span class="token punctuation">.</span><span class="token function">registerCorsConfiguration</span><span class="token punctuation">(</span><span class="token string">"/**"</span><span class="token punctuation">,</span> config<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">CorsFilter</span> filter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">CorsFilter</span><span class="token punctuation">(</span>source<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>