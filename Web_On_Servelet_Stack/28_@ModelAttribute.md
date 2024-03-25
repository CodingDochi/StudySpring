<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/modelattrib-method-args.html">반응형 스택에서 이에 상응하는 내용 보기</a> </p>
<p><code>@ModelAttribute</code> 메소드 매개변수 주석은 요청 매개변수를 모델 객체에 바인딩합니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}/edit"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">processSubmit</span><span class="token punctuation">(</span><span class="token annotation punctuation">@ModelAttribute</span> <span class="token class-name">Pet</span> pet<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token comment">// method logic...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>Pet</code> 인스턴스에 바인딩합니다.</p>
<p><code>Pet</code> 인스턴스는 다음과 같습니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-modelattrib-methods.html">@ModelAttribute 메소드</a>에 의해 추가될 수 있는 모델에서 액세스됩니다.</p>
</li>
<li>
<p>모델 속성이 클래스 수준 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/sessionattributes.html"><code>@SessionAttributes</code></a> annotation에 나열된 경우 HTTP 세션에서 액세스됩니다.</p>
</li>
<li>
<p>모델 속성 이름이 경로 변수 또는 요청 매개변수와 같은 요청 값의 이름과 일치하는 경우 <code>Converter</code>를 통해 가져옵니다(예제는 다음 참조).</p>
</li>
<li>
<p>기본 생성자를 통해 인스턴스화됩니다.</p>
</li>
<li>
<p>서블릿 요청 매개변수와 일치하는 인수를 사용하여 "primary 생성자"를 통해 인스턴스화됩니다. 인수 이름은 바이트코드의 런타임 유지 매개변수 이름을 통해 결정됩니다.</p>
</li>
</ul>
<p>위에서 언급한 바와 같이 모델 속성 이름이 경로 변수나 요청 매개변수 등 요청 값의 이름과 일치하고 호환되는 <code>Converter&lt;String, T&gt;</code> 이 있는 경우 <code>Converter&lt;String, T&gt;</code>를 사용하여 모델 객체를 얻을 수 있다. 아래 예에서 모델 속성 이름 <code>account</code>는 URI 경로 변수 <code>account</code>와 일치하며 아마도 지속성 저장소에서 이를 검색하는 등록된 <code>Converter&lt;String, Account&gt;</code>가 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PutMapping</span><span class="token punctuation">(</span><span class="token string">"/accounts/{account}"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">save</span><span class="token punctuation">(</span><span class="token annotation punctuation">@ModelAttribute</span><span class="token punctuation">(</span><span class="token string">"account"</span><span class="token punctuation">)</span> <span class="token class-name">Account</span> account<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>기본적으로 생성자와 속성 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-binding">데이터 바인딩</a>이 모두 적용됩니다. 그러나 모델 객체 설계에는 세심한 고려가 필요하며 보안상의 이유로 웹 바인딩에 특화된 객체를 사용하거나 생성자 바인딩만 적용하는 것이 좋습니다. 속성 바인딩을 계속 사용해야 하는 경우 allowedFields 패턴을 설정하여 설정할 수 있는 속성을 제한해야 합니다. 이에 대한 자세한 내용과 구성 예는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-initbinder.html#mvc-ann-initbinder-model-design">모델 설계</a>를 참조하세요.</p>
<p>생성자 바인딩을 사용할 때 <code>@BindParam</code> annotation을 통해 요청 매개변수 이름을 사용자 정의할 수 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token keyword">class</span> <span class="token class-name">Account</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> firstName<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">Account</span><span class="token punctuation">(</span><span class="token annotation punctuation">@BindParam</span><span class="token punctuation">(</span><span class="token string">"first-name"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> firstName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>firstName <span class="token operator">=</span> firstName<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@BindParam</code>은 생성자 매개변수에 해당하는 필드에 배치될 수도 있습니다. <code>@BindParam</code>은 기본적으로 지원되지만 <code>DataBinder</code>에서 <code>DataBinder.NameResolver</code>를 설정하여 다른 annotation을 사용할 수도 있습니다.</p>
</blockquote>
<p>어떤 경우에는 데이터 바인딩 없이 모델 속성에 액세스하고 싶을 수도 있습니다. 이러한 경우 <code>Model</code>을 컨트롤러에 삽입하고 직접 액세스하거나 다음 예제와 같이 <code>@ModelAttribute(bind=false)</code>를 설정할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ModelAttribute</span>
<span class="token keyword">public</span> <span class="token class-name">AccountForm</span> <span class="token function">setUpForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">AccountForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@ModelAttribute</span>
<span class="token keyword">public</span> <span class="token class-name">Account</span> <span class="token function">findAccount</span><span class="token punctuation">(</span><span class="token annotation punctuation">@PathVariable</span> <span class="token class-name">String</span> accountId<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> accountRepository<span class="token punctuation">.</span><span class="token function">findOne</span><span class="token punctuation">(</span>accountId<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"update"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">update</span><span class="token punctuation">(</span><span class="token class-name">AccountForm</span> form<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> result<span class="token punctuation">,</span>
		<span class="token annotation punctuation">@ModelAttribute</span><span class="token punctuation">(</span>binding<span class="token operator">=</span><span class="token boolean">false</span><span class="token punctuation">)</span> <span class="token class-name">Account</span> account<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@ModelAttribute(binding=false)</code>를 설정합니다.</p>
<p>데이터 바인딩으로 인해 오류가 발생하면 기본적으로 <code>MethodArgumentNotValidException</code>이 발생하지만 컨트롤러 메서드에서 이러한 오류를 처리하기 위해 <code>@ModelAttribute</code> 바로 옆에 <code>BindingResult</code> 인수를 추가할 수도 있습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}/edit"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">processSubmit</span><span class="token punctuation">(</span><span class="token annotation punctuation">@ModelAttribute</span><span class="token punctuation">(</span><span class="token string">"pet"</span><span class="token punctuation">)</span> <span class="token class-name">Pet</span> pet<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1)</span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">hasErrors</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token string">"petForm"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@ModelAttribute</code> 옆에 <code>BindingResult</code>를 추가합니다.</p>
<p><code>jakarta.validation.Valid</code> annotation이나 Spring의 <code>@Validated</code> annotation을 추가하면 데이터 바인딩 후 자동으로 유효성 검사를 적용할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html">Bean Validation</a> 검사 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/validation.html">Spring 유효성 검사</a>를 참조하세요. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@PostMapping</span><span class="token punctuation">(</span><span class="token string">"/owners/{ownerId}/pets/{petId}/edit"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">processSubmit</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Valid</span> <span class="token annotation punctuation">@ModelAttribute</span><span class="token punctuation">(</span><span class="token string">"pet"</span><span class="token punctuation">)</span> <span class="token class-name">Pet</span> pet<span class="token punctuation">,</span> <span class="token class-name">BindingResult</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token comment">// (1) </span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span>result<span class="token punctuation">.</span><span class="token function">hasErrors</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token string">"petForm"</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>Pet</code> 인스턴스를 검증합니다.</p>
<p><code>@ModelAttribute</code> 뒤에 <code>BindingResult</code> 매개변수가 없으면 유효성 검사 오류와 함께 <code>MethodArgumentNotValueException</code>이 발생합니다. 그러나 다른 매개변수에 <code>@jakarta.validation.Constraint</code> annotation이 있어서 메서드 유효성 검사가 적용되는 경우 대신 <code>HandlerMethodValidationException</code>이 발생합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html">유효성</a> 검사 섹션을 참조하세요.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>@ModelAttribute</code> 사용은 선택 사항입니다. 기본적으로 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/beans/BeanUtils.html#isSimpleProperty-java.lang.Class-">BeanUtils#isSimpleProperty</a>에 의해 결정된 단순 값 유형이 아니고 다른 인수 resolver에 의해 resolve되지 않은 매개변수는 암시적 <code>@ModelAttribute</code>로 처리됩니다.</p>
</blockquote>
<blockquote>
<p><strong>[Warning]</strong><br>
GraalVM을 사용하여 네이티브 이미지로 컴파일할 때 위에 설명된 암시적 <code>@ModelAttribute</code> 지원은 관련 데이터 바인딩 반영 힌트에 대한 적절한 사전 추론(ahead-of-time inference)을 허용하지 않습니다. 결과적으로 GraalVM 네이티브 이미지에서 사용하기 위해 <code>@ModelAttribute</code>로 메서드 매개변수에 명시적으로 annotation을 추가하는 것이 좋습니다.</p>
</blockquote>