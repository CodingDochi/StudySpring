<p>type별 autowiring은 여러 후보로 이어질 수 있으므로 선택 프로세스를 더 많이 제어해야 하는 경우가 많습니다. 이를 달성하는 한 가지 방법은 Spring의 <code>@Primary</code> 어노테이션을 사용하는 것입니다. <code>@Primary</code>는 여러 Bean이 단일 값 종속성에 자동 연결될 후보인 경우 특정 Bean에 우선순위를 부여해야 함을 나타냅니다. 후보 중에 정확히 하나의 기본 Bean이 존재하는 경우 이는 자동 연결 값이 됩니다.</p>
<p><code>firstMovieCatalog</code>를 기본 <code>MovieCatalog</code>로 정의하는 다음 구성을 고려하세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieConfiguration</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@Primary</span>
	<span class="token keyword">public</span> <span class="token class-name">MovieCatalog</span> <span class="token function">firstMovieCatalog</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">MovieCatalog</span> <span class="token function">secondMovieCatalog</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> <span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 구성을 사용하면 다음 <code>MovieRecommender</code>가 <code>firstMovieCatalog</code>와 함께 자동 연결됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>해당 Bean 정의는 다음과 같습니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span> <span class="token attr-name">primary</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>movieRecommender<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.MovieRecommender<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>