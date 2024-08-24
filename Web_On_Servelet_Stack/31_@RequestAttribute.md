<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/requestattrib.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@SessionAttribute</code>와 유사하게 <code>@RequestAttribute</code> annotation을 사용하여 이전에 생성된 기존 요청 속성(예: 서블릿 <code>Filter</code> 또는 <code>HandlerInterceptor</code>를 통해)에 액세스할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestAttribute</span> <span class="token class-name">Client</span> client<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@RequestAttribute</code> annotation을 사용합니다.</p>