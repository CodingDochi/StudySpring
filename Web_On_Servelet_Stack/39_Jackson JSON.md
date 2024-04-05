<p>Spring은 Jackson JSON 라이브러리를 지원합니다.</p>
<h1 id="json-views">JSON Views</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/jackson.html#webflux-ann-jsonview">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 <code>Object</code>에 있는 모든 필드의 하위 집합만 렌더링할 수 있는 <a href="https://www.baeldung.com/jackson-json-view-annotation">Jackson의 직렬화 뷰</a>에 대한 기본 지원을 제공합니다. <code>@ResponseBody</code> 또는 <code>ResponseEntity</code> 컨트롤러 메서드와 함께 사용하려면 다음 예제와 같이 Jackson의 <code>@JsonView</code> annotation을 사용하여 직렬화 뷰 클래스를 활성화할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/user"</span><span class="token punctuation">)</span>
	<span class="token annotation punctuation">@JsonView</span><span class="token punctuation">(</span><span class="token class-name">User</span><span class="token punctuation">.</span><span class="token class-name">WithoutPasswordView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">User</span> <span class="token function">getUser</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token string">"eric"</span><span class="token punctuation">,</span> <span class="token string">"7!jd#h23"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">User</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">WithoutPasswordView</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
	<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">WithPasswordView</span> <span class="token keyword">extends</span> <span class="token class-name">WithoutPasswordView</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> username<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> password<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token class-name">String</span> username<span class="token punctuation">,</span> <span class="token class-name">String</span> password<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>username <span class="token operator">=</span> username<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>password <span class="token operator">=</span> password<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@JsonView</span><span class="token punctuation">(</span><span class="token class-name">WithoutPasswordView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>username<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@JsonView</span><span class="token punctuation">(</span><span class="token class-name">WithPasswordView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getPassword</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>password<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@JsonView</code>는 다양한 뷰 클래스를 허용하지만 컨트롤러 메서드당 하나만 지정할 수 있습니다. 여러 보기를 활성화해야 하는 경우 복합 인터페이스를 사용할 수 있습니다.</p>
</blockquote>
<p>위 작업을 프로그래밍 방식으로 수행하려면 <code>@JsonView</code> annotation을 선언하는 대신 반환 값을 <code>MappingJacksonValue</code>로 래핑하고 이를 사용하여 직렬화 뷰를 제공합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RestController</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/user"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">MappingJacksonValue</span> <span class="token function">getUser</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">User</span> user <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token string">"eric"</span><span class="token punctuation">,</span> <span class="token string">"7!jd#h23"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">MappingJacksonValue</span> value <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MappingJacksonValue</span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">;</span>
		value<span class="token punctuation">.</span><span class="token function">setSerializationView</span><span class="token punctuation">(</span><span class="token class-name">User</span><span class="token punctuation">.</span><span class="token class-name">WithoutPasswordView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> value<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>뷰 확인에 의존하는 컨트롤러의 경우 다음 예제와 같이 직렬화 뷰 클래스를 모델에 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserController</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/user"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getUser</span><span class="token punctuation">(</span><span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token string">"user"</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">User</span><span class="token punctuation">(</span><span class="token string">"eric"</span><span class="token punctuation">,</span> <span class="token string">"7!jd#h23"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token class-name">JsonView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">User</span><span class="token punctuation">.</span><span class="token class-name">WithoutPasswordView</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token string">"userView"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>