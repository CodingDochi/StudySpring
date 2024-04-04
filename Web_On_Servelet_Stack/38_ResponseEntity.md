<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/responseentity.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><code>ResponseEntity</code>는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/responsebody.html"><code>@ResponseBody</code></a>와 비슷하지만 상태와 헤더가 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/something"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">ResponseEntity</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">String</span> body <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>
	<span class="token class-name">String</span> etag <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token class-name">ResponseEntity</span><span class="token punctuation">.</span><span class="token function">ok</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">eTag</span><span class="token punctuation">(</span>etag<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span>body<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring MVC는 단일 값 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응 type</a>을 사용하여 <code>ResponseEntity</code>를 비동기적으로 생성하거나 본문에 대한 단일 및 다중 값 반응 유형을 지원합니다. 이를 통해 다음 유형의 비동기 응답이 허용됩니다.</p>
<ul>
<li>
<p><code>ResponseEntity&lt;Mono&lt;T&gt;&gt;</code> 또는 <code>ResponseEntity&lt;Flux&lt;T&gt;&gt;</code>는 응답 상태와 헤더를 즉시 알리고 본문은 나중에 비동기적으로 제공됩니다. 본문이 0..1 값으로 구성된 경우 <code>Mono</code>를 사용하고 여러 값을 생성할 수 있는 경우 <code>Flux</code>를 사용합니다.</p>
</li>
<li>
<p><code>Mono&lt;ResponseEntity&lt;T&gt;&gt;</code>는 나중에 응답 상태, 헤더, 본문 세 가지를 모두 비동기식으로 제공합니다. 이를 통해 응답 상태와 헤더가 비동기 요청 처리 결과에 따라 달라질 수 있습니다.</p>
</li>
</ul>