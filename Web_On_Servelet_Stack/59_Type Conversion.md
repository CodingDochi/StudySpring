<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-conversion">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>기본적으로 필드의 <code>@NumberFormat</code> 및 <code>@DateTimeFormat</code>을 통한 사용자 정의 지원과 함께 다양한 숫자 및 날짜 유형에 대한 포맷터가 설치됩니다.</p>
<p>Java 구성에 사용자 정의 포맷터 및 변환기를 등록하려면 다음을 사용하십시오.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addFormatters</span><span class="token punctuation">(</span><span class="token class-name">FormatterRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>XML 구성에서 동일한 작업을 수행하려면 다음을 사용하십시오.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>mvc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/mvc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/mvc
		https://www.springframework.org/schema/mvc/spring-mvc.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span> <span class="token attr-name">conversion-service</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>conversionService<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>conversionService<span class="token punctuation">"</span></span>
			<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.format.support.FormattingConversionServiceFactoryBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>converters<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyConverter<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>formatters<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyFormatter<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyAnnotationFormatterFactory<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>formatterRegistrars<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyFormatterRegistrar<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>기본적으로 Spring MVC는 날짜 값을 구문 분석하고 형식화할 때 요청 Locale을 고려합니다. 이는 날짜가 "input" 양식 필드가 있는 문자열로 표시되는 양식에 적용됩니다. 그러나 "date" 및 "time" 양식 필드의 경우 브라우저는 HTML 사양에 정의된 고정 형식을 사용합니다. 이러한 경우 날짜 및 시간 형식을 다음과 같이 사용자 정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">addFormatters</span><span class="token punctuation">(</span><span class="token class-name">FormatterRegistry</span> registry<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">DateTimeFormatterRegistrar</span> registrar <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DateTimeFormatterRegistrar</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registrar<span class="token punctuation">.</span><span class="token function">setUseIsoFormat</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		registrar<span class="token punctuation">.</span><span class="token function">registerFormatters</span><span class="token punctuation">(</span>registry<span class="token punctuation">)</span><span class="token punctuation">;</span>
     	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
FormatterRegistrar 구현을 언제 사용해야 하는지에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/validation/format.html#format-FormatterRegistrar-SPI"><code>FormatterRegistrar</code> SPI</a> 및 <code>FormattingConversionServiceFactoryBean</code>을 참조하세요.</p>
</blockquote>