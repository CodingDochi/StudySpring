<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-modelattrib-methods.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@ModelAttribute</code> annotation을 사용할 수 있습니다.</p>
<ul>
<li>
<p><code>@RequestMapping</code> 메소드의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/modelattrib-method-args.html">메소드 인수</a>에서 모델의 <code>Object</code>를 생성 또는 액세스하고 <code>WebDataBinder</code>를 통해 요청에 바인딩합니다.</p>
</li>
<li>
<p><code>@RequestMapping</code> 메서드 호출 전에 모델을 초기화하는 데 도움이 되는 <code>@Controller</code> 또는 <code>@ControllerAdvice</code> 클래스의 메서드 수준 annotation으로 사용됩니다.</p>
</li>
<li>
<p>반환 값을 표시하는 <code>@RequestMapping</code> 메소드에는 모델 속성이 있습니다.</p>
</li>
</ul>
<p>이 섹션에서는 이전 목록의 두 번째 항목인 <code>@ModelAttribute</code> 메서드에 대해 설명합니다. 컨트롤러에는 <code>@ModelAttribute</code> 메서드가 얼마든지 있을 수 있습니다. 이러한 모든 메소드는 동일한 컨트롤러의 <code>@RequestMapping</code> 메소드보다 먼저 호출됩니다. <code>@ModelAttribute</code> 메서드는 <code>@ControllerAdvice</code>를 통해 컨트롤러 간에 공유될 수도 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html">컨트롤러 advice</a> 섹션을 참조하세요.</p>
<p><code>@ModelAttribute</code> 메소드에는 유연한 메소드 서명이 있습니다. <code>@ModelAttribute</code> 자체나 요청 본문과 관련된 항목을 제외하고 <code>@RequestMapping</code> 메서드와 동일한 인수를 많이 지원합니다.</p>
<p>다음 예제에서는 <code>@ModelAttribute</code> 메서드를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ModelAttribute</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">populateModel</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span> <span class="token class-name">String</span> number<span class="token punctuation">,</span> <span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span>accountRepository<span class="token punctuation">.</span><span class="token function">findAccount</span><span class="token punctuation">(</span>number<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// add more ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 하나의 속성만 추가합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ModelAttribute</span>
<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">addAccount</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span> <span class="token class-name">String</span> number<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> accountRepository<span class="token punctuation">.</span><span class="token function">findAccount</span><span class="token punctuation">(</span>number<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
이름이 명시적으로 지정되지 않은 경우 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/core/Conventions.html"><code>Conventions</code></a>에 대한 javadoc에 설명된 대로 <code>Object</code> type에 따라 기본 이름이 선택됩니다. 오버로드된 <code>addAttribute</code> 메서드를 사용하거나 <code>@ModelAttribute</code>(반환 값의 경우)의 <code>name</code> 속성을 통해 항상 명시적인 이름을 할당할 수 있습니다.</p>
</blockquote>
<p><code>@RequestMapping</code> 메소드의 메소드 레벨 annotation으로 <code>@ModelAttribute</code>를 사용할 수도 있습니다. 이 경우 <code>@RequestMapping</code> 메소드의 반환 값은 모델 속성으로 해석됩니다. 이는 반환 값이 뷰 이름으로 해석되는 <code>String</code>이 아닌 한 HTML 컨트롤러의 기본 동작이므로 일반적으로 필요하지 않습니다. <code>@ModelAttribute</code>는 다음 예제와 같이 모델 속성 이름을 사용자 정의할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@GetMapping</span><span class="token punctuation">(</span><span class="token string">"/accounts/{id}"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@ModelAttribute</span><span class="token punctuation">(</span><span class="token string">"myAccount"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">handle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
	<span class="token keyword">return</span> account<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>