<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-customize">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Java 구성에서는 다음 예제와 같이 <code>WebMvcConfigurer</code> 인터페이스를 구현할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableWebMvc</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">implements</span> <span class="token class-name">WebMvcConfigurer</span> <span class="token punctuation">{</span>

	<span class="token comment">// Implement configuration methods...</span>
<span class="token punctuation">}</span></code></pre>
<p>XML에서는 <code>&lt;mvc:annotation-driven/&gt;</code>의 속성과 하위 요소를 확인할 수 있습니다. <a href="https://schema.spring.io/mvc/spring-mvc.xsd">Spring MVC XML 스키마</a>를 보거나 IDE의 코드 완성 기능을 사용하여 사용 가능한 속성과 하위 요소를 찾을 수 있습니다.</p>