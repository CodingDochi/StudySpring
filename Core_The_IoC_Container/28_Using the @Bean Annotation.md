<p><code>@Bean</code>은 메소드 수준 어노테이션이며 XML <code>&lt;bean/&gt;</code> 요소와 직접적으로 유사합니다. 어노테이션은 다음과 같이 <code>&lt;bean/&gt;</code>에서 제공하는 일부 속성(attribute)을 지원합니다.</p>
<ul>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean">init-method</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-disposablebean">destroy-method</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html">autowiring</a></li>
<li><code>name</code>.</li>
</ul>
<p><code>@Configuration</code>-annotated 또는 <code>@Component</code>-annotated 클래스에서 <code>@Bean</code> 어노테이션을 사용할 수 있습니다.</p>
<h1 id="declaring-a-bean">Declaring a Bean</h1>
<p>Bean을 선언하려면 <code>@Bean</code> 어노테이션을 사용하여 메서드에 어노테이션을 달 수 있습니다. 이 메소드를 사용하여 메소드의 반환 값으로 지정된 타입의 <code>ApplicationContext</code> 내에 빈 정의를 등록합니다. 기본적으로 Bean 이름은 메소드 이름과 동일합니다. 다음 예제에서는 <code>@Bean</code> 메서드 선언을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferServiceImpl</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 구성은 다음 Spring XML과 정확히 동일합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>transferService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme.TransferServiceImpl<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>두 선언 모두 다음 텍스트 이미지에 표시된 것처럼 <code>TransferServiceImpl</code> 타입의 객체 인스턴스에 바인딩되어 <code>ApplicationContext</code>에서 사용 가능한 <code>transferService</code>라는 빈을 만듭니다.</p>
<pre><code class="language-null">transferService -&gt; com.acme.TransferServiceImpl</code></pre>
<p>default 메소드를 사용하여 Bean을 정의할 수도 있습니다. 이를 통해 default 메소드에 Bean 정의가 포함된 인터페이스를 구현하여 Bean 구성(configuration)을 구성(compose)할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">BaseConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">default</span> <span class="token class-name">TransferServiceImpl</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token keyword">implements</span> <span class="token class-name">BaseConfig</span> <span class="token punctuation">{</span>

<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 인터페이스(또는 기본 클래스) 반환 유형을 사용하여 <code>@Bean</code> 메서드를 선언할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그러나 이는 고급 type prediction의 가시성을 지정된 인터페이스 타입(<code>TransferService</code>)으로 제한합니다. 그런 다음 영향을 받은 싱글톤 Bean이 인스턴스화되면 컨테이너에 전체 타입(<code>TransferServiceImpl</code>)이 알려집니다. Non-lazy 싱글톤 Bean은 선언 순서에 따라 인스턴스화 됩니다. 따라서 다른 구성 요소가 선언되지 않은 유형(예: <code>transferService</code> 빈이 인스턴스화된 후에만 resolve되는 <code>@Autowired TransferServiceImpl</code>)으로 일치(match)를 시도하는 경우에 따라 다른 타입 match 결과가 표시될 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
선언된 서비스 인터페이스로 타입을 일관되게 참조하는 경우 <code>@Bean</code> 반환 타입이 해당 디자인 결정에 안전하게 참여할 수 있습니다. 그러나 여러 인터페이스를 구현하는 컴포넌트 또는 구현 타입에 의해 잠재적으로 참조되는 컴포넌트의 경우, 가능한 가장 구체적인 반환 타입을 선언하는 것이 더 안전합니다(적어도 Bean을 참조하는 주입 지점에서 요구하는 만큼 구체적).</p>
</blockquote>
<h1 id="bean-dependencies">Bean Dependencies</h1>
<p><code>@Bean</code> 어노테이션이 달린 메소드에는 해당 Bean을 빌드하는 데 필요한 종속성을 설명하는 임의의 수의 매개변수가 있을 수 있습니다. 예를 들어 <code>TransferService</code>에 <code>AccountRepository</code>가 필요한 경우 다음 예제와 같이 메서드 매개 변수를 사용하여 해당 종속성을 구체화(materialize)할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">TransferService</span> <span class="token function">transferService</span><span class="token punctuation">(</span><span class="token class-name">AccountRepository</span> accountRepository<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TransferServiceImpl</span><span class="token punctuation">(</span>accountRepository<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>resolution 메커니즘은 생성자 기반 종속성 주입과 거의 동일합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html#beans-constructor-injection">관련 섹션</a>을 참조하세요.</p>
<h1 id="receiving-lifecycle-callbacks">Receiving Lifecycle Callbacks</h1>
<p><code>@Bean</code> 어노테이션으로 정의된 모든 클래스는 일반 수명 주기 콜백을 지원하고 JSR-250의 <code>@PostConstruct</code> 및 <code>@PreDestroy</code> 어노테이션을 사용할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html">JSR-250 어노테이션</a>을 참조하세요.</p>
<p>일반 Spring <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html">라이프사이클</a> 콜백도 완벽하게 지원됩니다. Bean이 <code>InitializingBean</code>, <code>DisposableBean</code> 또는 <code>Lifecycle</code>을 구현하는 경우 해당 메소드는 컨테이너에 의해 호출됩니다.</p>
<p><code>*Aware</code> 인터페이스(예: <a href="https://docs.spring.io/spring-framework/reference/core/beans/beanfactory.html">BeanFactoryAware</a>, <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-aware">BeanNameAware</a>, <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-functionality-messagesource">MessageSourceAware</a>, <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-aware">ApplicationContextAware</a> 등)의 표준 세트도 완벽하게 지원됩니다.</p>
<p><code>@Bean</code> 어노테이션은 다음 예제와 같이 Bean 요소에 대한 Spring XML의 <code>init-method</code> 및 <code>destroy-method</code> 속성(attribute)과 매우 유사한 임의의 초기화 및 소멸 콜백 메소드 지정을 지원합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BeanOne</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// initialization logic</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BeanTwo</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">cleanup</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// destruction logic</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span>initMethod <span class="token operator">=</span> <span class="token string">"init"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">BeanOne</span> <span class="token function">beanOne</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">BeanOne</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span>destroyMethod <span class="token operator">=</span> <span class="token string">"cleanup"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">BeanTwo</span> <span class="token function">beanTwo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">BeanTwo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
기본적으로 public <code>close</code> 또는 <code>shutdown</code> 메소드가 있는 Java 구성으로 정의된 Bean은 자동으로 소멸 콜백에 등록(enlisted)됩니다. public <code>close</code> 또는 <code>shutdown</code> 메소드가 있고, 컨테이너가 종료될 때 호출되는 것을 원하지 않는 경우 Bean 정의에 <code>@Bean(destroyMethod = "")</code>를 추가하여 기본<code>(inferred)</code> 모드를 비활성화할 수 있습니다.</p>
<p>JNDI로 획득한 리소스의 수명주기가 애플리케이션 외부에서 관리되므로, JNDI로 획득한 리소스에 대해 기본적으로 이를 수행할 수 있습니다. 특히, Jakarta EE 애플리케이션 서버에서는 문제가 있는 것으로 알려져 있으므로 항상 <code>DataSource</code>에 대해 수행해야 합니다.</p>
<p>다음 예에서는 <code>DataSource</code>에 대한 자동 소멸 콜백을 방지하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span>destroyMethod <span class="token operator">=</span> <span class="token string">""</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">NamingException</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token class-name">DataSource</span><span class="token punctuation">)</span> jndiTemplate<span class="token punctuation">.</span><span class="token function">lookup</span><span class="token punctuation">(</span><span class="token string">"MyDS"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>또한 <code>@Bean</code> 메소드를 사용하면 일반적으로 Spring의 <code>JndiTemplate</code> 또는 <code>JndiLocatorDelegate</code> 도우미를 사용하거나 JNDI <code>InitialContext</code>을 사용하여 프로그래밍 방식의 JNDI 조회(look up)를 사용하지만 <code>JndiObjectFactoryBean</code> 변형은 사용하지 않습니다(이 경우 실제 대상 타입 대신 <code>FactoryBean</code> 타입으로 반환 타입 선언을 강제하며, 이로 인해 여기에 제공된 리소스를 참조하려는 다른 <code>@Bean</code> 메서드의 상호 참조 호출에 사용하기가 더 어려워집니다).</p>
</blockquote>
<p>이전 참고 사항 위의 예제에서 <code>BeanOne</code>의 경우 다음 예제와 같이 생성 중에 직접 <code>init()</code> 메서드를 호출하는 것도 동일하게 유효합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">BeanOne</span> <span class="token function">beanOne</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">BeanOne</span> beanOne <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">BeanOne</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		beanOne<span class="token punctuation">.</span><span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> beanOne<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
Java에서 직접 작업할 때 객체를 사용하여 원하는 모든 작업을 수행할 수 있으며 항상 컨테이너 수명 주기에 의존할 필요는 없습니다.</p>
</blockquote>
<h1 id="spedifying-bean-scope">Spedifying Bean Scope</h1>
<p>Spring에는 Bean의 범위를 지정할 수 있도록 <code>@Scope</code> 어노테이션이 포함되어 있습니다.</p>
<h2 id="using-the-scope-annotation">Using the <code>@Scope</code> Annotation</h2>
<p><code>@Bean</code> 어노테이션으로 정의된 Bean이 특정 범위를 갖도록 지정할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html">Bean 범위 섹션</a>에 지정된 표준 범위를 사용할 수 있습니다.</p>
<p>기본 범위는 <code>singleton</code>이지만 다음 예제와 같이 <code>@Scope</code> 어노테이션으로 이를 재정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyConfiguration</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@Scope</span><span class="token punctuation">(</span><span class="token string">"prototype"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Encryptor</span> <span class="token function">encryptor</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="scope-and-scoped-proxy"><code>@Scope</code> and <code>scoped-proxy</code></h2>
<p>Spring은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other-injection">범위가 지정된 프록시(scoped-proxy)</a>를 통해 범위가 지정된 종속성을 작업하는 편리한 방법을 제공합니다. XML 구성을 사용할 때 이러한 프록시를 생성하는 가장 쉬운 방법은 <code>&lt;aop:scoped-proxy/&gt;</code> 요소입니다. <code>@Scope</code> 어노테이션을 사용하여 Java에서 Bean을 구성하면 <code>proxyMode</code> 속성(attribute)과 동등한 지원이 제공됩니다. 기본값은 <code>ScopedProxyMode.DEFAULT</code>입니다. 이는 일반적으로 컴포넌트-스캔 명령 수준에서 다른 기본값(default)이 구성되지 않은 한 범위가 지정된 프록시를 생성해서는 안 됨을 나타냅니다. <code>ScopedProxyMode.TARGET_CLASS</code>, <code>ScopedProxyMode.INTERFACES</code> 또는 <code>ScopedProxyMode.NO</code>를 지정할 수 있습니다.</p>
<p>Java를 사용하여 XML 참조 문서(<a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other-injection">범위가 지정된 프록시</a> 참조)의 범위가 지정된 프록시 예제를 <code>@Bean</code>으로 포팅하는 경우 다음과 유사합니다.</p>
<pre><code class="language-java"><span class="token comment">// an HTTP Session-scoped bean exposed as a proxy</span>
<span class="token annotation punctuation">@Bean</span>
<span class="token annotation punctuation">@SessionScope</span>
<span class="token keyword">public</span> <span class="token class-name">UserPreferences</span> <span class="token function">userPreferences</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">UserPreferences</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">Service</span> <span class="token function">userService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">UserService</span> service <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleUserService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// a reference to the proxied userPreferences bean</span>
	service<span class="token punctuation">.</span><span class="token function">setUserPreferences</span><span class="token punctuation">(</span><span class="token function">userPreferences</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> service<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="customizing-bean-naming">Customizing Bean Naming</h1>
<p>기본적으로 구성 클래스는 <code>@Bean</code> 메소드 이름을 결과 Bean의 이름으로 사용합니다. 그러나 이 기능은 다음 예제와 같이 <code>name</code> 속성(attribute)으로 재정의될 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span><span class="token string">"myThing"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Thing</span> <span class="token function">thing</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Thing</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="bean-aliasing">Bean Aliasing</h1>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-beanname">Bean 이름 지정</a>에서 설명한 것처럼 단일 Bean에 여러 이름을 지정하는 것이 바람직할 때도 있습니다. 이는 Bean 별명 지정이라고도 알려져 있습니다. <code>@Bean</code> 어노테이션의 <code>name</code> 속성(attribute)은 이 목적을 위해 String 배열을 허용합니다. 다음 예에서는 Bean에 대한 여러 별칭을 설정하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token string">"dataSource"</span><span class="token punctuation">,</span> <span class="token string">"subsystemA-dataSource"</span><span class="token punctuation">,</span> <span class="token string">"subsystemB-dataSource"</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// instantiate, configure and return DataSource bean...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="bean-description">Bean Description</h1>
<p>때로는 Bean에 대한 보다 자세한 텍스트 설명을 제공하는 것이 도움이 될 수 있습니다. 이는 모니터링 목적으로 Bean이 노출될 때(아마도 JMX를 통해) 특히 유용할 수 있습니다.</p>
<p><code>@Bean</code>에 설명을 추가하려면 다음 예제와 같이 <code>@Description</code> 어노테이션을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@Description</span><span class="token punctuation">(</span><span class="token string">"Provides a basic example of a bean"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Thing</span> <span class="token function">thing</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Thing</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>