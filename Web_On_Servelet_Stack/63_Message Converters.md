<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-message-codecs">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Java 구성에서 사용할 <code>HttpMessageConverter</code> 인스턴스를 설정하고, <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#configureMessageConverters-java.util.List-"><code>configureMessageConverters()</code></a>를 재정의하여 기본적으로 사용되는 인스턴스를 대체할 수 있습니다. 또한 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#extendMessageConverters-java.util.List-"><code>extendMessageConverters()</code></a>를 재정의하여 마지막에 구성된 메시지 변환기 목록을 사용자 정의할 수도 있습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
Spring Boot 애플리케이션에서 <code>WebMvcAutoConfiguration</code>은 기본 변환기 외에 감지한 모든 <code>HttpMessageConverter</code> Bean을 추가합니다. 따라서 Boot 애플리케이션에서는 <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/web.html#web.servlet.spring-mvc.message-converters">HttpMessageConverters</a> 메커니즘을 사용하는 것이 좋습니다. 또는 <code>extendMessageConverters</code>를 사용하여 마지막에 메시지 변환기를 수정합니다.</p>
</blockquote>
<p>다음 예에서는 기본 <code>ObjectMapper</code> 대신 사용자 정의된 <code>ObjectMapper</code>를 사용하여 XML 및 Jackson JSON 변환기를 추가합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfiguration</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">configureMessageConverters</span><span class="token punctuation">(</span><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">HttpMessageConverter</span><span class="token punctuation">&lt;</span><span class="token operator">?</span><span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span></span> converters<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Jackson2ObjectMapperBuilder</span> builder <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Jackson2ObjectMapperBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">indentOutput</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">dateFormat</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">SimpleDateFormat</span><span class="token punctuation">(</span><span class="token string">"yyyy-MM-dd"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
				<span class="token punctuation">.</span><span class="token function">modulesToInstall</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">ParameterNamesModule</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		converters<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MappingJackson2HttpMessageConverter</span><span class="token punctuation">(</span>builder<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		converters<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MappingJackson2XmlHttpMessageConverter</span><span class="token punctuation">(</span>builder<span class="token punctuation">.</span><span class="token function">createXmlMapper</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/http/converter/json/Jackson2ObjectMapperBuilder.html"><code>Jackson2ObjectMapperBuilder</code></a>는 들여쓰기가 활성화된 <code>MappingJackson2HttpMessageConverter</code> 및 <code>MappingJackson2XmlHttpMessageConverter</code>, 사용자 정의 날짜 형식 및 <a href="https://github.com/FasterXML/jackson-module-parameter-names"><code>jackson-module-parameter-names</code></a> 등록을 위한 공통 구성을 생성하는 데 사용됩니다. (자바 8에 추가된 특성).</p>
<p>이 빌더는 다음과 같이 Jackson의 기본 속성을 사용자 정의합니다.</p>
<ul>
<li>
<p><code>DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES</code>가 비활성화되었습니다.</p>
</li>
<li>
<p><code>MapperFeature.DEFAULT_VIEW_INCLUSION</code>이 비활성화되었습니다.</p>
</li>
</ul>
<p>또한 클래스 경로에서 감지되면 다음과 같은 잘 알려진 모듈을 자동으로 등록합니다.</p>
<ul>
<li>
<p><a href="https://github.com/FasterXML/jackson-datatype-joda">jackson-datatype-joda</a>: Joda-Time 유형을 지원합니다.</p>
</li>
<li>
<p><a href="https://github.com/FasterXML/jackson-datatype-jsr310">jackson-datatype-jsr310</a>: Java 8 날짜 및 시간 API 유형을 지원합니다.</p>
</li>
<li>
<p><a href="https://github.com/FasterXML/jackson-datatype-jdk8">jackson-datatype-jdk8</a>: <code>Optional</code>과 같은 다른 Java 8 유형을 지원합니다.</p>
</li>
<li>
<p><a href="https://github.com/FasterXML/jackson-module-kotlin"><code>jackson-module-kotlin</code></a>: Kotlin 클래스 및 데이터 클래스를 지원합니다.</p>
</li>
</ul>
<blockquote>
<p><strong>[Note]</strong><br>
Jackson XML 지원으로 들여쓰기를 활성화하려면 <code>jackson-dataformat-xml</code> 종속성 외에 <code>woodstox-core-asl</code> 종속성이 필요합니다.<br>
다른 흥미로운 Jackson 모듈도 사용할 수 있습니다:</p>
</blockquote>
<ul>
<li>
<p><a href="https://github.com/zalando/jackson-datatype-money">jackson-datatype-money</a>: <code>javax.money</code> 유형 지원(비공식 모듈)</p>
</li>
<li>
<p><a href="https://github.com/FasterXML/jackson-datatype-hibernate">jackson-datatype-hibernate</a>: Hibernate 관련 유형 및 속성(지연 로딩 aspect 포함)을 지원합니다.</p>
</li>
</ul>
<p>다음 예에서는 XML에서 동일한 구성을 달성하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>message-converters</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.http.converter.json.MappingJackson2HttpMessageConverter<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>objectMapper<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>objectMapper<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.http.converter.xml.MappingJackson2XmlHttpMessageConverter<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>objectMapper<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>xmlMapper<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>message-converters</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">mvc:</span>annotation-driven</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>objectMapper<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean<span class="token punctuation">"</span></span>
	  <span class="token attr-name"><span class="token namespace">p:</span>indentOutput</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span>
	  <span class="token attr-name"><span class="token namespace">p:</span>simpleDateFormat</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yyyy-MM-dd<span class="token punctuation">"</span></span>
	  <span class="token attr-name"><span class="token namespace">p:</span>modulesToInstall</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.fasterxml.jackson.module.paramnames.ParameterNamesModule<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>xmlMapper<span class="token punctuation">"</span></span> <span class="token attr-name">parent</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>objectMapper<span class="token punctuation">"</span></span> <span class="token attr-name"><span class="token namespace">p:</span>createXmlMapper</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>