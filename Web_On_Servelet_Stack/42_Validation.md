<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-validation.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Spring MVC에는 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html">Java Bean 유효성 검사</a>를 사용하는 옵션을 포함하여 <code>@RequestMapping</code> 메서드에 대한 <a href="https://docs.spring.io/spring-framework/reference/core/validation/validator.html">유효성 검사</a> 지원이 내장되어 있습니다. 검증 지원은 두 가지 수준에서 작동합니다.</p>
<p>먼저 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/modelattrib-method-args.html">@ModelAttribute</a>, <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/requestbody.html">@RequestBody</a> 및 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/multipart-forms.html">@RequestPart</a> 메서드 매개 변수에 대한 해석기는 매개 변수에 Jakarta의 <code>@Valid</code> 또는 Spring의 <code>@Validated</code> annotation이 있는 경우 유효성 검사를 수행하고 필요한 경우 <code>MethodArgumentNotValidException</code>을 발생시킵니다. 또는 검증된 매개 변수 바로 뒤에 <code>Errors</code> 또는 <code>BindingResult</code> 메서드 매개 변수를 추가하여 컨트롤러 메서드의 오류를 처리할 수 있습니다.</p>
<p>둘째, <a href="https://beanvalidation.org/">Java Bean 유효성 검증</a>이 존재하고 메소드 매개변수에 <code>@Constraint</code> annotation이 있는 경우 메소드 유효성 검증이 대신 적용되어 필요한 경우 <code>HandlerMethodValidationException</code>이 발생합니다. 이 경우 <code>Errors</code> 또는 <code>BindingResult</code> 메서드 매개 변수를 추가하여 컨트롤러 메서드 내에서 유효성 검사 오류를 처리할 수 있지만, 다른 메서드 인수에 유효성 검사 오류가 있으면 대신 <code>HandlerMethodValidationException</code>이 발생합니다. 메소드에 <code>@Valid</code> 또는 <code>@Constraint</code> annotation이 추가된 경우 메소드 유효성 검증이 반환 값에 적용될 수 있습니다.</p>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/validation.html">WebMvc 구성</a>을 통해 전역적으로 <code>Validator</code>를 구성하거나 <code>@Controller</code> 또는 <code>@ControllerAdvice</code>의 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-initbinder.html">@InitBinder</a> 메서드를 통해 로컬로 구성할 수 있습니다. 여러 validator를 사용할 수도 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
컨트롤러에 <code>@Validated</code> 클래스 수준이 있으면 AOP 프록시를 통해 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html#validation-beanvalidation-spring-method">메서드 유효성 검사가 적용됩니다</a>. Spring Framework 6.1에 추가된 메서드 유효성 검사를 위한 Spring MVC 내장 지원을 활용하려면 컨트롤러에서 클래스 수준 <code>@Validated</code> annotation을 제거해야 합니다.</p>
</blockquote>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html">오류 응답</a> 섹션에서는 <code>MethodArgumentNotValidException</code> 및 <code>HandlerMethodValidationException</code>이 처리되는 방법과 <code>MessageSource</code> 및 로케일 및 언어별 리소스 번들을 통해 해당 렌더링을 사용자 정의할 수 있는 방법에 대한 자세한 내용을 제공합니다.</p>
<p>메서드 유효성 검사 오류를 추가로 사용자 지정 처리하려면 <code>ResponseEntityExceptionHandler</code>를 확장하거나 컨트롤러 또는 <code>@ControllerAdvice</code>에서 <code>@ExceptionHandler</code> 메서드를 사용하고 <code>HandlerMethodValidationException</code>을 직접 처리할 수 있습니다. 예외에는 메서드 매개 변수별로 유효성 검사 오류를 그룹화하는 <code>ParameterValidationResults</code> 목록이 포함되어 있습니다. 이를 반복하거나 컨트롤러 메서드 매개변수 유형별로 방문자에게 콜백 메서드를 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">HandlerMethodValidationException</span> ex <span class="token operator">=</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">;</span>

ex<span class="token punctuation">.</span><span class="token function">visitResults</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">HandlerMethodValidationException</span><span class="token punctuation">.</span><span class="token class-name">Visitor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">requestHeader</span><span class="token punctuation">(</span><span class="token class-name">RequestHeader</span> requestHeader<span class="token punctuation">,</span> <span class="token class-name">ParameterValidationResult</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">requestParam</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Nullable</span> <span class="token class-name">RequestParam</span> requestParam<span class="token punctuation">,</span> <span class="token class-name">ParameterValidationResult</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">modelAttribute</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Nullable</span> <span class="token class-name">ModelAttribute</span> modelAttribute<span class="token punctuation">,</span> <span class="token class-name">ParameterErrors</span> errors<span class="token punctuation">)</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">other</span><span class="token punctuation">(</span><span class="token class-name">ParameterValidationResult</span> result<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>