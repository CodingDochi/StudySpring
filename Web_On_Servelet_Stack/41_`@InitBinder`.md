<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-initbinder.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@Controller</code> 또는 <code>@ControllerAdvice</code> 클래스는 <code>@InitBinder</code> 메서드를 사용하여 <code>WebDataBinder</code> 인스턴스를 초기화할 수 있으며, 이는 다음을 수행할 수 있습니다.</p>
<ul>
<li>
<p>요청 매개변수를 모델 객체에 바인딩합니다.</p>
</li>
<li>
<p>요청 값을 문자열에서 객체 속성 유형으로 변환합니다.</p>
</li>
<li>
<p>HTML 양식을 렌더링할 때 모델 객체 속성의 형식을 문자열로 지정합니다.</p>
</li>
</ul>
<p><code>@Controller</code>에서 <code>DataBinder</code> 사용자 정의는 컨트롤러 내에서 로컬로 적용되거나 annotation을 통해 이름으로 참조되는 특정 모델 속성에도 적용됩니다. <code>@ControllerAdvice</code>에서 사용자 정의는 컨트롤러 전체 또는 일부에 적용될 수 있습니다.</p>
<p>유형 변환을 위해 <code>DataBinder</code>에 <code>PropertyEditor</code>, <code>Converter</code> 및 <code>Formatter</code> 구성 요소를 등록할 수 있습니다. 또는 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/conversion.html">MVC 구성</a>을 사용하여 전역적으로 공유되는 <code>FormattingConversionService</code>에 <code>Converter</code> 및 <code>Formatter</code> 구성 요소를 등록할 수 있습니다.</p>
<p><code>@InitBinder</code> 메소드는 <code>@ModelAttribute</code>를 제외하고 <code>@RequestMapping</code> 메소드와 동일한 인수를 많이 가질 수 있습니다. 일반적으로 이러한 메서드에는 <code>WebDataBinder</code> 인수(등록용)와 <code>void</code> 반환 값이 있습니다. 예를 들면 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FormController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@InitBinder</span> <span class="token comment">// (1)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">SimpleDateFormat</span> dateFormat <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleDateFormat</span><span class="token punctuation">(</span><span class="token string">"yyyy-MM-dd"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		dateFormat<span class="token punctuation">.</span><span class="token function">setLenient</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		binder<span class="token punctuation">.</span><span class="token function">registerCustomEditor</span><span class="token punctuation">(</span><span class="token class-name">Date</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">CustomDateEditor</span><span class="token punctuation">(</span>dateFormat<span class="token punctuation">,</span> <span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@InitBinder</code> 메소드를 정의합니다.</p>
<p>또는 공유 <code>FormattingConversionService</code>를 통해 <code>Formatter</code> 기반 설정을 사용하는 경우 다음 예제와 같이 동일한 접근 방식을 재사용하고 컨트롤러별 <code>Formatter</code> 구현을 등록할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FormController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@InitBinder</span> <span class="token comment">// (1)</span>
	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		binder<span class="token punctuation">.</span><span class="token function">addCustomFormatter</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">DateFormatter</span><span class="token punctuation">(</span><span class="token string">"yyyy-MM-dd"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 사용자 정의 포맷터에 <code>@InitBinder</code> 메소드를 정의합니다.</p>
<h1 id="model-design">Model Design</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-initbinder.html#webflux-ann-initbinder-model-design">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>웹 요청에 대한 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-binding">데이터 바인딩</a>에는 요청 매개변수를 모델 개체에 바인딩하는 작업이 포함됩니다. 기본적으로 요청 매개 변수는 모델 개체의 모든 공개 속성에 바인딩될 수 있습니다. 이는 악의적인 클라이언트가 모델 개체 그래프에 존재하지만 설정될 것으로 예상되지 않는 속성에 대한 추가 값을 제공할 수 있음을 의미합니다. 이것이 모델 객체 설계에 세심한 고려가 필요한 이유입니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
모델 개체 및 해당 중첩 개체 그래프는 명령 개체, 양식 지원 개체 또는 POJO(Plain Old Java 개체)라고도 합니다.</p>
</blockquote>
<p>웹 데이터 바인딩을 위해 JPA 또는 Hibernate 엔터티와 같은 도메인 모델을 노출하는 대신 전용 모델 개체를 사용하는 것이 좋습니다. 예를 들어 이메일 주소를 변경하는 양식에서 입력에 필요한 속성만 선언하는 <code>ChangeEmailForm</code> 모델 객체를 생성합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ChangeEmailForm</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> oldEmailAddress<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> newEmailAddress<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setOldEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> oldEmailAddress<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>oldEmailAddress <span class="token operator">=</span> oldEmailAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getOldEmailAddress</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>oldEmailAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setNewEmailAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> newEmailAddress<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>newEmailAddress <span class="token operator">=</span> newEmailAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getNewEmailAddress</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>newEmailAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>또 다른 좋은 방법은 생성자 인수에 필요한 요청 매개변수만 사용하고 다른 입력은 무시하는 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beans-beans.html#beans-constructor-binding">생성자 바인딩</a>을 적용하는 것입니다. 이는 일치하는 속성이 있는 모든 요청 매개변수를 기본적으로 바인딩하는 속성 바인딩과 대조됩니다.</p>
<p>전용 모델 객체나 생성자 바인딩 모두 충분하지 않고 속성 바인딩을 사용해야 하는 경우 예기치 않은 속성이 설정되는 것을 방지하기 위해 <code>WebDataBinder</code>에 <code>allowedFields</code> 패턴(대소문자 구분)을 등록하는 것이 좋습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ChangeEmailController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@InitBinder</span>
	<span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		binder<span class="token punctuation">.</span><span class="token function">setAllowedFields</span><span class="token punctuation">(</span><span class="token string">"oldEmailAddress"</span><span class="token punctuation">,</span> <span class="token string">"newEmailAddress"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// @RequestMapping methods, etc.</span>

<span class="token punctuation">}</span></code></pre>
<p><code>disallowedFields</code> 패턴을 등록할 수도 있습니다(대소문자 구분 안 함). 그러나 "허용" 구성은 "허용되지 않음"보다 더 명확하고 실수할 가능성이 적기 때문에 선호됩니다.</p>
<p>기본적으로 생성자와 속성 바인딩이 모두 사용됩니다. 생성자 바인딩만 사용하려는 경우 컨트롤러 내에서 로컬로 또는 <code>@ControllerAdvice</code>를 통해 전역적으로 <code>@InitBinder</code> 메서드를 통해 <code>WebDataBinder</code>에 <code>declarativeBinding</code> 플래그를 설정할 수 있습니다. 이 플래그를 켜면 생성자 바인딩만 사용되며 <code>allowedFields</code> 패턴이 구성되지 않은 한 속성 바인딩은 사용되지 않습니다. 예를 들어:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@InitBinder</span>
	<span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		binder<span class="token punctuation">.</span><span class="token function">setDeclarativeBinding</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// @RequestMapping methods, etc.</span>

<span class="token punctuation">}</span></code></pre>