<p>Spring의 Java 기반 구성 기능을 사용하면 주석을 작성하여 구성의 복잡성을 줄일 수 있습니다.</p>
<h1 id="using-the-import-annotation">Using the <code>@Import</code> Annotation</h1>
<p><code>&lt;import/&gt;</code> 요소가 Spring XML 파일 내에서 구성 모듈화를 돕기 위해 사용되는 것과 마찬가지로 <code>@Import</code> 어노테이션은 다음 예제와 같이 다른 구성 클래스에서 <code>@Bean</code> 정의를 로드할 수 있도록 허용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConfigA</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">A</span> <span class="token function">a</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">A</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token class-name">ConfigA</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConfigB</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">B</span> <span class="token function">b</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">B</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 컨텍스트를 인스턴스화할 때 <code>ConfigA.class</code>와 <code>ConfigB.class</code>를 모두 지정할 필요 없이 다음 예제와 같이 <code>ConfigB</code>만 명시적으로 제공(supply)하면 됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">ConfigB</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token comment">// now both beans A and B will be available...</span>
	<span class="token class-name">A</span> a <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">A</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">B</span> b <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">B</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 접근 방식은 생성 중에 잠재적으로 많은 수의 <code>@Configuration</code> 클래스를 기억할 필요 없이 하나의 클래스만 처리하면 되므로 컨테이너 인스턴스화를 단순화합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Framework 4.2부터 <code>@Import</code>는 <code>AnnotationConfigApplicationContext.register</code> 메서드와 유사한 일반 구성 요소 클래스에 대한 참조도 지원합니다. 이는 모든 컴포넌트를 명시적으로 정의하기 위한 진입점으로 몇 가지 구성 클래스를 사용하여 component scanning을 피하려는 경우 특히 유용합니다.</p>
</blockquote>
<h2 id="injecting-dependencies-on-imported-bean-definitions">Injecting Dependencies on Imported <code>@Bean</code> Definitions</h2>
<p>앞의 예제는 작동하지만 단순합니다. 대부분의 실제 시나리오에서 Bean은 구성 클래스 전체에 걸쳐 서로 종속성을 갖습니다. XML을 사용할 때 컴파일러가 포함되지 않고 <code>ref="someBean"</code>을 선언하고 컨테이너 초기화 중에 Spring이 이를 해결한느 것을 신뢰할 수 있기 때문에 문제가 되지 않습니다. <code>@Configuration</code> 클래스를 사용할 때 Java 컴파일러는 구성(configuration) 모델에 제약 조건을 적용합니다. 즉, 다른 Bean에 대한 참조는 유효한 Java 구문(syntax)이어야 합니다.</p>
<p>다행히 이 문제를 해결하는 방법은 간단합니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html#beans-java-dependencies">이미 논의한 것처럼</a> <code>@Bean</code> 메소드는 빈 종속성을 설명하는 임의의 수의 매개변수를 가질 수 있습니다. 각각 다른 클래스에 선언된 Bean에 따라 달라지는 여러 <code>@Configuration</code> 클래스가 포함된 다음과 같은 보다 실제적인 시나리오를 고려해 보세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token class-name">AccountRepository</span> accountRepository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span>accountRepository<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RepositoryConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">AccountRepository</span> <span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">JdbcAccountRepository</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ServiceConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">RepositoryConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SystemTestConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// return new DataSource</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">SystemTestConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// everything wires up across configuration classes...</span>
	<span class="token class-name">TransferService</span> transferService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">TransferService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	transferService<span class="token punctuation">.</span><span class="token function">transfer</span><span class="token punctuation">(</span><span class="token number">100.00</span><span class="token punctuation">,</span> <span class="token string">"A123"</span><span class="token punctuation">,</span> <span class="token string">"C456"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>동일한 결과를 얻는 또 다른 방법이 있습니다. <code>@Configuration</code> 클래스는 궁극적으로 컨테이너의 또 다른 Bean일 뿐이라는 점을 기억하세요. 이는 <code>@Autowired</code> 및 <code>@Value</code> 주입과 다른 Bean과 동일한 기타 기능을 활용할 수 있음을 의미합니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
그런 식으로 주입하는 종속성이 가장 단순한 종류인지 확인하세요. <code>@Configuration</code> 클래스는 컨텍스트 초기화 중에 매우 일찍 처리되며 이러한 방식으로 종속성을 강제로 주입하면 예기치 않은 조기 초기화가 발생할 수 있습니다. 가능할 때마다 앞의 예와 같이 매개변수 기반 주입을 사용하세요.</p>
<p>동일한 구성 클래스의 <code>@PostConstruct</code> 메소드 내에서 지역적으로(locally) 정의된 Bean에 대한 액세스를 피하십시오. non-static <code>@Bean</code> 메소드는 의미론적으로(semantically) 완전히 초기화된 구성(configuration) 클래스 인스턴스를 호출해야 하기 때문에 이는 효과적으로 순환 참조로 이어집니다. 순환 참조가 허용되지 않으면(예: Spring Boot 2.6+에서) <code>BeanCurrentlyInCreationException</code>이 발생할 수 있습니다.</p>
<p>또한 <code>@Bean</code>을 통한 <code>BeanPostProcessor</code> 및 <code>BeanFactoryPostProcessor</code> 정의에 특히 주의하세요. 이는 일반적으로 포함된 구성 클래스의 인스턴스화를 트리거하지 않고 <code>static @Bean</code> 메소드로 선언되어야 합니다. 그렇지 않으면 <code>AutowiredAnnotationBeanPostProcessor</code>보다 이전의 Bean 인스턴스로 생성할 수 있으므로 <code>@Autowired</code> 및 <code>@Value</code>가 구성(configuration) 클래스 자체에서 작동하지 않을 수 있습니다.</p>
</blockquote>
<p>다음 예제는 하나의 Bean을 다른 Bean에 자동 연결하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">AccountRepository</span> accountRepository<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span>accountRepository<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">RepositoryConfig</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">DataSource</span> dataSource<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">RepositoryConfig</span><span class="token punctuation">(</span><span class="token class-name">DataSource</span> dataSource<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>dataSource <span class="token operator">=</span> dataSource<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">AccountRepository</span> <span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">JdbcAccountRepository</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ServiceConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">RepositoryConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SystemTestConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// return new DataSource</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">SystemTestConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// everything wires up across configuration classes...</span>
	<span class="token class-name">TransferService</span> transferService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">TransferService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	transferService<span class="token punctuation">.</span><span class="token function">transfer</span><span class="token punctuation">(</span><span class="token number">100.00</span><span class="token punctuation">,</span> <span class="token string">"A123"</span><span class="token punctuation">,</span> <span class="token string">"C456"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>@Configuration</code> 클래스의 생성자 주입은 Spring Framework 4.3부터만 지원됩니다. 또한 대상 Bean이 생성자를 하나만 정의하는 경우 <code>@Autowired</code>를 지정할 필요가 없습니다.</p>
</blockquote>
<p><em>탐색의 용이성을 위해 완전한 자격을 갖춘 imported Bean</em></p>
<p>앞의 시나리오에서 <code>@Autowired</code>를 사용하는 것은 잘 작동하고 원하는 모듈성을 제공하지만 autowired bean 정의가 선언되는 위치를 정확히 결정하는 것은 여전히 다소 모호합니다. 예를 들어, <code>ServiceConfig</code>를 바라보는(looking at) 개발자로서 <code>@Autowired AccountRepository</code> 빈이 선언된 위치를 정확히 어떻게 알 수 있습니까? 코드에 명시적이지는 않지만 괜찮을 수도 있습니다. <a href="https://spring.io/tools">Eclipse용 Spring 도구</a>는 모든 것이 어떻게 연결되어 있는지 보여주는 그래프를 렌더링할 수 있는 도구를 제공하며, 이것이 필요한 전부일 수 있습니다. 또한 Java IDE는 <code>AccountRepository</code> 유형의 모든 선언 및 사용을 쉽게 찾고 해당 유형을 반환하는 <code>@Bean</code> 메소드의 위치를 빠르게 표시할 수 있습니다.</p>
<p>이러한 모호함이 허용되지 않고 IDE 내에서 하나의 <code>@Configuration</code> 클래스에서 다른 클래스로 직접 탐색하려는 경우, 구성(configuration) 클래스 자체를 자동 연결(autowire)하는 것이 좋습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">RepositoryConfig</span> repositoryConfig<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// navigate 'through' the config class to the @Bean method!</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span>repositoryConfig<span class="token punctuation">.</span><span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 상황에서는 <code>AccountRepository</code>가 정의되는 위치가 완전히 명시적입니다. 그러나 <code>ServiceConfig</code>는 이제 <code>RepositoryConfig</code>와 긴밀하게 결합(coupled)됩니다. 그것이 절충안입니다. 이러한 긴밀한 결합은 인터페이스 기반 또는 추상 클래스 기반 <code>@Configuration</code> 클래스를 사용하여 어느 정도 완화될 수 있습니다. 다음 예를 고려하십시오.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ServiceConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">RepositoryConfig</span> repositoryConfig<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span>repositoryConfig<span class="token punctuation">.</span><span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">RepositoryConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token class-name">AccountRepository</span> <span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultRepositoryConfig</span> <span class="token keyword">implements</span> <span class="token class-name">RepositoryConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">AccountRepository</span> <span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">JdbcAccountRepository</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Import</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ServiceConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">DefaultRepositoryConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>  <span class="token comment">// import the concrete config!</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SystemTestConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// return DataSource</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">SystemTestConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">TransferService</span> transferService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">TransferService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	transferService<span class="token punctuation">.</span><span class="token function">transfer</span><span class="token punctuation">(</span><span class="token number">100.00</span><span class="token punctuation">,</span> <span class="token string">"A123"</span><span class="token punctuation">,</span> <span class="token string">"C456"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 <code>ServiceConfig</code>는 구체적인(concrete) <code>DefaultRepositoryConfig</code>와 관련하여 느슨하게 결합되어 있으며 내장된 IDE 도구는 여전히 유용합니다. <code>RepositoryConfig</code> 구현의 타입 계층 구조를 쉽게 얻을 수 있습니다. 이러한 방식으로 <code>@Configuration</code> 클래스와 해당 종속성을 탐색하는 것은 인터페이스 기반 코드를 탐색하는 일반적인 프로세스와 다르지 않습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
특정 빈의 시작 생성 순서에 영향을 미치고 싶다면 그 중 일부를 <code>@Lazy</code>(시작 시가 아닌 첫 번째 액세스 시 생성) 또는 <code>@DependsOn</code> 을 특정 다른 빈에 대해 선언하는 것을 고려하세요 (후자의 직접적인 종속성이 의미하는 것 이상으로 현재 Bean 이전에 특정 다른 Bean이 생성되었는지 확인합니다.)</p>
</blockquote>
<h1 id="conditionally-include-configuration-classes-or-bean-methods">Conditionally Include <code>@Configuration</code> Classes or <code>@Bean</code> Methods</h1>
<p>임의의 시스템 상태에 따라 전체 <code>@Configuration</code> 클래스 또는 개별 <code>@Bean</code> 메소드를 조건부로 활성화하거나 비활성화하는 것이 유용한 경우가 많습니다. 이에 대한 한 가지 일반적인 예는 <code>@Profile</code> 어노테이션을 사용하여 Spring <code>Environment</code>에서 특정 프로필이 활성화된 경우에만 Bean을 활성화하는 것입니다(자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/environment.html#beans-definition-profiles">Bean 정의 프로필</a> 참조).</p>
<p><code>@Profile</code> 어노테이션은 실제로 <code>@Conditional</code>이라는 훨씬 더 유연한 어노테이션을 사용하여 구현됩니다. <code>@Conditional</code> 어노테이션은 <code>@Bean</code>이 등록되기 전에 참조(consulted)해야 하는 특정 <code>org.springframework.context.annotation.Condition</code> 구현을 나타냅니다.</p>
<p><code>Condition</code> 인터페이스의 구현은 <code>true</code> 또는 <code>false</code>를 반환하는 <code>match(…​)</code> 메서드를 제공합니다. 예를 들어 다음 목록은 <code>@Profile</code>에 사용되는 실제 <code>Condition</code> 구현을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Override</span>
<span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">matches</span><span class="token punctuation">(</span><span class="token class-name">ConditionContext</span> context<span class="token punctuation">,</span> <span class="token class-name">AnnotatedTypeMetadata</span> metadata<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Read the @Profile annotation attributes</span>
	<span class="token class-name">MultiValueMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">&gt;</span></span> attrs <span class="token operator">=</span> metadata<span class="token punctuation">.</span><span class="token function">getAllAnnotationAttributes</span><span class="token punctuation">(</span><span class="token class-name">Profile</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">if</span> <span class="token punctuation">(</span>attrs <span class="token operator">!=</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token class-name">Object</span> value <span class="token operator">:</span> attrs<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"value"</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">if</span> <span class="token punctuation">(</span>context<span class="token punctuation">.</span><span class="token function">getEnvironment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">acceptsProfiles</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">)</span> value<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
				<span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span>
		<span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	<span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>자세한 내용은 <code>@Conditional</code> javadoc을 참조하세요.</p>
<h1 id="combining-java-and-xml-configuration">Combining Java and XML Configuration</h1>
<p>Spring의 <code>@Configuration</code> 클래스 지원은 Spring XML을 100% 완전히 대체하는 것을 목표로 하지 않습니다. Spring XML 네임스페이스와 같은 일부 기능은 컨테이너를 구성하는 이상적인 방법으로 남아 있습니다. XML이 편리하거나 필요한 경우에는 <code>ClassPathXmlApplicationContext</code> 등을 사용하여 "XML 중심" 방식으로 컨테이너를 인스턴스화하거나 <code>AnnotationConfigApplicationContext</code> 및 필요에 따라 XML을 가져오는 <code>@ImportResource</code> 어노테이션을 사용할 수 있습니다.</p>
<h2 id="xml-centric-use-of-configuration-classes">XML-centric Use of <code>@Configuration</code> Classes</h2>
<p>XML에서 Spring 컨테이너를 부트스트랩하고 임시(ad-hoc) 방식으로 <code>@Configuration</code> 클래스를 포함하는 것이 더 나을 수 있습니다. 예를 들어 Spring XML을 사용하는 대규모 기존 코드베이스에서는 필요에 따라 <code>@Configuration</code> 클래스를 생성하고 기존 XML 파일로부터 이를 포함하는 것이 더 쉽습니다. 이 섹션의 뒷부분에서는 이러한 "XML 중심" 상황에서 <code>@Configuration</code> 클래스를 사용하기 위한 옵션을 다룹니다.</p>
<hr>
<p><strong>애드혹(ad-hoc)</strong><br>
프로그래밍에서 "ad-hoc"은 보통 특정한 목적을 위해, 어떤 일반적인 패턴에 따르지 않고, 특정한 상황에 맞추어, 특별히 설계된 코드나 솔루션을 가리키는 용어로 사용됩니다. </p>
<p>예를 들어, 함수형 프로그래밍에서 "ad-hoc 다형성(ad-hoc polymorphism)"은 오버로딩(overloading)이나 오버라이딩(overriding)과 같은 정적 다형성(static polymorphism)과는 달리, 다양한 입력에 따라 동적으로 다양한 형태로 작동할 수 있는 함수나 연산자를 가리킵니다. </p>
<p>또 다른 예로는 데이터 처리나 분석을 위한 ad-hoc 쿼리라고 할 수 있습니다. 이는 특정한 데이터 집합에 대해 한 번성으로 실행되고, 그 결과를 특정한 목적에 맞게 가공하거나 분석하는 쿼리를 의미합니다. 이러한 쿼리는 보통 일회성이며 특정 분석이나 작업을 위해 작성되므로 "ad-hoc"이라는 용어를 사용하여 구분할 수 있습니다.</p>
<hr>
<p><em><code>@Configuration</code> 클래스를 일반 Spring <code>&lt;bean/&gt;</code> 요소로 선언</em></p>
<p><code>@Configuration</code> 클래스는 궁극적으로 컨테이너의 Bean 정의라는 점을 기억하세요. 이 시리즈 예제에서는 <code>AppConfig</code>라는 <code>@Configuration</code> 클래스를 생성하고 이를 <code>&lt;bean/&gt;</code> 정의로 <code>system-test-config.xml</code> 내에 포함합니다. <code>&lt;context:annotation-config/&gt;</code>가 켜져 있기(switched on) 때문에 컨테이너는 <code>@Configuration</code> 어노테이션을 인식하고 <code>AppConfig</code>에 선언된 <code>@Bean</code> 메서드를 올바르게 처리합니다.</p>
<p>다음 예에서는 Java의 일반 구성 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">DataSource</span> dataSource<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">AccountRepository</span> <span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">JdbcAccountRepository</span><span class="token punctuation">(</span>dataSource<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferService</span><span class="token punctuation">(</span><span class="token function">accountRepository</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 샘플 <code>system-test-config.xml</code> 파일의 일부를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- enable processing of annotations such as @Autowired and @Configuration --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:/com/acme/jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme.AppConfig<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.jdbc.datasource.DriverManagerDataSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 예에서는 가능한 <code>jdbc.properties</code> 파일을 보여줍니다.</p>
<pre><code class="language-null">jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=</code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"classpath:/com/acme/system-test-config.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">TransferService</span> transferService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">TransferService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>system-test-config.xml</code> 파일에서 <code>AppConfig</code> <code>&lt;bean/&gt;</code>은 <code>id</code> 요소를 선언하지 않습니다. 그렇게 하는 것이 허용될 수 있지만, 다른 Bean이 이를 참조하지 않고, 이름으로 컨테이너에서 명시적으로 가져오지 않는다는 점을 고려하면 불필요합니다. 마찬가지로 <code>DataSource</code> 빈은 유형별로만 자동 연결되므로 명시적인 빈 <code>id</code>가 꼭 필요한 것은 아닙니다.</p>
</blockquote>
<p><em><code>@Configuration</code> 클래스를 선택하기 위해 <code>&lt;context:comComponent-scan/&gt;</code> 사용하기</em></p>
<p><code>@Configuration</code>은 <code>@Component</code>로 메타 어노테이션을 달기 때문에 <code>@Configuration</code> 어노테이션이 달린 클래스는 자동으로 컴포넌트 스캐닝의 후보가 됩니다. 이전 예에서 설명한 것과 동일한 시나리오를 사용하여 <code>system-test-config.xml</code>을 재정의하여 컴포넌트 스캔을 활용할 수 있습니다. 이 경우 <code>&lt;context:comComponent-scan/&gt;</code>이 동일한 기능을 활성화하므로 <code>&lt;context:annotation-config/&gt;</code>를 명시적으로 선언할 필요가 없습니다.</p>
<p>다음 예에서는 수정된 <code>system-test-config.xml</code> 파일을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- picks up and registers AppConfig as a bean definition --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:/com/acme/jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.jdbc.datasource.DriverManagerDataSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="configuration-class-centric-use-of-xml-with-importresource"><code>@Configuration</code> Class-centric Use of XML with <code>@ImportResource</code></h2>
<p><code>@Configuration</code> 클래스가 컨테이너 구성을 위한 기본(primary) 메커니즘인 애플리케이션에서는 여전히 최소한 일부 XML을 사용해야 할 가능성이 높습니다. 이러한 시나리오에서는 <code>@ImportResource</code>를 사용하고 필요한 만큼만 XML을 정의할 수 있습니다. 이렇게 하면 컨테이너 구성에 대한 "Java 중심" 접근 방식이 달성되고 XML이 최소한으로 유지됩니다. 다음 예제(구성 클래스, Bean을 정의하는 XML 파일, 속성(properties) 파일 및 <code>main</code> 클래스 포함)는 필요에 따라 XML을 사용하는 "Java 중심" 구성을 달성하기 위해 <code>@ImportResource</code> 어노테이션을 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ImportResource</span><span class="token punctuation">(</span><span class="token string">"classpath:/com/acme/properties-config.xml"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${jdbc.url}"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> url<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${jdbc.username}"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> username<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"${jdbc.password}"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">String</span> password<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">DriverManagerDataSource</span><span class="token punctuation">(</span>url<span class="token punctuation">,</span> username<span class="token punctuation">,</span> password<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-xml">properties-config.xml
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:/com/acme/jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<pre><code class="language-null">jdbc.properties
jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
jdbc.username=sa
jdbc.password=</code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">AppConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">TransferService</span> transferService <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">TransferService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>