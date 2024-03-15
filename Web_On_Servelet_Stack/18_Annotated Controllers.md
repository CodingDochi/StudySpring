<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC는 <code>@Controller</code> 및 <code>@RestController</code> component가 annotation을 사용하여 요청 매핑, 요청 입력, 예외 처리 등을 표현하는 annotation 기반 프로그래밍 모델을 제공합니다. annotation이 달린 컨트롤러는 유연한 메서드 서명을 가지며 기본 클래스를 확장하거나 특정 인터페이스를 구현할 필요가 없습니다. 다음 예에서는 annotation으로 정의된 컨트롤러를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">HelloController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/hello"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token string">"message"</span><span class="token punctuation">,</span> <span class="token string">"Hello World!"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token string">"index"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 메서드는 <code>Model</code>을 받아들이고 뷰 이름을 <code>String</code>로 반환하지만 다른 옵션도 많이 존재하며 이에 대해서는 이 장의 뒷부분에서 설명합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
<a href="https://spring.io/guides"><code>spring.io</code></a>의 가이드와 튜토리얼은 이 섹션에 설명된 annotation 기반 프로그래밍 모델을 사용합니다.</p>
</blockquote>