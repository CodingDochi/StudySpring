<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/sessionattributes.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@SessionAttributes</code>는 요청 간 HTTP 서블릿 세션에 모델 속성을 저장하는 데 사용됩니다. 특정 컨트롤러에서 사용하는 세션 속성을 선언하는 유형 수준 주석입니다. 여기에는 일반적으로 후속 요청에 대한 액세스를 위해 세션에 투명하게 저장되어야 하는 모델 속성의 이름 또는 모델 속성 유형이 나열됩니다.</p>
<p>다음 예에서는 <code>@SessionAttributes</code> annotation을 사용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token annotation punctuation">@SessionAttributes</span><span class="token punctuation">(</span><span class="token string">"pet"</span><span class="token punctuation">)</span>  <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EditPetForm</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@SessionAttributes</code> annotation을 사용합니다.</p>
<p>첫 번째 요청에서 이름이 <code>pet</code>인 모델 속성이 모델에 추가되면 자동으로 HTTP 서블릿 세션으로 승격되어 저장됩니다. 다음 예제와 같이 다른 컨트롤러 메서드가 <code>SessionStatus</code> 메서드 인수를 사용하여 저장소를 지울 때까지 그대로 유지됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token annotation punctuation">@SessionAttributes</span><span class="token punctuation">(</span><span class="token string">"pet"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EditPetForm</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/pets/{id}"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">Pet</span> pet<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> errors<span class="token punctuation">,</span> <span class="token class-name">SessionStatus</span> status<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>errors<span class="token punctuation">.</span>hasErrors<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token comment">// ...</span>
		<span class="token punctuation">}</span>
		status<span class="token punctuation">.</span><span class="token function">setComplete</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// (2)</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 서블릿 세션에 <code>Pet</code> 값을 저장합니다.<br>
(2) 서블릿 세션에서 <code>Pet</code> 값을 지웁니다.</p>