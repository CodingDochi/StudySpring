<p><code>@Value</code>는 일반적으로 외부화(externalized)된 속성(property)을 주입하는 데 사용됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> catalog<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${catalog.name}"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> catalog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>catalog <span class="token operator">=</span> catalog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 구성을 사용합니다</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@PropertySource</span><span class="token punctuation">(</span><span class="token string">"classpath:application.properties"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span> <span class="token punctuation">}</span></code></pre>
<p>그리고 다음 <code>application.properties</code> 파일과 함께요:</p>
<pre><code class="language-null">catalog.name=MovieCatalog</code></pre>
<p>이 경우 <code>catalog</code> 매개변수와 필드는 <code>MovieCatalog</code> 값과 동일(equal)합니다.</p>
<p>기본 관대한(lenient) 내장 값 resolver는 Spring에서 제공됩니다. 속성(property) 값을 확인하려고 시도하며, 확인할 수 없는 경우 속성 이름(예: <code>${catalog.name}</code>)이 값으로 삽입됩니다. 존재하지 않는 값에 대해 엄격한 제어를 유지하려면 다음 예제와 같이 <code>PropertySourcesPlaceholderConfigurer</code> 빈을 선언해야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token class-name">PropertySourcesPlaceholderConfigurer</span> <span class="token function">propertyPlaceholderConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">PropertySourcesPlaceholderConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
JavaConfig를 사용하여 <code>PropertySourcesPlaceholderConfigurer</code>를 구성할 때 <code>@Bean</code> 메서드는 <code>static</code>이어야 합니다.</p>
</blockquote>
<p>위 구성을 사용하면 <code>${}</code> 자리 표시자를 resolve 할 수 없는 경우 Spring 초기화가 실패합니다. <code>setPlaceholderPrefix</code>, <code>setPlaceholderSuffix</code> 또는 <code>setValueSeparator</code>와 같은 메소드를 사용하여 자리 표시자를 사용자 정의하는 것도 가능합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Boot는 기본적으로 <code>application.properties</code> 및 <code>application.yml</code> 파일에서 속성을 가져오는 <code>PropertySourcesPlaceholderConfigurer</code> 빈을 구성합니다.</p>
</blockquote>
<p>Spring이 제공하는 내장 변환기(converter) 지원을 통해 간단한 타입 변환(예: <code>Integer</code> 또는 <code>int</code>로)을 자동으로 처리할 수 있습니다. 여러 개의 쉼표로 구분된 값을 추가 노력 없이 자동으로 <code>String</code> 배열로 변환할 수 있습니다.</p>
<p>다음과 같이 기본값을 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> catalog<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${catalog.name:defaultCatalog}"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> catalog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>catalog <span class="token operator">=</span> catalog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring <code>BeanPostProcessor</code>는 배후에서 <code>ConversionService</code>를 사용하여 <code>@Value</code>의 <code>String</code> 값을 대상 타입으로 변환하는 프로세스를 처리합니다. 고유한 사용자 정의 타입에 대한 변환 지원을 제공하려는 경우 다음 예제와 같이 고유한 <code>ConversionService</code> Bean 인스턴스를 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Bean</span>
    <span class="token keyword">public</span> <span class="token class-name">ConversionService</span> <span class="token function">conversionService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">DefaultFormattingConversionService</span> conversionService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultFormattingConversionService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        conversionService<span class="token punctuation">.</span><span class="token function">addConverter</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyCustomConverter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> conversionService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Value</code>에 <a href="https://docs.spring.io/spring-framework/reference/core/expressions.html"><code>SpEL</code> 표현식</a>이 포함된 경우 값은 다음 예제와 같이 런타임 시 동적으로 계산됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> catalog<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{systemProperties['user.catalog'] + 'Catalog' }"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> catalog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>catalog <span class="token operator">=</span> catalog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>SpEL을 사용하면 더욱 복잡한 데이터 구조도 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> countOfMoviesPerCatalog<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span>
            <span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{{'Thriller': 100, 'Comedy': 300}}"</span><span class="token punctuation">)</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> countOfMoviesPerCatalog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>countOfMoviesPerCatalog <span class="token operator">=</span> countOfMoviesPerCatalog<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>