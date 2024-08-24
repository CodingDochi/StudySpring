<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/config.html#webflux-config-advanced-java">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p><code>@EnableWebMvc</code>는 다음과 같은 <code>DelegatingWebMvcConfiguration</code>을 가져옵니다.</p>
<ul>
<li>
<p>Spring MVC 애플리케이션을 위한 기본 Spring 구성 제공</p>
</li>
<li>
<p><code>WebMvcConfigurer</code> 구현을 감지하고 위임하여 해당 구성을 사용자 지정합니다.</p>
</li>
</ul>
<p>고급 모드의 경우 다음 예제와 같이 <code>WebMvcConfigurer</code>를 구현하는 대신 <code>@EnableWebMvc</code>를 제거하고 <code>DelegatingWebMvcConfiguration</code>에서 직접 확장할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token keyword">extends</span> <span class="token class-name">DelegatingWebMvcConfiguration</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>WebConfig</code>에서 기존 메소드를 유지할 수 있지만 이제 기본 클래스에서 Bean 선언을 대체할 수도 있으며 classpath에 다른 <code>WebMvcConfigurer</code> 구현을 얼마든지 가질 수 있습니다.</p>