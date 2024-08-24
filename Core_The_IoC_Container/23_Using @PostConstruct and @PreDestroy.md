<p><code>CommonAnnotationBeanPostProcessor</code>는 <code>@Resource</code> 어노테이션뿐만 아니라 JSR-250 라이프사이클 어노테이션(<code>jakarta.annotation.PostConstruct</code> 및 <code>jakarta.annotation.PreDestroy</code>)도 인식합니다. Spring 2.5에 도입된 이러한 주석 지원은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean">초기화 콜백</a> 및 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-disposablebean">소멸 콜백</a>에 설명된 수명 주기 콜백 메커니즘에 대한 대안을 제공합니다. <code>CommonAnnotationBeanPostProcessor</code>가 Spring <code>ApplicationContext</code> 내에 등록된 경우 이러한 어노테이션 중 하나를 전달하는 메소드는 해당 Spring 라이프사이클 인터페이스 메소드 또는 명시적으로 선언된 콜백 메소드와 라이프사이클의 동일한 지점에서 호출됩니다. 다음 예에서는 캐시가 초기화 시 미리 채워지고(pre-populated) 삭제 시 지워집니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CachingMovieLister</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@PostConstruct</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">populateMovieCache</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// populates the movie cache upon initialization...</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@PreDestroy</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">clearMovieCache</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// clears the movie cache upon destruction...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다양한 수명 주기 메커니즘 연결(combine)의 효과에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-combined-effects">수명 주기 메커니즘 연결(combine)</a>을 참조하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Resource</code>와 마찬가지로 <code>@PostConstruct</code> 및 <code>@PreDestroy</code> 어노테이션 타입은 JDK 6부터 8까지 표준 Java 라이브러리의 일부였습니다. 그러나 전체 <code>javax.annotation</code> 패키지는 JDK 9의 핵심 Java 모듈에서 분리되어 결국 JDK 11에서 제거되었습니다. Jakarta EE 9부터 패키지는 이제 <code>jakarta.annotation</code>에 있습니다. 필요한 경우 이제 Maven Central을 통해 <code>jakarta.annotation-api</code> 아티팩트를 가져와야 하며 다른 라이브러리처럼 애플리케이션의 클래스패스에 추가하기만 하면 됩니다.</p>
</blockquote>