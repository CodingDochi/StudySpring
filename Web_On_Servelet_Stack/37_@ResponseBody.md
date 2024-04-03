<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/responsebody.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>메서드에 <code>@ResponseBody</code> annotation을 사용하여 <a href="https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-message-conversion">HttpMessageConverter</a>를 통해 응답 본문에 반환을 직렬화할 수 있습니다. 다음 목록은 예를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/accounts/{id}"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@ResponseBody</span>
<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@ResponseBody</code>는 클래스 수준에서도 지원되며, 이 경우 모든 컨트롤러 메서드에서 상속됩니다. 이는 <code>@Controller</code> 및 <code>@ResponseBody</code>로 표시된 메타 annotation에 불과한 <code>@RestController</code>의 효과입니다.</p>
<p>반응형 유형에 <code>@ResponseBody</code>를 사용할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html">비동기 요청</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-async.html#mvc-ann-async-reactive-types">반응형 유형</a>을 참조하세요.</p>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config.html">MVC Config</a>의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/message-converters.html">메시지 Converters</a> 옵션을 사용하여 메시지 변환을 구성하거나 사용자 정의할 수 있습니다.</p>
<p><code>@ResponseBody</code> 메소드를 JSON 직렬화 뷰와 결합할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/jackson.html">잭슨 JSON</a>을 참조하세요.</p>