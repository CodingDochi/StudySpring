<p><code>@Configuration</code>은 객체가 Bean 정의의 소스임을 나타내는 클래스 수준 어노테이션입니다. <code>@Configuration</code> 클래스는 <code>@Bean</code> 어노테이션이 달린 메소드를 통해 Bean을 선언합니다. <code>@Configuration</code> 클래스의 <code>@Bean</code> 메소드에 대한 호출은 Bean 간 종속성을 정의하는 데 사용될 수도 있습니다. 일반적인 소개는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/java/basic-concepts.html">기본 개념: <code>@Bean</code> 및 <code>@Configuration</code></a>을 참조하세요.</p>
<h1 id="injecting-inter-bean-dependenncies">Injecting inter-bean Dependenncies</h1>
<p>Bean이 서로 종속성을 갖는 경우 해당 종속성을 표현하는 것은 다음 예제와 같이 하나의 Bean 메소드가 다른 Bean 메소드를 호출하는 것만큼 간단합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">BeanOne</span> <span class="token function">beanOne</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">BeanOne</span><span class="token punctuation">(</span><span class="token function">beanTwo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">BeanTwo</span> <span class="token function">beanTwo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">BeanTwo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예제에서 <code>beanOne</code>은 생성자 주입을 통해 <code>beanTwo</code>에 대한 참조를 받습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
bean 간 종속성을 선언하는 이 방법은 <code>@Bean</code> 메서드가 <code>@Configuration</code> 클래스 내에서 선언된 경우에만 작동합니다. 일반 <code>@Component</code> 클래스를 사용하여 Bean 간 종속성을 선언할 수 없습니다.</p>
</blockquote>
<h1 id="lookup-method-injection">Lookup Method Injection</h1>
<p>앞서 언급했듯이 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-method-injection.html">lookup 메서드 주입</a>은 거의 사용하지 않는 고급 기능입니다. 이는 싱글톤 범위의 Bean이 프로토타입 범위의 Bean에 종속되는 경우에 유용합니다. 이러한 타입의 구성에 Java를 사용하면 이 패턴을 구현하기 위한 자연스러운 수단이 제공됩니다. 다음 예에서는 조회 메서드 주입을 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">CommandManager</span> <span class="token punctuation">{</span>
	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">process</span><span class="token punctuation">(</span><span class="token class-name">Object</span> commandState<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// grab a new instance of the appropriate Command interface</span>
		<span class="token class-name">Command</span> command <span class="token operator">=</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// set the state on the (hopefully brand new) Command instance</span>
		command<span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span>commandState<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> command<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// okay... but where is the implementation of this method?</span>
	<span class="token keyword">protected</span> <span class="token keyword">abstract</span> <span class="token class-name">Command</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Java 구성(configuration)을 사용하면 추상 <code>createCommand()</code> 메서드가 새(프로토타입) 명령 객체를 찾는 방식으로 재정의되는 <code>CommandManager</code>의 하위 클래스를 만들 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token annotation punctuation">@Scope</span><span class="token punctuation">(</span><span class="token string">"prototype"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">AsyncCommand</span> <span class="token function">asyncCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AsyncCommand</span> command <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AsyncCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// inject dependencies here as required</span>
	<span class="token keyword">return</span> command<span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">CommandManager</span> <span class="token function">commandManager</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// return new anonymous implementation of CommandManager with createCommand()</span>
	<span class="token comment">// overridden to return a new prototype Command object</span>
	<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">CommandManager</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">protected</span> <span class="token class-name">Command</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">return</span> <span class="token function">asyncCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="further-information-about-how-java-based-configuration-works-internally">Further Information About How java-based Configuration Works Internally</h1>
<p><code>@Bean</code> 어노테이션이 달린 메서드가 두 번 호출되는 것을 보여주는 다음 예제를 고려해보세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">ClientService</span> <span class="token function">clientService1</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ClientServiceImpl</span> clientService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClientServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		clientService<span class="token punctuation">.</span><span class="token function">setClientDao</span><span class="token punctuation">(</span><span class="token function">clientDao</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> clientService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">ClientService</span> <span class="token function">clientService2</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ClientServiceImpl</span> clientService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClientServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		clientService<span class="token punctuation">.</span><span class="token function">setClientDao</span><span class="token punctuation">(</span><span class="token function">clientDao</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> clientService<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">ClientDao</span> <span class="token function">clientDao</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">ClientDaoImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>clientDao()</code>는 <code>clientService1()</code>에서 한 번, <code>clientService2()</code>에서 한 번 호출되었습니다. 이 메소드는 <code>ClientDaoImpl</code>의 새 인스턴스를 생성하고 이를 반환하므로 일반적으로 두 개의 인스턴스(각 서비스당 하나씩)가 있을 것으로 예상합니다. 그것은 분명히 문제가 될 것(problematic)입니다. Spring에서 인스턴스화된 Bean은 기본적으로 <code>singleton</code> 범위를 갖습니다. 이것이 마법이 일어나는 곳입니다. 모든 <code>@Configuration</code> 클래스는 시작 시 <code>CGLIB</code>를 사용하여 하위 클래스로 분류됩니다. 서브클래스에서 하위 메소드는 상위 메소드를 호출하고 새 인스턴스를 생성하기 전에 먼저 컨테이너에서 캐시된(범위가 지정된) Bean을 확인합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Bean의 범위에 따라 동작이 다를 수 있습니다. 여기서는 싱글톤에 대해 이야기하고 있습니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
CGLIB 클래스는 <code>org.springframework.cglib</code> 패키지 아래에 다시 패키지되고 s<code>pring-core</code> JAR 내에 직접 포함되므로 클래스패스에 CGLIB를 추가할 필요가 없습니다.</p>
</blockquote>
<blockquote>
<p><strong>[Tip]</strong><br>
CGLIB가 시작 시 기능을 동적으로 추가한다는 사실로 인해 몇 가지 제한 사항이 있습니다. 특히 구성 클래스는 <code>final</code> 클래스가 아니어야 합니다. 그러나 <code>@Autowired</code> 사용이나 기본 주입을 위한 non-default 단일 생성자 선언을 포함하여 구성 클래스에서는 모든 생성자가 허용됩니다.</p>
<p>CGLIB에 의해 부과된 제한을 피하려면 <code>@Configuration</code>이 아닌 클래스(예: 일반 <code>@Component</code> 클래스)에서 <code>@Bean</code> 메서드를 선언하거나 구성 클래스에 <code>@Configuration(proxyBeanMethods = false)</code> 어노테이션을 추가하는 것을 고려하세요. <code>@Bean</code> 메서드 간의 메서드 간 호출은 가로채지 않으므로 생성자 또는 메서드 수준에서 종속성 주입에만 의존해야 합니다.</p>
</blockquote>