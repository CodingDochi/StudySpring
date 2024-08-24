<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/uri-building.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>이 섹션에서는 URI 작업을 위해 Spring Framework에서 사용할 수 있는 다양한 옵션을 설명합니다.</p>
<h1 id="uricomponents">UriComponents</h1>
<p>스프링 MVC와 스프링 WebFlux <code>UriComponentsBuilder</code>는 다음 예제와 같이 변수가 있는 URI 템플릿에서 URI를 빌드하는 데 도움이 됩니다.</p>
<pre><code class="language-java"><span class="token class-name">UriComponents</span> uriComponents <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span>
		<span class="token punctuation">.</span><span class="token function">fromUriString</span><span class="token punctuation">(</span><span class="token string">"https://example.com/hotels/{hotel}"</span><span class="token punctuation">)</span>  <span class="token comment">// (1)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span> <span class="token comment">// (2)</span>
		<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span>  <span class="token comment">// (3)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>   <span class="token comment">// (4)</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> uriComponents<span class="token punctuation">.</span><span class="token function">expand</span><span class="token punctuation">(</span><span class="token string">"Westin"</span><span class="token punctuation">,</span> <span class="token string">"123"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">// (5)</span></code></pre>
<p>(1) URI 템플릿을 사용하는 정적 팩터리 메서드입니다.<br>
(2) URI 구성 요소를 추가하거나 교체합니다.<br>
(3) URI 템플릿과 URI 변수를 인코딩하도록 요청합니다.<br>
(4) <code>UriComponents</code>를 빌드합니다.<br>
(5) 변수를 확장하고 URI를 얻습니다.</p>
<p>다음 예제와 같이 이전 예제를 하나의 체인으로 통합하고 <code>buildAndExpand</code>를 사용하여 단축할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span>
		<span class="token punctuation">.</span><span class="token function">fromUriString</span><span class="token punctuation">(</span><span class="token string">"https://example.com/hotels/{hotel}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">buildAndExpand</span><span class="token punctuation">(</span><span class="token string">"Westin"</span><span class="token punctuation">,</span> <span class="token string">"123"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 URI(인코딩을 의미함)로 직접 이동하여 더 단축할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span>
		<span class="token punctuation">.</span><span class="token function">fromUriString</span><span class="token punctuation">(</span><span class="token string">"https://example.com/hotels/{hotel}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"Westin"</span><span class="token punctuation">,</span> <span class="token string">"123"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 전체 URI 템플릿을 사용하면 더 단축할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span>
		<span class="token punctuation">.</span><span class="token function">fromUriString</span><span class="token punctuation">(</span><span class="token string">"https://example.com/hotels/{hotel}?q={q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"Westin"</span><span class="token punctuation">,</span> <span class="token string">"123"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h1 id="uribuilder">UriBuilder</h1>
<p>스프링 MVC와 스프링 WebFlux</p>
<p><code>UriComponentsBuilder</code>는 <code>UriBuilder</code>를 구현합니다. <code>UriBuilderFactory</code>를 사용하여 <code>UriBuilder</code>를 생성할 수 있습니다. <code>UriBuilderFactory</code>와 <code>UriBuilder</code>는 함께 기본 URL, 인코딩 기본 설정 및 기타 세부 정보와 같은 공유 구성을 기반으로 URI 템플릿에서 URI를 구축하는 플러그형 메커니즘을 제공합니다.</p>
<p><code>UriBuilderFactory</code>를 사용하여 <code>RestTemplate</code> 및 <code>WebClient</code>를 구성하여 URI 준비를 사용자 지정할 수 있습니다. <code>DefaultUriBuilderFactory</code>는 <code>UriComponentsBuilder</code>를 내부적으로 사용하고 공유 구성 옵션을 노출하는 <code>UriBuilderFactory</code>의 기본 구현입니다.</p>
<p>다음 예에서는 <code>RestTemplate</code>을 구성하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token comment">// import org.springframework.web.util.DefaultUriBuilderFactory.EncodingMode;</span>

<span class="token class-name">String</span> baseUrl <span class="token operator">=</span> <span class="token string">"https://example.org"</span><span class="token punctuation">;</span>
<span class="token class-name">DefaultUriBuilderFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultUriBuilderFactory</span><span class="token punctuation">(</span>baseUrl<span class="token punctuation">)</span><span class="token punctuation">;</span>
factory<span class="token punctuation">.</span><span class="token function">setEncodingMode</span><span class="token punctuation">(</span><span class="token class-name">EncodingMode</span><span class="token punctuation">.</span>TEMPLATE_AND_VALUES<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">RestTemplate</span> restTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">RestTemplate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
restTemplate<span class="token punctuation">.</span><span class="token function">setUriTemplateHandler</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예에서는 <code>WebClient</code>를 구성합니다.</p>
<pre><code class="language-java"><span class="token comment">// import org.springframework.web.util.DefaultUriBuilderFactory.EncodingMode;</span>

<span class="token class-name">String</span> baseUrl <span class="token operator">=</span> <span class="token string">"https://example.org"</span><span class="token punctuation">;</span>
<span class="token class-name">DefaultUriBuilderFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultUriBuilderFactory</span><span class="token punctuation">(</span>baseUrl<span class="token punctuation">)</span><span class="token punctuation">;</span>
factory<span class="token punctuation">.</span><span class="token function">setEncodingMode</span><span class="token punctuation">(</span><span class="token class-name">EncodingMode</span><span class="token punctuation">.</span>TEMPLATE_AND_VALUES<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">WebClient</span> client <span class="token operator">=</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uriBuilderFactory</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>또한 <code>DefaultUriBuilderFactory</code>를 직접 사용할 수도 있습니다. 이는 <code>UriComponentsBuilder</code>를 사용하는 것과 유사하지만 정적 팩터리 메서드 대신 다음 예제와 같이 구성 및 기본 설정을 보유하는 실제 인스턴스입니다.</p>
<pre><code class="language-java"><span class="token class-name">String</span> baseUrl <span class="token operator">=</span> <span class="token string">"https://example.com"</span><span class="token punctuation">;</span>
<span class="token class-name">DefaultUriBuilderFactory</span> uriBuilderFactory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultUriBuilderFactory</span><span class="token punctuation">(</span>baseUrl<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> uriBuilderFactory<span class="token punctuation">.</span><span class="token function">uriString</span><span class="token punctuation">(</span><span class="token string">"/hotels/{hotel}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"Westin"</span><span class="token punctuation">,</span> <span class="token string">"123"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h1 id="uri-encoding">URI Encoding</h1>
<p>스프링 MVC와 스프링 WebFlux</p>
<p><code>UriComponentsBuilder</code>는 두 가지 수준에서 인코딩 옵션을 노출합니다.</p>
<ul>
<li>
<p><code>UriComponentsBuilder#encode()</code>: 먼저 URI 템플릿을 사전 인코딩한 다음 확장 시 URI 변수를 엄격하게 인코딩합니다.</p>
</li>
<li>
<p><code>UriComponents#encode()</code>: URI 변수가 확장된 후 URI 구성 요소를 인코딩합니다.</p>
</li>
</ul>
<p>두 옵션 모두 ASCII가 아닌 문자와 잘못된 문자를 이스케이프된 옥텟으로 바꿉니다. 그러나 첫 번째 옵션도 URI 변수에 나타나는 예약된 의미로 문자를 바꿉니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
경로에서는 유효하지만 의미가 보존되어 있는 ";"을 고려하세요. 첫 번째 옵션은 ";"을 대체합니다. URI 변수에는 "%3B"가 있지만 URI 템플릿에는 없습니다. 대조적으로, 두 번째 옵션은 경로에서 유효한 문자이기 때문에 ";"를 대체하지 않습니다.</p>
</blockquote>
<p>대부분의 경우 첫 번째 옵션은 URI 변수를 완전히 인코딩할 불투명 데이터로 처리하므로 예상한 결과를 제공할 가능성이 높으며, 두 번째 옵션은 URI 변수에 의도적으로 예약된 문자가 포함된 경우 유용합니다. 두 번째 옵션은 URI 변수처럼 보이는 모든 항목도 인코딩하므로 URI 변수를 전혀 확장하지 않을 때도 유용합니다.</p>
<p>다음 예에서는 첫 번째 옵션을 사용합니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromPath</span><span class="token punctuation">(</span><span class="token string">"/hotel list/{city}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">buildAndExpand</span><span class="token punctuation">(</span><span class="token string">"New York"</span><span class="token punctuation">,</span> <span class="token string">"foo+bar"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Result is "/hotel%20list/New%20York?q=foo%2Bbar"</span></code></pre>
<p>다음 예제와 같이 URI(인코딩을 의미함)로 직접 이동하여 앞의 예제를 단축할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromPath</span><span class="token punctuation">(</span><span class="token string">"/hotel list/{city}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">queryParam</span><span class="token punctuation">(</span><span class="token string">"q"</span><span class="token punctuation">,</span> <span class="token string">"{q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"New York"</span><span class="token punctuation">,</span> <span class="token string">"foo+bar"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 전체 URI 템플릿을 사용하면 더 단축할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">UriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromUriString</span><span class="token punctuation">(</span><span class="token string">"/hotel list/{city}?q={q}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"New York"</span><span class="token punctuation">,</span> <span class="token string">"foo+bar"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>WebClient</code>와 <code>RestTemplate</code>은 <code>UriBuilderFactory</code> 전략을 통해 내부적으로 URI 템플릿을 확장하고 인코딩합니다. 다음 예와 같이 둘 다 사용자 지정 전략으로 구성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">String</span> baseUrl <span class="token operator">=</span> <span class="token string">"https://example.com"</span><span class="token punctuation">;</span>
<span class="token class-name">DefaultUriBuilderFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultUriBuilderFactory</span><span class="token punctuation">(</span>baseUrl<span class="token punctuation">)</span>
factory<span class="token punctuation">.</span><span class="token function">setEncodingMode</span><span class="token punctuation">(</span><span class="token class-name">EncodingMode</span><span class="token punctuation">.</span>TEMPLATE_AND_VALUES<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Customize the RestTemplate..</span>
<span class="token class-name">RestTemplate</span> restTemplate <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">RestTemplate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
restTemplate<span class="token punctuation">.</span><span class="token function">setUriTemplateHandler</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// Customize the WebClient..</span>
<span class="token class-name">WebClient</span> client <span class="token operator">=</span> <span class="token class-name">WebClient</span><span class="token punctuation">.</span><span class="token function">builder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">uriBuilderFactory</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>DefaultUriBuilderFactory</code> 구현은 내부적으로 <code>UriComponentsBuilder</code>를 사용하여 URI 템플릿을 확장하고 인코딩합니다. 팩토리로서 아래 인코딩 모드 중 하나를 기반으로 인코딩 접근 방식을 구성할 수 있는 단일 위치를 제공합니다.</p>
<ul>
<li>
<p><code>TEMPLATE_AND_VALUES</code>: 이전 목록의 첫 번째 옵션에 해당하는  <code>UriComponentsBuilder#encode()</code>를 사용하여 URI 템플릿을 사전 인코딩하고 확장 시 URI 변수를 엄격하게 인코딩합니다.</p>
</li>
<li>
<p><code>VALUES_ONLY</code>: URI 템플릿을 인코딩하지 않고 대신 템플릿으로 확장하기 전에 <code>UriUtils#encodeUriVariables</code>를 통해 URI 변수에 엄격한 인코딩을 적용합니다.</p>
</li>
<li>
<p><code>URI_COMPONENT</code>: 앞선 목록의 두 번째 옵션에 해당하는 <code>UriComponents#encode()</code>를 사용하여 URI 변수가 확장된 후 URI 구성 요소 값을 인코딩합니다.</p>
</li>
<li>
<p><code>NONE</code>: 인코딩이 적용되지 않습니다.</p>
</li>
<li>
<p><code>RestTemplate</code>은 역사적인 이유와 이전 버전과의 호환성을 위해 <code>EncodingMode.URI_COMPONENT</code>로 설정됩니다. <code>WebClient</code>는 5.0.x의 <code>EncodingMode.URI_COMPONENT</code>에서 5.1의 <code>EncodingMode.TEMPLATE_AND_VALUES</code>로 변경된 <code>DefaultUriBuilderFactory</code>의 기본값을 사용합니다.</p>
</li>
</ul>
<h1 id="relative-servelet-requests">Relative Servelet Requests</h1>
<p>다음 예제와 같이 <code>ServletUriComponentsBuilder</code>를 사용하여 현재 요청과 관련된 URI를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">HttpServletRequest</span> request <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

<span class="token comment">// Re-uses scheme, host, port, path, and query string...</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">ServletUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromRequest</span><span class="token punctuation">(</span>request<span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">replaceQueryParam</span><span class="token punctuation">(</span><span class="token string">"accountId"</span><span class="token punctuation">,</span> <span class="token string">"{id}"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token string">"123"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 컨텍스트 경로를 기준으로 URI를 만들 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">HttpServletRequest</span> request <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

<span class="token comment">// Re-uses scheme, host, port, and context path...</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">ServletUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromContextPath</span><span class="token punctuation">(</span>request<span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/accounts"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다음 예제와 같이 서블릿에 상대적인 URI(예: <code>/main/*</code>)를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">HttpServletRequest</span> request <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>

<span class="token comment">// Re-uses scheme, host, port, context path, and Servlet mapping prefix...</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> <span class="token class-name">ServletUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromServletMapping</span><span class="token punctuation">(</span>request<span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/accounts"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
5.1부터 <code>ServletUriComponentsBuilder</code>는 클라이언트 시작 주소를 지정하는 <code>Forwarded</code> 및 <code>X-Forwarded-*</code> 헤더의 정보를 무시합니다. 이러한 헤더를 추출하여 사용하거나 삭제하려면 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-forwarded-headers"><code>ForwardedHeaderFilter</code></a>를 사용하는 것이 좋습니다.</p>
</blockquote>
<h1 id="links-to-controllers">Links to Controllers</h1>
<p>Spring MVC는 컨트롤러 메소드에 대한 링크를 준비하는 메커니즘을 제공합니다. 예를 들어 다음 MVC 컨트롤러는 링크 생성을 허용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/hotels/{hotel}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BookingController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/bookings/{booking}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">ModelAndView</span> <span class="token function">getBooking</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">Long</span> booking<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 이름으로 메서드를 참조하여 링크를 준비할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">UriComponents</span> uriComponents <span class="token operator">=</span> <span class="token class-name">MvcUriComponentsBuilder</span>
	<span class="token punctuation">.</span><span class="token function">fromMethodName</span><span class="token punctuation">(</span><span class="token class-name">BookingController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token string">"getBooking"</span><span class="token punctuation">,</span> <span class="token number">21</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">buildAndExpand</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> uriComponents<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>앞의 예에서는 경로 변수로 사용되고 URL에 삽입될 실제 메서드 인수 값(이 경우 긴 값: <code>21</code>)을 제공합니다. 또한 유형 수준 요청 매핑에서 상속된 <code>hotel</code> 변수와 같은 나머지 URI 변수를 채우기 위해 값 <code>42</code>를 제공합니다. 메소드에 더 많은 인수가 있는 경우 URL에 필요하지 않은 인수에 대해 null을 제공할 수 있습니다. 일반적으로 <code>@PathVariable</code> 및 <code>@RequestParam</code> 인수만 URL 구성에 관련됩니다.</p>
<p><code>MvcUriComponentsBuilder</code>를 사용하는 추가 방법이 있습니다. 예를 들어, 다음 예제에서 볼 수 있듯이 컨트롤러 메서드를 이름으로 참조하지 않도록 프록시를 통한 모의 테스트(mock testing)와 유사한 기술을 사용할 수 있습니다(예에서는 <code>MvcUriComponentsBuilder.on</code>의 static import를 가정합니다).</p>
<pre><code class="language-java"><span class="token class-name">UriComponents</span> uriComponents <span class="token operator">=</span> <span class="token class-name">MvcUriComponentsBuilder</span>
	<span class="token punctuation">.</span><span class="token function">fromMethodCall</span><span class="token punctuation">(</span><span class="token function">on</span><span class="token punctuation">(</span><span class="token class-name">BookingController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getBooking</span><span class="token punctuation">(</span><span class="token number">21</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">buildAndExpand</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> uriComponents<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
컨트롤러 메서드 서명은 <code>fromMethodCall</code>을 통한 링크 생성에 사용할 수 있다고 가정할 때 디자인이 제한됩니다. 적절한 매개변수 서명이 필요한 것 외에도 반환 유형(즉, 링크 빌더 호출을 위한 런타임 프록시 생성)에는 기술적인 제한이 있으므로 반환 유형은 <code>final</code>이 아니어야 합니다. 특히, 뷰 이름에 대한 일반적인 <code>String</code> 반환 유형은 여기서 작동하지 않습니다. 대신 <code>ModelAndView</code> 또는 일반 <code>Object</code>(<code>String</code> 반환 값 포함)를 사용해야 합니다.</p>
</blockquote>
<p>이전 예제에서는 <code>MvcUriComponentsBuilder</code>의 정적 메서드를 사용합니다. 내부적으로는 <code>ServletUriComponentsBuilder</code>를 사용하여 현재 요청의 구성표, 호스트, 포트, 컨텍스트 경로 및 서블릿 경로에서 기본 URL을 준비합니다. 이는 대부분의 경우에 잘 작동합니다. 그러나 때로는 충분하지 않을 수도 있습니다. 예를 들어 요청 컨텍스트 외부에 있거나(예: 링크를 준비하는 일괄 프로세스) 경로 접두사(예: 요청 경로에서 제거되어 다시 수정해야 하는 로케일 접두사)를 삽입해야 할 수 있습니다. 링크에 삽입했습니다).</p>
<p>이러한 경우 기본 URL을 사용하기 위해 <code>UriComponentsBuilder</code>를 허용하는 정적 <code>fromXxx</code> 오버로드된 메서드를 사용할 수 있습니다. 또는 기본 URL을 사용하여 <code>MvcUriComponentsBuilder</code>의 인스턴스를 만든 다음 인스턴스 기반 <code>withXxx</code> 메서드를 사용할 수 있습니다. 예를 들어 다음 목록에서는 <code>withMethodCall</code>을 사용합니다.</p>
<pre><code class="language-java"><span class="token class-name">UriComponentsBuilder</span> base <span class="token operator">=</span> <span class="token class-name">ServletUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">fromCurrentContextPath</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">path</span><span class="token punctuation">(</span><span class="token string">"/en"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">MvcUriComponentsBuilder</span> builder <span class="token operator">=</span> <span class="token class-name">MvcUriComponentsBuilder</span><span class="token punctuation">.</span><span class="token function">relativeTo</span><span class="token punctuation">(</span>base<span class="token punctuation">)</span><span class="token punctuation">;</span>
builder<span class="token punctuation">.</span><span class="token function">withMethodCall</span><span class="token punctuation">(</span><span class="token function">on</span><span class="token punctuation">(</span><span class="token class-name">BookingController</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getBooking</span><span class="token punctuation">(</span><span class="token number">21</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">buildAndExpand</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">URI</span> uri <span class="token operator">=</span> uriComponents<span class="token punctuation">.</span><span class="token function">encode</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toUri</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>5.1부터 <code>MvcUriComponentsBuilder</code>는 클라이언트 시작 주소를 수신하는 <code>Forwarded</code> 및 <code>X-Forwarded-*</code> 헤더의 정보를 무시합니다. 이러한 헤더를 추출하여 사용하거나 삭제하려면 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/filters.html#filters-forwarded-headers">ForwardedHeaderFilter</a>를 사용하는 것이 좋습니다.</p>
<h1 id="links-in-views">Links in Views</h1>
<p>Thymeleaf, FreeMarker 또는 JSP와 같은 용도로 각 요청 매핑에 대해 해당하거나 고유하게 이름을 참조하여 추출된 컨트롤러에 대한 링크를 구축할 수 있습니다. 다음 예를 고려하시기 바랍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/people/{id}/addresses"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PersonAddressController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/{country}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">HttpEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">PersonAddress</span><span class="token punctuation">&gt;</span></span> <span class="token function">getAddress</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> country<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 컨트롤러가 주어지면 다음과 같이 JSP에서 링크를 준비할 수 있습니다.</p>
<pre><code class="language-jsp">&lt;%@ taglib uri="http://www.springframework.org/tags" prefix="s" %&gt;
...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>a</span> <span class="token attr-name">href</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${s:mvcUrl('PAC#getAddress').arg(0,'US').buildAndExpand('123')}<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Get Address<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>a</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예제는 Spring 태그 라이브러리(즉, META-INF/spring.tld)에 선언된 <code>mvcUrl</code> 함수에 의존하지만 자신만의 함수를 정의하거나 다른 템플릿 기술을 위해 유사한 함수를 준비하는 것이 쉽습니다.</p>
<p>작동 방식은 다음과 같습니다. 시작 시 모든 <code>@RequestMapping</code>에는 <code>HandlerMethodMappingNamingStrategy</code>를 통해 기본 이름이 할당됩니다. 기본 구현에서는 클래스의 대문자와 메서드 이름을 사용합니다(예를 들어 <code>ThingController</code>의 <code>getThing</code> 메서드는 "TC#getThing"이 됩니다). 이름 충돌이 있는 경우 <code>@RequestMapping(name="..")</code>을 사용하여 명시적인 이름을 할당하거나 자체 <code>HandlerMethodMappingNamingStrategy</code>를 구현할 수 있습니다.</p>