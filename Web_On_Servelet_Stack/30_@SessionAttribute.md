<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/sessionattribute.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>전역적으로 관리되고(예를 들어 필터에 의해 컨트롤러 외부에서) 존재하거나 존재하지 않을 수 있는 기존 세션 속성에 액세스해야 하는 경우 다음과 같이 메소드 매개변수에 <code>@SessionAttribute</code> annotation을 사용할 수 있습니다. 다음 예는 다음을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@SessionAttribute</span> <span class="token class-name">User</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@SessionAttribute</code> annotation을 사용합니다.</p>
<p>세션 속성을 추가하거나 제거해야 하는 사용 사례의 경우 <code>org.springframework.web.context.request.WebRequest</code> 또는 <code>jakarta.servlet.http.HttpSession</code>을 컨트롤러 메서드에 삽입하는 것을 고려하세요.</p>
<p>컨트롤러 워크플로의 일부로 세션에 모델 속성을 임시로 저장하려면 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/sessionattributes.html"><code>@SessionAttributes</code></a>에 설명된 대로 <code>@SessionAttributes</code>를 사용하는 것이 좋습니다.</p>