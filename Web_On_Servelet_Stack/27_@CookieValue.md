<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/cookievalue.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><code>@CookieValue</code> annotation을 사용하여 HTTP 쿠키 값을 컨트롤러의 메서드 인수에 바인딩할 수 있습니다. 다음 쿠키를 사용한 요청을 고려해보세요.</p>
<pre><code class="language-null">JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84</code></pre>
<p>다음 예에서는 쿠키 값을 가져오는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/demo"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token annotation punctuation">@CookieValue</span><span class="token punctuation">(</span><span class="token string">"JSESSIONID"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> cookie<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) JSESSIONID 쿠키의 값을 가져옵니다. </p>
<p>대상 메소드 매개변수 type이 <code>String</code>이 아닌 경우 유형 변환이 자동으로 적용됩니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/typeconversion.html">Type Conversion</a>을 참조하세요.</p>