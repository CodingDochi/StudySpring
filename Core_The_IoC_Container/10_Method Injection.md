<p>대부분의 애플리케이션 시나리오에서 컨테이너에 있는 대부분의 Bean은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-singleton">싱글톤</a>입니다. 싱글톤 Bean이 다른 싱글톤 Bean과 협력해야 하거나 싱글톤이 아닌 Bean이 다른 싱글톤이 아닌 Bean과 협력해야 하는 경우 일반적으로 하나의 Bean을 다른 Bean의 속성(property)으로 정의하여 종속성을 처리합니다. Bean 수명주기가 다르면 문제가 발생합니다. 싱글톤 Bean A가, A에 대한 각 메소드 호출에서 싱글톤이 아닌(프로토타입) Bean B를 사용해야 한다고 가정합니다. 컨테이너는 싱글톤 Bean A를 한 번만 생성하므로 속성(property)을 설정할 수 있는 기회는 한 번만 얻습니다. 컨테이너는 필요할 때마다 Bean B의 새 인스턴스를 Bean A에 제공할 수 없습니다.</p>
<p>해결책은 제어의 역전을 포기하는 것입니다. <code>ApplicationContextAware</code> 인터페이스를 구현하고 <a href="https://docs.spring.io/spring-framework/reference/core/beans/basics.html#beans-factory-client">컨테이너에 대한 <code>getBean("B")</code> 호출을 만들어서</a>, Bean A의 필요에 따라 (일반적으로 새로운) Bean B 인스턴스를 요청함으로써 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-aware">Bean A가 컨테이너를 인식하도록 할 수 있습니다</a>. 다음 예에서는 이 접근 방식을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">fiona<span class="token punctuation">.</span>apple</span><span class="token punctuation">;</span>

<span class="token comment">// Spring-API imports</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans</span><span class="token punctuation">.</span><span class="token class-name">BeansException</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ApplicationContextAware</span><span class="token punctuation">;</span>

<span class="token comment">/**
 * A class that uses a stateful Command-style class to perform
 * some processing.
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CommandManager</span> <span class="token keyword">implements</span> <span class="token class-name">ApplicationContextAware</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">ApplicationContext</span> applicationContext<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">process</span><span class="token punctuation">(</span><span class="token class-name">Map</span> commandState<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// grab a new instance of the appropriate Command</span>
		<span class="token class-name">Command</span> command <span class="token operator">=</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// set the state on the (hopefully brand new) Command instance</span>
		command<span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span>commandState<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> command<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">protected</span> <span class="token class-name">Command</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// notice the Spring API dependency!</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>applicationContext<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token string">"command"</span><span class="token punctuation">,</span> <span class="token class-name">Command</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setApplicationContext</span><span class="token punctuation">(</span>
			<span class="token class-name">ApplicationContext</span> applicationContext<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">BeansException</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>applicationContext <span class="token operator">=</span> applicationContext<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>비즈니스 코드가 Spring Framework를 인식하고 이에 결합(coupled)되므로 앞의 내용(the preceding)은 바람직하지 않습니다. Spring IoC 컨테이너의 다소 고급 기능인 메서드 주입을 사용하면 이 use case 를 깔끔하게 처리할 수 있습니다.</p>
<blockquote>
<p><a href="https://spring.io/blog/2004/08/06/method-injection/">이 블로그</a>에서 method injection 사용 동기에 대해 더 읽을 수 있습니다. </p>
</blockquote>
<h1 id="lookup-method-injection">Lookup Method Injection</h1>
<p>조회(Lookup) 메소드 주입은 컨테이너가 컨테이너 관리 하에 있는 빈들의 메서드를 재정의(override)하고 컨테이너 내부의 다른 명명된 빈(another named bean)들에 대한 조회(lookup) 결과를 반환하는 기능입니다.</p>
<p>검색(Lookup)에는 일반적으로 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-method-injection.html#">이전 섹션</a>에 설명된 시나리오와 같이 프로토타입 Bean이 포함됩니다. Spring Framework는 메서드를 재정의하는 하위 클래스를 동적으로 생성하기 위해, CGLIB 라이브러리에서 생성하는 바이트코드를 사용하여, 이 메서드 주입을 구현합니다.</p>
<blockquote>
<p><strong>[Note]</strong></p>
<ul>
<li>
<p>이 동적 서브클래싱이 작동하려면 Spring Bean 컨테이너가 서브클래싱하는 클래스가 <code>final</code>일 수 없고,재정의할 메서드도 <code>final</code>일 수 없습니다.</p>
</li>
<li>
<p><code>abstract</code> 메서드가 있는 클래스를 단위 테스트하려면 클래스를 직접 하위 클래스화하고 <code>abstract</code> 메서드의 스텁 구현을 제공해야 합니다.</p>
</li>
<li>
<p>선택할 구체(concrete) 클래스를 필요로 하는 컴포넌트(component) scanning에도 구체(concrete) 메서드가 필요합니다.</p>
</li>
</ul>
<p>또 다른 주요 제한 사항은 조회(lookup) 메소드가 팩토리 메소드, 특히 구성(configuration) 클래스의 <code>@Bean</code> 메소드와 작동하지 않는다는 점입니다. 그러므로, 이 경우 컨테이너는 인스턴스 생성을 담당하지 않으며, 즉시(on the fly) 런타임-생성 하위클래스를 생성할 수 없습니다. </p>
</blockquote>
<p>이전 코드 조각의 <code>CommandManager</code> 클래스의 경우, Spring 컨테이너는 <code>createCommand()</code> 메서드의 구현을 동적으로 재정의합니다. 재작업된 예제에서 볼 수 있듯이 <code>CommandManager</code> 클래스는 Spring 종속성을 가지지 않습니다. </p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">fiona<span class="token punctuation">.</span>apple</span><span class="token punctuation">;</span>

<span class="token comment">// no more Spring imports!</span>

<span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">CommandManager</span> <span class="token punctuation">{</span>

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
<p>주입할 메서드가 포함된 클라이언트 클래스(이 경우 <code>CommandManager</code>)에서 주입할 메서드에는 다음 형식의 시그니처(signature)가 필요합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>public|protected</span><span class="token punctuation">&gt;</span></span> [abstract] <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>return-type</span><span class="token punctuation">&gt;</span></span> theMethodName(no-arguments);</code></pre>
<p>메소드가 <code>abstract</code>이면 동적으로 생성된 서브클래스가 메소드를 구현합니다. 그렇지 않으면 동적으로 생성된 하위 클래스가 원본 클래스에 정의된 구체(concrete) 메서드를 재정의합니다. 다음 예를 고려하십시오.</p>
<pre><code class="language-xml"><span class="token comment">&lt;!-- a stateful bean deployed as a prototype (non-singleton) --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myCommand<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>fiona.apple.AsyncCommand<span class="token punctuation">"</span></span> <span class="token attr-name">scope</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>prototype<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- inject dependencies here as required --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token comment">&lt;!-- commandProcessor uses statefulCommandHelper --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>commandManager<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>fiona.apple.CommandManager<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>lookup-method</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createCommand<span class="token punctuation">"</span></span> <span class="token attr-name">bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myCommand<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>commandManager</code>로 식별된 Bean은 <code>myCommand</code> Bean의 새 인스턴스가 필요할 때마다 자체 <code>createCommand()</code> 메소드를 호출합니다. 실제로 필요한 것이 <code>myCommand</code>라면 <code>myCommand</code> 빈을 프로토타입으로 배포할 때 주의해야 합니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-singleton">싱글톤</a>인 경우 <code>myCommand</code> 빈의 동일한 인스턴스가 매번 반환됩니다.</p>
<p>또는 다음 예제와 같이 어노테이션 기반 컴포넌트(componenet) 모델 내에서 <code>@Lookup</code> 어노테이션을 통해 조회 메서드를 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">CommandManager</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">process</span><span class="token punctuation">(</span><span class="token class-name">Object</span> commandState<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Command</span> command <span class="token operator">=</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		command<span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span>commandState<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> command<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Lookup</span><span class="token punctuation">(</span><span class="token string">"myCommand"</span><span class="token punctuation">)</span>
	<span class="token keyword">protected</span> <span class="token keyword">abstract</span> <span class="token class-name">Command</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>또는 더 관용적(idiomatically)으로, 선언된 조회(lookup) 메소드의 반환 유형에 대해 resolve되는 대상 Bean에 의존할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">CommandManager</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">process</span><span class="token punctuation">(</span><span class="token class-name">Object</span> commandState<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Command</span> command <span class="token operator">=</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		command<span class="token punctuation">.</span><span class="token function">setState</span><span class="token punctuation">(</span>commandState<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> command<span class="token punctuation">.</span><span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Lookup</span>
	<span class="token keyword">protected</span> <span class="token keyword">abstract</span> <span class="token class-name">Command</span> <span class="token function">createCommand</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>추상 클래스가 기본적으로 무시되는 Spring의 componenet scanning 규칙과 호환되도록, 일반적으로 구체(concrete) 스텁 구현을 사용하여 어노테이션이 달린 조회 메서드를 선언해야 합니다. 이 제한사항은 명시적으로 등록(registered)되거나 명시적으로 가져온(imported) Bean 클래스에는 적용되지 않습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
범위가 다른 대상 Bean에 액세스하는 또 다른 방법은 <code>ObjectFactory</code>/<code>Provider</code> 주입 지점입니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other-injection">종속성으로서 범위가 지정된 Bean</a>을 참조하세요.</p>
<p><code>ServiceLocatorFactoryBean</code>(<code>org.springframework.beans.factory.config</code> 패키지에 있음)이 유용할 수도 있습니다.</p>
</blockquote>
<h1 id="arbitrary-method-replacement">Arbitrary Method Replacement</h1>
<p>조회(lookup) 메소드 주입보다 덜 유용한 메소드 주입 형태는, 관리되고 있는 Bean에서 임의의 메소드를 다른 메소드 구현으로 대체(replace)하는 기능입니다. 실제로 이 기능이 필요할 때까지는 이 섹션의 나머지 부분을 건너뛰어도 됩니다.</p>
<p>XML 기반 구성 메타데이터를 사용하면 <code>replacement-method</code> 요소를 사용하여 배포된 Bean의 기존 메서드 구현을 다른 메서드 구현으로 바꿀 수 있습니다. 우리가 재정의하려는 <code>computeValue</code>라는 메서드가 있는 다음 클래스를 고려하세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyValueCalculator</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">computeValue</span><span class="token punctuation">(</span><span class="token class-name">String</span> input<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// some real code...</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// some other methods...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>org.springframework.beans.factory.support.MethodReplacer</code> 인터페이스를 구현하는 클래스는 다음 예제와 같이 새로운 메소드 정의를 제공합니다.</p>
<pre><code class="language-java"><span class="token comment">/**
 * meant to be used to override the existing computeValue(String)
 * implementation in MyValueCalculator
 */</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ReplacementComputeValue</span> <span class="token keyword">implements</span> <span class="token class-name">MethodReplacer</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">reimplement</span><span class="token punctuation">(</span><span class="token class-name">Object</span> o<span class="token punctuation">,</span> <span class="token class-name">Method</span> m<span class="token punctuation">,</span> <span class="token class-name">Object</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
		<span class="token comment">// get the input value, work with it, and return a computed result</span>
		<span class="token class-name">String</span> input <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">)</span> args<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
		<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
		<span class="token keyword">return</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>원래 클래스를 배포하고 메소드 재정의를 지정하는 Bean 정의는 다음 예제와 유사(resemble)합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myValueCalculator<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.z.MyValueCalculator<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- arbitrary method replacement --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>replaced-method</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>computeValue<span class="token punctuation">"</span></span> <span class="token attr-name">replacer</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>replacementComputeValue<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>arg-type</span><span class="token punctuation">&gt;</span></span>String<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>arg-type</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>replaced-method</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>replacementComputeValue<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>a.b.c.ReplacementComputeValue<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p><code>&lt;replaced-method/&gt;</code> 요소 내에서 하나 이상의 <code>&lt;arg-type/&gt;</code> 요소를 사용하여 재정의되는(being overriden) 메서드의 메서드 시그니처(signature)를 가리킬 수 있습니다. 인수에 대한 시그니처는 메서드가 오버로드되고 클래스 내에 여러 변형(variants)이 존재하는 경우에만 필요합니다. 편의상 인수의 유형 문자열은 정규화된 유형 이름의 하위 문자열일 수 있습니다. 예를 들어, 다음은 모두 <code>java.lang.String</code>과 일치합니다.</p>
<pre><code class="language-java">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span><span class="token class-name">String</span>
<span class="token class-name">String</span>
<span class="token class-name">Str</span></code></pre>
<p>인수 수는 가능한 각 선택 항목을 구별하기에 충분할 경우가 많기 때문에 이 단축키를 사용하면 인수 유형과 일치하는 가장 짧은 문자열만 입력할 수 있으므로 입력 시간을 많이 줄일 수 있습니다.</p>