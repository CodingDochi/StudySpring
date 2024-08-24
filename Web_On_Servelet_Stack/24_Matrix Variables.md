<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/matrix-variables.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><a href="https://datatracker.ietf.org/doc/html/rfc3986#section-3.3">RFC 3986</a>에서는 경로 세그먼트의 이름-값 쌍에 대해 설명합니다. Spring MVC에서는 Tim Berners-Lee의 <a href="https://www.w3.org/DesignIssues/MatrixURIs.html">"old post"</a>을 기반으로 이를 "행렬 변수"라고 부르지만 URI 경로 매개변수라고도 할 수 있습니다.</p>
<p>매트릭스 변수는 모든 경로 세그먼트에 나타날 수 있으며 각 변수는 세미콜론으로 구분되고 여러 값은 쉼표로 구분됩니다(예: <code>/cars;color=red,green;year=2012</code>). 반복되는 변수 이름을 통해 여러 값을 지정할 수도 있습니다(예: <code>color=red;color=green;color=blue</code>).</p>
<p>URL에 행렬 변수가 포함될 것으로 예상되는 경우 컨트롤러 메서드에 대한 요청 매핑은 URI 변수를 사용하여 해당 변수 콘텐츠를 마스크하고 행렬 변수 순서 및 존재 여부와 관계없이 요청이 성공적으로 일치할 수 있는지 확인해야 합니다. 다음 예에서는 행렬 변수를 사용합니다.</p>
<pre><code class="language-java"><span class="token comment">// GET /pets/42;q=11;r=22</span>

<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/pets/{petId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> petId<span class="token punctuation">,</span> <span class="token annotation punctuation">@MatrixVariable</span> <span class="token keyword">int</span> q<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token comment">// petId == 42</span>
	<span class="token comment">// q == 11</span>
<span class="token punctuation">}</span></code></pre>
<p>모든 경로 세그먼트에 행렬 변수가 포함될 수 있다는 점을 고려하면 행렬 변수가 어떤 경로 변수에 포함될 것으로 예상되는지 명확하게 해야 하는 경우가 있습니다. 다음 예에서는 그렇게 하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token comment">// GET /owners/42;q=11/pets/21;q=22</span>

<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span>
		<span class="token annotation punctuation">@MatrixVariable</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token string">"q"</span><span class="token punctuation">,</span> pathVar<span class="token operator">=</span><span class="token string">"ownerId"</span><span class="token punctuation">)</span> <span class="token keyword">int</span> q1<span class="token punctuation">,</span>
		<span class="token annotation punctuation">@MatrixVariable</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token string">"q"</span><span class="token punctuation">,</span> pathVar<span class="token operator">=</span><span class="token string">"petId"</span><span class="token punctuation">)</span> <span class="token keyword">int</span> q2<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token comment">// q1 == 11</span>
	<span class="token comment">// q2 == 22</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서 볼 수 있듯이 행렬 변수는 선택 사항으로 정의되고 기본값이 지정될 수 있습니다.</p>
<pre><code class="language-java"><span class="token comment">// GET /pets/42</span>

<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/pets/{petId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span><span class="token annotation punctuation">@MatrixVariable</span><span class="token punctuation">(</span>required<span class="token operator">=</span><span class="token boolean">false</span><span class="token punctuation">,</span> defaultValue<span class="token operator">=</span><span class="token string">"1"</span><span class="token punctuation">)</span> <span class="token keyword">int</span> q<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token comment">// q == 1</span>
<span class="token punctuation">}</span></code></pre>
<p>모든 행렬 변수를 얻으려면 다음 예제와 같이 <code>MultiValueMap</code>을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token comment">// GET /owners/42;q=11;r=12/pets/21;q=22;s=23</span>

<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">findPet</span><span class="token punctuation">(</span>
		<span class="token annotation punctuation">@MatrixVariable</span> <span class="token class-name">MultiValueMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> matrixVars<span class="token punctuation">,</span>
		<span class="token annotation punctuation">@MatrixVariable</span><span class="token punctuation">(</span>pathVar<span class="token operator">=</span><span class="token string">"petId"</span><span class="token punctuation">)</span> <span class="token class-name">MultiValueMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> petMatrixVars<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token comment">// matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]</span>
	<span class="token comment">// petMatrixVars: ["q" : 22, "s" : 23]</span>
<span class="token punctuation">}</span></code></pre>
<p>행렬 변수를 사용하도록 설정해야 합니다. MVC Java 구성에서는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/path-matching.html">경로 matching</a>를 통해 <code>removeSemicolonContent=false</code>로 <code>UrlPathHelper</code>를 설정해야 합니다. MVC XML 네임스페이스에서 <code>&lt;mvc:annotation-driven 활성화-matrix-variables="true"/&gt;</code>를 설정할 수 있습니다.</p>