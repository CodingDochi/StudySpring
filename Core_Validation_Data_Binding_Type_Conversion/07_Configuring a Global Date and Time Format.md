<p>기본적으로 <code>@DateTimeFormat</code>으로 어노테이션이 추가되지 않은 날짜 및 시간 필드는 <code>DateFormat.SHORT</code> 스타일을 사용하여 문자열에서 변환됩니다. 원하는 경우 고유한 전역 형식을 정의하여 이를 변경할 수 있습니다.</p>
<p>그렇게 하려면 Spring이 기본 포맷터를 등록하지 않도록 해야 합니다. 대신 다음을 사용하여 포맷터를 수동으로 등록하세요.</p>
<ul>
<li>
<p><code>org.springframework.format.datetime.standard.DateTimeFormatterRegistrar</code></p>
</li>
<li>
<p><code>org.springframework.format.datetime.DateFormatterRegistrar</code></p>
</li>
</ul>
<p>예를 들어, 다음 Java 구성은 global <code>yyyyMMdd</code> 형식을 등록합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">FormattingConversionService</span> <span class="token function">conversionService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token comment">// Use the DefaultFormattingConversionService but do not register defaults</span>
		<span class="token class-name">DefaultFormattingConversionService</span> conversionService <span class="token operator">=</span>
			<span class="token keyword">new</span> <span class="token class-name">DefaultFormattingConversionService</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Ensure @NumberFormat is still supported</span>
		conversionService<span class="token punctuation">.</span><span class="token function">addFormatterForFieldAnnotation</span><span class="token punctuation">(</span>
			<span class="token keyword">new</span> <span class="token class-name">NumberFormatAnnotationFormatterFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Register JSR-310 date conversion with a specific global format</span>
		<span class="token class-name">DateTimeFormatterRegistrar</span> dateTimeRegistrar <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DateTimeFormatterRegistrar</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		dateTimeRegistrar<span class="token punctuation">.</span><span class="token function">setDateFormatter</span><span class="token punctuation">(</span><span class="token class-name">DateTimeFormatter</span><span class="token punctuation">.</span><span class="token function">ofPattern</span><span class="token punctuation">(</span><span class="token string">"yyyyMMdd"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		dateTimeRegistrar<span class="token punctuation">.</span><span class="token function">registerFormatters</span><span class="token punctuation">(</span>conversionService<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// Register date conversion with a specific global format</span>
		<span class="token class-name">DateFormatterRegistrar</span> dateRegistrar <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DateFormatterRegistrar</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		dateRegistrar<span class="token punctuation">.</span><span class="token function">setFormatter</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">DateFormatter</span><span class="token punctuation">(</span><span class="token string">"yyyyMMdd"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		dateRegistrar<span class="token punctuation">.</span><span class="token function">registerFormatters</span><span class="token punctuation">(</span>conversionService<span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token keyword">return</span> conversionService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>XML 기반 구성을 선호하는 경우 <code>FormattingConversionServiceFactoryBean</code>을 사용할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>conversionService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.format.support.FormattingConversionServiceFactoryBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>registerDefaultFormatters<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>false<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>formatters<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.format.number.NumberFormatAnnotationFormatterFactory<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>formatterRegistrars<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>set</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.format.datetime.standard.DateTimeFormatterRegistrar<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
					<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dateFormatter<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
						<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.format.datetime.standard.DateTimeFormatterFactoryBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
							<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>pattern<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yyyyMMdd<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
						<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
					<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>set</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>웹 애플리케이션에서 날짜 및 시간 형식을 구성할 때 추가 고려 사항이 있습니다. <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-config/conversion.html">WebMVC 변환 및 포맷</a> 또는 <a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-conversion">WebFlux 변환 및 포맷</a>을 참조하세요.</p>