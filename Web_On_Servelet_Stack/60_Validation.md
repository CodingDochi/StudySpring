<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-validation">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>기본적으로 <a href="https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html#validation-beanvalidation-overview">Bean 유효성 검사</a>가 클래스 경로(예: Hibernate Validator)에 존재하는 경우 <code>LocalValidatorFactoryBean</code>은 컨트롤러 메서드 인수에서<code> @Valid</code> 및 <code>@Validated</code>와 함께 사용하기 위한 전역 <a href="https://docs.spring.io/spring-framework/reference/core/validation/validator.html">유효성 검사기</a>로 등록됩니다.</p>
<p>Java 구성에서는 다음 예제와 같이 전역 <code>Validator</code> 인스턴스를 사용자 정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token class-name">Validator</span> <span class="token function">getValidator</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>mvc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/mvc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/mvc
		https://www.springframework.org/schema/mvc/spring-mvc.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span> <span class="token attr-name">validator</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>globalValidator<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 예제와 같이 <code>Validator</code> 구현을 로컬로 등록할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Controller</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyController</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@InitBinder</span>
	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">initBinder</span><span class="token punctuation">(</span><span class="token class-name">WebDataBinder</span> binder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		binder<span class="token punctuation">.</span><span class="token function">addValidators</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FooValidator</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>LocalValidatorFactoryBean</code>을 어딘가에 주입해야 하는 경우 MVC 구성에 선언된 항목과의 충돌을 피하기 위해 Bean을 생성하고 <code>@Primary</code>로 표시합니다.</p>
</blockquote>