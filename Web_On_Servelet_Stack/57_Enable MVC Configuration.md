<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-enable">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Java 구성에서는 다음 예제와 같이 <code>@EnableWebMvc</code> 어노테이션을 사용하여 MVC 구성을 활성화할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>XML 구성에서는 다음 예제와 같이 <code>&lt;mvc:annotation-driven&gt;</code> 요소를 사용하여 MVC 구성을 활성화할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>mvc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/mvc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/mvc
		https://www.springframework.org/schema/mvc/spring-mvc.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">mvc:</span>annotation-driven</span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이전 예제에서는 다수의 Spring MVC <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/special-bean-types.html">인프라 Bean</a>을 등록하고 클래스 경로에서 사용 가능한 종속성에 적응합니다(예: JSON, XML 등에 대한 페이로드 변환기).</p>