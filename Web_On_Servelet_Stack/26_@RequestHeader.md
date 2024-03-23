<p>[반응형 스택에서 이에 상응하는 내용 보기]</p>
<p><code>@RequestHeader</code> annotation을 사용하여 요청 헤더를 컨트롤러의 메서드 인수에 바인딩할 수 있습니다. 헤더가 포함된 다음 요청을 고려하세요.</p>
<pre><code class="language-null">Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300</code></pre>
<p>다음 예에서는 <code>Accept-Encoding</code> 및 <code>Keep-Alive</code> 헤더의 값을 가져옵니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/demo"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">handle</span><span class="token punctuation">(</span>
		<span class="token annotation punctuation">@RequestHeader</span><span class="token punctuation">(</span><span class="token string">"Accept-Encoding"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> encoding<span class="token punctuation">,</span> <span class="token comment">// (1)</span>
		<span class="token annotation punctuation">@RequestHeader</span><span class="token punctuation">(</span><span class="token string">"Keep-Alive"</span><span class="token punctuation">)</span> <span class="token keyword">long</span> keepAlive<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (2)</span>
	<span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>Accept-Encoding</code> 헤더의 값을 가져옵니다.<br>
(2) <code>Keep-Alive</code> 헤더의 값을 가져옵니다.</p>
<p>대상 메소드 매개변수 유형이 <code>String</code>이 아닌 경우 type conversion이 자동으로 적용됩니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/typeconversion.html">Type Conversion</a>을 참조하세요.</p>
<p><code>@RequestHeader</code> annotation이 <code>Map&lt;String, String&gt;</code>, <code>MultiValueMap&lt;String, String&gt;</code> 또는 <code>HttpHeaders</code> 인수에 사용되면 맵이 모든 헤더 값으로 채워집니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
쉼표로 구분된 문자열을 문자열의 배열이나 컬렉션 또는 유형 변환 시스템에 알려진 다른 유형으로 변환하는 데 기본 지원이 제공됩니다. 예를 들어 <code>@RequestHeader("Accept")</code>로 주석이 달린 메서드 매개 변수는 <code>String</code> 유형일 수 있지만 <code>String[]</code> 또는 <code>List&lt;String&gt;</code>일 수도 있습니다.</p>
</blockquote>