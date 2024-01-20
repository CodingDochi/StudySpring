<p>DI(종속성 주입)는 객체가 자신의 종속성(즉, 작업하는 다른 객체)을 정의하는 프로세스입니다. 오로지 생성자 인수, 팩토리 메서드에 대한 인수 또는 생성된 후 객체 인스턴스에 설정된 속성, 팩토리 메서드에서 반환된 값을 통해서만 객체의 종속성을 정의하는 프로세스입니다. </p>
<p>그런 다음 컨테이너는 Bean을 생성할 때 해당 종속성을 주입합니다. 이 프로세스는 근본적으로 클래스 또는 서비스 로케이터 패턴의 생성자를 직접 사용하여 Bean이 가진 종속성의 인스턴스화 또는 위치를 제어하는 Bean 자체의 역(따라서 이름, 제어 역전)입니다.</p>
<p>DI 원칙을 사용하면 코드가 더 깔끔해지며, 객체에 종속성이 제공될 때 분리(decoupling)가 더 효과적입니다. 개체는 종속성을 조회하지 않으며 종속성의 위치나 클래스를 알지 못합니다. </p>
<p>결과적으로, 특히 단위 테스트에서 스텁 또는 모의 구현을 허용하는 추상 베이스 클래스나 인터페이스 위에 종속성이 있는 경우, 클래스를 테스트하기가 더 쉬워집니다.</p>
<p>DI는 생성자 기반 종속성 주입과 Setter 기반 종속성 주입이라는 두 가지 주요 변형으로 존재합니다.</p>
<h1 id="constructor-based-dependency-injection">Constructor-based Dependency Injection</h1>
<p>생성자 기반 DI는 각각 종속성을 나타내는 여러 인수를 사용하여 생성자를 호출하는 컨테이너에 의해 수행됩니다. Bean을 구성하기 위해 특정 인수를 사용하여 <code>static</code> 팩토리 메소드를 호출하는 것은 거의 동등하며, 이 토론에서는 생성자에 제공되는 인수와 <code>static</code> 팩토리 메소드에 대한 인수를 유사하게 처리합니다. 다음 예제에서는 생성자 주입을 통해서만 종속성 주입이 가능한 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token comment">// the SimpleMovieLister has a dependency on a MovieFinder</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token comment">// a constructor so that the Spring container can inject a MovieFinder</span>
	<span class="token keyword">public</span> <span class="token class-name">SimpleMovieLister</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// business logic that actually uses the injected MovieFinder is omitted...</span>
<span class="token punctuation">}</span></code></pre>
<p>이 클래스에는 특별한 것이 없습니다. 이는 컨테이너별 인터페이스, 베이스 클래스 또는 어노테이션에 대한 종속성이 없는 POJO입니다.</p>
<h1 id="constructor-argument-resolution">Constructor Argument Resolution</h1>
<p>생성자 인수 resolution matching는 인수 type을 사용하여 발생합니다. Bean 정의의 생성자 인수에 잠재적인 모호성(ambiguity)이 없으면 Bean 정의에서 생성자 인수가 정의되는 순서는 Bean이 인스턴스화될 때 해당 인수가 적절한 생성자에 제공되는 순서입니다. 다음 클래스를 고려해보세요:</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">x<span class="token punctuation">.</span>y</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ThingOne</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">ThingOne</span><span class="token punctuation">(</span><span class="token class-name">ThingTwo</span> thingTwo<span class="token punctuation">,</span> <span class="token class-name">ThingThree</span> thingThree<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ThingTwo</code> 및 <code>ThingThree</code> 클래스가 상속으로 관련되어 있지 않다고 가정하면, 잠재적인 모호성은 존재하지 않습니다. 따라서 다음 구성은 제대로 작동하며 <code>&lt;constructor-arg/&gt;</code> 요소에 생성자 인수 인덱스 또는 유형을 명시적으로 지정할 필요가 없습니다.</p>
<pre><code class="language-XML"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanOne<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.ThingOne<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanTwo<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanThree<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanTwo<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.ThingTwo<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanThree<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>x.y.ThingThree<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다른 Bean이 참조되면 type이 알려지고 matching이 발생할 수 있습니다(이전 예제의 경우와 마찬가지로). <code>&lt;value&gt;true&lt;/value&gt;</code>와 같은 단순 유형이 사용되면 Spring은 값의 유형을 결정할 수 없으므로 도움 없이 type을 match시킬 수 없습니다. 다음 클래스를 고려해보세요:</p>
<pre><code class="language-java"><span class="token keyword">package</span> examples<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token comment">// Number of years to calculate the Ultimate Answer</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token keyword">int</span> years<span class="token punctuation">;</span>

	<span class="token comment">// The Answer to Life, the Universe, and Everything</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> ultimateAnswer<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">ExampleBean</span><span class="token punctuation">(</span><span class="token keyword">int</span> years<span class="token punctuation">,</span> <span class="token class-name">String</span> ultimateAnswer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>years <span class="token operator">=</span> years<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>ultimateAnswer <span class="token operator">=</span> ultimateAnswer<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 시나리오에서 다음 예제와 같이 <code>type</code> 속성(attribute)을 사용하여 생성자 인수의 type을 명시적으로 지정하는 경우 컨테이너는 단순 type과 matching하는 형식을 사용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>int<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>7500000<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>java.lang.String<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>42<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 예제와 같이 <code>index</code> 속성(attribute)을 사용하여 생성자 인수의 인덱스를 명시적으로 지정할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">index</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>0<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>7500000<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">index</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>1<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>42<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>여러 단순 값의 모호성을 해결하는 것 외에도 인덱스를 지정하면 생성자가 동일한 type의 인수를 두 개 이상 가질 때의 모호성을 해결합니다.</p>
<blockquote>
<p><strong>[Note]</strong> 인덱스는 0부터 시작합니다.</p>
</blockquote>
<p>생성자 인수 이름</p>
<p>다음 예제와 같이 값 명확성(disambiguation)을 위해 생성자 매개 변수 이름을 사용할 수도 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>years<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>7500000<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>ultimateAnswer<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>42<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 작업을 즉시 수행하려면 Spring이 생성자에서 매개변수 이름을 찾을 수 있도록 디버그 플래그를 활성화하여 코드를 컴파일해야 합니다. 디버그 플래그를 사용하여 코드를 컴파일할 수 없거나 컴파일하고 싶지 않은 경우 <a href="https://download.oracle.com/javase/8/docs/api/java/beans/ConstructorProperties.html">@ConstructorProperties</a> JDK annotation을 사용하여 생성자 인수의 이름을 명시적으로 지정할 수 있습니다. 그러면 샘플 클래스는 다음과 같아야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> examples<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token comment">// Fields omitted</span>

	<span class="token annotation punctuation">@ConstructorProperties</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token string">"years"</span><span class="token punctuation">,</span> <span class="token string">"ultimateAnswer"</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">ExampleBean</span><span class="token punctuation">(</span><span class="token keyword">int</span> years<span class="token punctuation">,</span> <span class="token class-name">String</span> ultimateAnswer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>years <span class="token operator">=</span> years<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>ultimateAnswer <span class="token operator">=</span> ultimateAnswer<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="setter-based-dependency-injection">Setter-based Dependency Injection</h1>
<p>Setter 기반 DI는 빈을 인스턴스화하기 위해 인수 없는 생성자 또는 인수 없는 <code>static</code> 팩토리 메소드를 호출한 후 빈에서 setter 메소드를 호출하는 컨테이너에 의해 수행됩니다.</p>
<p>다음 예제에서는 순수 setter 주입을 통해서만 종속성 주입이 가능한 클래스를 보여줍니다. 이 클래스는 일반적인 Java입니다. 이는 컨테이너별 인터페이스, 베이스 클래스 또는 어노테이션에 대한 종속성이 없는 POJO입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token comment">// the SimpleMovieLister has a dependency on the MovieFinder</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token comment">// a setter method so that the Spring container can inject a MovieFinder</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// business logic that actually uses the injected MovieFinder is omitted...</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ApplicationContext</code>는 관리하는 Bean에 대해 생성자 기반 및 setter 기반 DI를 지원합니다. 또한 생성자 접근 방식을 통해 일부 종속성이 이미 주입된 후의 경우에도 setter 기반 DI를 지원합니다. 속성(property)을 한 형식에서 다른 형식으로 변환하기 위해 <code>PropertyEditor</code> 인스턴스와 함께 사용하는(use in conjunction with) <code>BeanDefinition</code> 형식으로 종속성을 구성합니다. 그러나 대부분의 Spring 사용자는 이러한 클래스를 직접(즉, 프로그래밍 방식으로) 사용하지 않고 XML <code>bean</code> 정의, annotated 구성 요소(component)(즉, <code>@Component</code>, <code>@Controller</code> 등으로 어노테이션이 달린 클래스) 또는 Java 기반 <code>@Configuration</code> 클래스에서 <code>@Bean</code> 메서드를 사용하여 작업합니다. 그런 다음 이러한 소스는 내부적으로 <code>BeanDefinition</code>의 인스턴스로 변환되고 전체 Spring IoC 컨테이너 인스턴스를 로드하는 데 사용됩니다.</p>
<blockquote>
<h3 id="생성자constructor-기반-또는-setter-기반-di">생성자(constructor) 기반 또는 setter 기반 DI?</h3>
<p>생성자 기반 DI와 setter 기반 DI를 혼합할 수 있으므로 필수 종속성에는 생성자를 사용하고 선택적 종속성에는 setter 메서드 또는 구성(configuration) 메서드를 사용하는 것이 좋습니다. setter 메소드에 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html">@Autowired</a> 어노테이션을 사용하면 해당 속성을 필수 종속성(required dependency)으로 만들 수 있습니다. 그러나 프로그래밍 방식으로 인수를 검증하는 생성자 주입이 더 좋습니다.</p>
<p>Spring 팀은 일반적으로 생성자 주입을 주창(advocate)합니다. 이를 통해 애플리케이션 구성 요소를 불변 객체(immutable objects)로 구현하고 필요한 종속성이 <code>null</code>이 아니란 것을 확인할 수 있기 때문입니다. 또한 생성자 주입 컴포넌트(component)는 항상 완전히 초기화된 상태로 클라이언트(호출, calling) 코드에 반환됩니다. 참고로, 생성자 인수가 너무 많으면 클래스에 책임이 너무 많을 가능성이 높으며, 문제를 적절하게 분리하기 위해 리팩터링해야 함을 의미합니다.</p>
<p>Setter 주입은 주로 클래스 내에서 합리적인 기본값을 할당할 수 있는 선택적 종속성에만 사용해야 합니다. 그렇지 않으면 코드가 종속성을 사용하는 모든 곳에서 not-null 검사를 수행해야 합니다. setter 주입의 한 가지 이점은 setter 메서드가 해당 클래스의 객체를 나중에 재구성(reconfiguration)하거나 다시 주입(reinjection)할 수 있도록 한다는 것입니다. 따라서 <a href="https://docs.spring.io/spring-framework/reference/integration/jmx.html">JMX MBean</a>을 통한 관리는 setter 주입에 대한 설득력 있는 사용 사례입니다.</p>
<p>특정 클래스에 가장 적합한 DI 스타일을 사용하십시오. 때로는 소스가 없는 타사 클래스를 처리할 때 선택이 자동으로 이루어집니다. 예를 들어, 타사 클래스가 setter 메서드를 노출하지 않는 경우 생성자 주입은 유일하게 사용 가능한 DI 형태일 수 있습니다.</p>
</blockquote>
<h1 id="dependency-resolution-process">Dependency Resolution Process</h1>
<hr>
<p><img src="https://i.stack.imgur.com/gL8Ky.jpg"><img src="https://flycoolman.com/coding/java/spring-beans/lifecycle-detailed-1.png"><img src="https://flycoolman.com/coding/java/spring-beans/lifecycle-detailed-2.png"><img src="https://howtodoinjava.com/wp-content/uploads/2023/10/Ioc-vs-DI-in-Spring.png"><img src="https://media.licdn.com/dms/image/C4E12AQECsGVVnRN2cw/article-cover_image-shrink_720_1280/0/1599319495950?e=2147483647&amp;v=beta&amp;t=L_W0dND194FmDnCySUuN-GLnjZwdirjZGqj8kskL2Dg"><img src="https://www.baeldung.com/wp-content/uploads/2023/02/singleton_design_pattern3.png"><img src="https://miro.medium.com/v2/resize:fit:720/format:webp/1*8jMFASYbJpkdGtrNDA-hsw.png"></p>
<p>컨테이너는 다음과 같이 Bean 종속성 해결(resolution)을 수행합니다.</p>
<ul>
<li>
<p><code>ApplicationContext</code>는 모든 Bean을 설명하는 구성 메타데이터로 생성되고 초기화됩니다. 구성 메타데이터는 XML, Java 코드 또는 어노테이션으로 지정할 수 있습니다.</p>
</li>
<li>
<p>각 Bean에 대해 해당 종속성은 속성(properties), 생성자 인수 또는 정적 팩토리 메서드에 대한 인수(일반 생성자 대신 이를 사용하는 경우) 형식으로 표현됩니다. 이러한 종속성은 Bean이 실제로 생성될 때 Bean에 제공됩니다.</p>
</li>
<li>
<p>각 속성(property)이나 생성자 인수는 설정(set)할 값의 실제 정의이거나 컨테이너의 다른 Bean에 대한 참조(reference)입니다.</p>
</li>
<li>
<p>값인 각 속성 또는 생성자 인수는 지정된 형식에서 해당 속성 또는 생성자 인수의 실제 형식으로 변환됩니다. 기본적으로 Spring은 String 형식으로 제공된 값을 <code>int</code>, <code>long</code>, <code>String</code>, <code>boolean</code> 등과 같은 모든 내장 유형(built-in type)으로 변환할 수 있습니다.</p>
</li>
</ul>
<p>Spring 컨테이너는 컨테이너가 생성될 때 각 Bean의 구성(configuration)을 검증합니다. 그러나 Bean 속성(property) 자체는 Bean이 실제로 생성될 때까지 설정되지 않습니다. 싱글톤 범위이고 사전-인스턴스화(pre-instantiated)(기본값 the default)되도록 설정된 Bean은 컨테이너가 생성될 때 생성됩니다. 범위는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html">Bean 범위</a>에 정의됩니다. 그렇지 않으면 요청될 때만 Bean이 생성됩니다. Bean을 생성하면 Bean의 종속성과 해당 종속성의 종속성(등)이 생성되고 할당되므로 잠재적으로 Bean의 그래프가 생성됩니다. 이러한 종속성 간의 resolution mismatch는 늦게, 즉 영향을 받은 Bean을 처음 생성할 때 나타날 수 있습니다.</p>
<blockquote>
<h3 id="순환-종속성">순환 종속성</h3>
<p>주로(predominantly) 생성자 주입을 사용하는 경우 해결할 수 없는 순환 종속성 시나리오를 생성할 수 있습니다.</p>
<p>예를 들어 클래스 A에는 생성자 주입을 통해 클래스 B의 인스턴스가 필요하고, 클래스 B에는 생성자 주입을 통해 클래스 A의 인스턴스가 필요합니다. 클래스 A와 B가 서로 주입되도록 빈을 구성하면 Spring IoC 컨테이너는 런타임에 이 순환 참조를 감지하고 <code>BeanCurrentlyInCreationException</code>을 발생시킵니다.</p>
<p>한 가지 가능한 해결책은 생성자가 아닌 setter로 구성(configure)되도록 일부 클래스의 소스 코드를 편집하는 것입니다. 또는 생성자 주입을 피하고 setter 주입만 사용하십시오. 즉, 권장되지는 않지만 setter 주입을 통해 순환 종속성을 구성할 수 있습니다.</p>
<p>순환 종속성이 없는 일반적인 경우와 달리 Bean A와 Bean B 사이의 순환 종속성은 완전히 초기화되기 전에 Bean 중 하나가 다른 Bean에 주입되도록 강제합니다(전통적인 닭고기와 달걀 시나리오).</p>
</blockquote>
<p>일반적으로 Spring이 올바른 일을 수행한다고 신뢰할 수 있습니다. 컨테이너 로드 시 존재하지 않는 Bean에 대한 참조 및 순환 종속성과 같은 구성(configuration) 문제를 감지합니다. Spring은 가능한 한 늦게(=빈이 실제로 생성될 때) 속성(property)을 설정(set)하고 종속성(dependency)을 해결(resolve)합니다. 이는 올바르게 로드된 Spring 컨테이너가 나중에 당신이 객체를 요청했을때, 그 객체 또는 그것의 종속성을 생성할 때 문제가 있는 경우 예외를 발생시킬 수도 있다는 뜻입니다. 예를 들어 빈은 누락되거나 유효하지 않은 속성(property)에 대한 결과로 예외를 발생시킵니다. </p>
<hr>
<p><img src="https://media.stacktips.com/media/uploads/summernote/singleton-design-pattern_1_6HU1DL5.jpg"></p>
<hr>
<p>일부 구성 문제에 대해, 잠재적으로 지연된 가시성(visibility)이 <code>ApplicationContext</code> 구현이 기본적으로 싱글톤 bean을 사전 인스턴스화하는 이유입니다. 실제로 필요하기 전에 이러한 Bean을 생성하는 데 약간의 선행 시간과 메모리를 희생하면, (나중이 아니라) ApplicationContext가 생성될 때 구성(configuration) 문제를 발견하게 됩니다. 싱글톤 bean이 즉시(eagerly) 사전 인스턴스화되는 대신 느리게(lazily) 초기화되도록 이 기본 동작을 재정의(override)할 수 있습니다.</p>
<p>순환 종속성이 존재하지 않는 경우 하나 이상의 협력(collaborating) bean이 종속 bean에 주입되면 각 협력 Bean은 종속 Bean에 주입되기 전에 온전히 구성(totally configured)됩니다. 이는 Bean A가 Bean B에 종속성을 갖는 경우 Spring IoC 컨테이너가 Bean A에서 setter 메소드를 호출하기 전에 Bean B를 완전히 구성한다는 것을 의미합니다. 즉, Bean은 인스턴스화됩니다(사전 인스턴스화된 싱글톤이 아닌 경우). 그것의 해당 종속성이 설정(set)되고 관련 lifecycle 메서드(예: <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean">구성된 init 메서드</a> 또는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean">InitializingBean 콜백 메서드</a>)가 호출됩니다.</p>
<h1 id="examples-of-dependency-injection">Examples of Dependency Injection</h1>
<p>다음 예에서는 setter 기반 DI에 XML 기반 구성 메타데이터를 사용합니다. Spring XML 구성 파일의 작은 부분은 다음과 같이 일부 Bean 정의를 지정합니다</p>
<pre><code class="language-XML"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- setter injection using the nested ref element --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanOne<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>ref</span> <span class="token attr-name">bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- setter injection using the neater ref attribute --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beanTwo<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>integerProperty<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>1<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.AnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.YetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예제에서는 해당하는 <code>ExampleBean</code> 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">AnotherBean</span> beanOne<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">YetAnotherBean</span> beanTwo<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">int</span> i<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBeanOne</span><span class="token punctuation">(</span><span class="token class-name">AnotherBean</span> beanOne<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>beanOne <span class="token operator">=</span> beanOne<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBeanTwo</span><span class="token punctuation">(</span><span class="token class-name">YetAnotherBean</span> beanTwo<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>beanTwo <span class="token operator">=</span> beanTwo<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setIntegerProperty</span><span class="token punctuation">(</span><span class="token keyword">int</span> i<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>i <span class="token operator">=</span> i<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 setter는 XML 파일에 지정된 속성(property)과 일치하도록 선언되었습니다. 다음 예에서는 생성자 기반 DI를 사용합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- constructor injection using the nested ref element --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>ref</span> <span class="token attr-name">bean</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>constructor-arg</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- constructor injection using the neater ref attribute --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>int<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>1<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.AnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.YetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예제에서는 해당하는 <code>ExampleBean</code> 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">AnotherBean</span> beanOne<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">YetAnotherBean</span> beanTwo<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">int</span> i<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">ExampleBean</span><span class="token punctuation">(</span>
		<span class="token class-name">AnotherBean</span> anotherBean<span class="token punctuation">,</span> <span class="token class-name">YetAnotherBean</span> yetAnotherBean<span class="token punctuation">,</span> <span class="token keyword">int</span> i<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>beanOne <span class="token operator">=</span> anotherBean<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>beanTwo <span class="token operator">=</span> yetAnotherBean<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>i <span class="token operator">=</span> i<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>Bean 정의에 지정된 생성자 인수는 <code>ExampleBean</code> 생성자에 대한 인수로 사용됩니다.</p>
<p>객체의 인스턴스를 반환하기 위해, 이제 Spring에게 생성자를 사용하는 대신 <code>static</code> 팩토리 메서드를 호출하도록 지시하는 이 예제의 변형을 고려해 보세요.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">factory-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>createInstance<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>constructor-arg</span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>1<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>anotherExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.AnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>yetAnotherBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.YetAnotherBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>다음 예제에서는 해당하는 <code>ExampleBean</code> 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token comment">// a private constructor</span>
	<span class="token keyword">private</span> <span class="token class-name">ExampleBean</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// a static factory method; the arguments to this method can be</span>
	<span class="token comment">// considered the dependencies of the bean that is returned,</span>
	<span class="token comment">// regardless of how those arguments are actually used.</span>
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token class-name">ExampleBean</span> createInstance <span class="token punctuation">(</span>
		<span class="token class-name">AnotherBean</span> anotherBean<span class="token punctuation">,</span> <span class="token class-name">YetAnotherBean</span> yetAnotherBean<span class="token punctuation">,</span> <span class="token keyword">int</span> i<span class="token punctuation">)</span> <span class="token punctuation">{</span>

		<span class="token class-name">ExampleBean</span> eb <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// some other operations...</span>
		<span class="token keyword">return</span> eb<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>static</code> 팩토리 메소드에 대한 인수는 <code>&lt;constructor-arg/&gt;</code> 요소에 의해 제공(supplied)되며, 이는 생성자가 실제로 사용된 것과 정확히 동일합니다. 팩토리 메서드에서 반환되는 클래스의 타입은 <code>static</code> 팩터리 메서드를 포함하는 클래스와 동일한 타입일 필요는 없습니다(단, 이 예에서는 동일함). 인스턴스(non-static) 팩토리 메소드는 본질적으로 동일한 방식(fashion)으로 사용될 수 있으므로(<code>class</code> 속성(attribute) 대신 <code>factory-bean</code> 속성(attribute)을 사용하는 것을 제외하고(aside from)) 여기서는 자세한 내용을 논의하지 않습니다.</p>