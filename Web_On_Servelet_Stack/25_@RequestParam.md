<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/requestparam.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><code>@RequestParam</code> annotation을 사용하여 서블릿 요청 매개변수(즉, 쿼리 매개변수 또는 양식 데이터)를 컨트롤러의 메서드 인수에 바인딩할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/pets"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EditPetForm</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@GetMapping</span>
	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">setupForm</span><span class="token punctuation">(</span><span class="token annotation punctuation">@RequestParam</span><span class="token punctuation">(</span><span class="token string">"petId"</span><span class="token punctuation">)</span> <span class="token keyword">int</span> petId<span class="token punctuation">,</span> <span class="token class-name">Model</span> model<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
		<span class="token class-name">Pet</span> pet <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>clinic<span class="token punctuation">.</span><span class="token function">loadPet</span><span class="token punctuation">(</span>petId<span class="token punctuation">)</span><span class="token punctuation">;</span>
		model<span class="token punctuation">.</span><span class="token function">addAttribute</span><span class="token punctuation">(</span><span class="token string">"pet"</span><span class="token punctuation">,</span> pet<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token string">"petForm"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>

<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@RequestParam</code>을 사용하여 <code>petId</code>를 바인딩합니다.</p>
<p>기본적으로 이 annotation을 사용하는 메서드 매개 변수는 필수이지만 <code>@RequestParam</code> annotation의 <code>required</code> 플래그를 <code>false</code>로 설정하거나 <code>java.util.Optional</code> 래퍼로 인수를 선언하여 메서드 매개 변수가 선택 사항임을 지정할 수 있습니다.</p>
<p>대상 메소드 매개변수 type이 <code>String</code>이 아닌 경우 유형 변환이 자동으로 적용됩니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/typeconversion.html">type 변환</a>을 참조하세요.</p>
<p>인수 유형을 배열이나 목록으로 선언하면 동일한 매개변수 이름에 대해 여러 매개변수 값을 확인할 수 있습니다.</p>
<p><code>@RequestParam</code> annotation이 annotation에 지정된 매개변수 이름 없이 <code>Map&lt;String, String&gt;</code> 또는 <code>MultiValueMap&lt;String, String&gt;</code>으로 선언되면 지정된 각 매개변수 이름에 대한 요청 매개변수 값으로 맵이 채워집니다.</p>
<p><code>@RequestParam</code> 사용은 선택 사항입니다(예: 속성 설정). 기본적으로 단순 값 유형(<a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-">BeanUtils#isSimpleProperty</a>에 의해 결정됨)이고 다른 인수 확인자에 의해 해결되지 않는 모든 인수는 <code>@RequestParam</code>으로 annotation이 달린 것처럼 처리됩니다.</p>