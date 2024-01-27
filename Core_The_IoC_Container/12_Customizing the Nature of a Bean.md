<p>Spring Framework는 Bean의 특성(nature)을 사용자 정의하는 데 사용할 수 있는 다양한 인터페이스를 제공합니다. 이 섹션에서는 다음과 같이 그룹화합니다. </p>
<ul>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle">Lifecycle Callbacks</a></li>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-aware"><code>ApplicationContextAware</code> 및 <code>BeanNameAware</code></a> </li>
<li><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#aware-list">기타 <code>Aware</code> 인터페이스</a></li>
</ul>
<h1 id="lifecycle-callbacks">Lifecycle Callbacks</h1>
<p>컨테이너의 bean lifecycle 관리와 상호작용하기 위해서 Spring <code>InitializingBean</code> 및 <code>DisposableBean</code> 인터페이스를 구현합니다. 컨테이너는 전자의 경우 <code>afterPropertiesSet()</code>을 호출하고 후자의 경우 <code>destroy()</code>를 호출하여 Bean의 초기화(initialization) 및 파기(destruction) 시 Bean이 특정 작업을 수행하도록 합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
JSR-250 <code>@PostConstruct</code> 및 <code>@PreDestroy</code> 어노테이션은 일반적으로 최신 Spring 애플리케이션에서 lifecycle 콜백을 수신하기 위한 모범 사례로 간주됩니다. 이러한 어노테이션을 사용한다는 것은 당신의 Bean이 Spring 특정 인터페이스에 결합되어 있지 않음(not coupled)을 의미합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html"><code>@PostConstruct</code> 및 <code>@PreDestroy</code> 사용</a>을 참조하세요.</p>
<p>JSR-250 어노테이션을 사용하고 싶지 않지만 여전히 결합을 제거하려는 경우 <code>init-method</code> 및 <code>destroy-method</code> Bean 정의 메타데이터를 고려하십시오.</p>
</blockquote>
<hr>
<p><strong>자바 어노테이션(java annotation)</strong></p>
<p>자바 어노테이션(Annotation)은 JDK 5.0 (자바 5)부터 도입되었습니다. 이것은 2004년에 출시되었습니다. 어노테이션은 코드에 메타데이터를 추가하여 컴파일러, 런타임 및 다양한 개발 도구가 코드를 분석하고 처리할 수 있도록 지원합니다.</p>
<p>어노테이션은 일반적으로 컴파일러에게 특정 작업을 수행하도록 지시하거나, 런타임 시에 동적으로 코드를 분석하고 처리하는 데 사용됩니다. 예를 들어, 자바의 내장된 어노테이션 중 하나인 <code>@Override</code>는 메서드가 슈퍼클래스의 메서드를 오버라이드하고 있는지 확인하기 위해 컴파일러에게 알려줍니다.</p>
<p>어노테이션 프로세서(Annotation Processor)를 사용하면 컴파일 시에 어노테이션을 처리하고 추가적인 코드 생성이나 검증을 수행할 수 있습니다. 또한, 리플렉션(Reflection)을 사용하여 런타임 시에 어노테이션을 분석하고 해당 정보를 활용할 수 있습니다.</p>
<p><strong>어노테이션 특성</strong></p>
<ul>
<li>
<p>어노테이션은 <code>@</code> 기호로 시작하며, 클래스, 메서드, 변수 등에 적용될 수 있으며, 여러 개의 속성을 가질 수 있습니다.</p>
</li>
<li>
<p>커스텀 어노테이션을 만들려면 다음과 같은 단계를 따르면 됩니다:</p>
<ol>
<li><code>@interface</code> 키워드 사용: 어노테이션을 정의하기 위해 <code>@interface</code> 키워드를 사용합니다.</li>
<li>요소 정의: 어노테이션에 속성을 추가하고, 이 속성에 대한 기본값을 설정할 수 있습니다.</li>
<li><code>@Retention</code> 및 <code>@Target</code> 어노테이션 사용: 어노테이션의 보존 정책과 적용 대상을 지정합니다.</li>
</ol>
</li>
<li>
<p>커스텀 어노테이션 작성 예시</p>
</li>
</ul>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">java<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>METHOD<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">MyAnnotation</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token string">"Default Value"</span><span class="token punctuation">;</span>
    <span class="token keyword">int</span> <span class="token function">number</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token number">0</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<ul>
<li>커스텀 어노테이션 적용 예시</li>
</ul>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyClass</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@MyAnnotation</span><span class="token punctuation">(</span>value <span class="token operator">=</span> <span class="token string">"Custom Value"</span><span class="token punctuation">,</span> number <span class="token operator">=</span> <span class="token number">42</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">myMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token comment">// 메서드 내용</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><strong>어노테이션 프로세서(Annotation Processor)</strong></p>
<ul>
<li>
<p>코드 생성(Code Generation): 어노테이션 프로세서를 사용하여 소스 코드에 특정 어노테이션을 적용하면, 이를 기반으로 추가적인 코드를 생성할 수 있습니다. 예를 들어, 롬복(Lombok)과 같은 라이브러리는 어노테이션 프로세서를 사용하여 게터(Getter) 및 세터(Setter)와 같은 메서드를 자동으로 생성합니다.</p>
</li>
<li>
<p>검증(Validation): 어노테이션을 사용하여 소스 코드에 메타데이터를 추가할 수 있습니다. 이 메타데이터는 코드의 일관성을 검증하고 문제를 식별하는 데 사용될 수 있습니다. </p>
</li>
<li>
<p>설정(Configuration): 어노테이션 프로세서를 사용하여 특정 어노테이션이 발견되면, 해당 어노테이션에 따라 동작을 구성하거나 설정을 변경할 수 있습니다.</p>
</li>
</ul>
<p><strong>컨트랙트(contract)</strong>:</p>
<ul>
<li>
<p>컨트랙트는 소프트웨어 개발에서 해당 기능이나 모듈의 동작을 설명하는 형식적인 계약을 나타냅니다. 이것은 메서드나 함수의 사전 조건(precondition), 사후 조건(postcondition), 불변식(invariant) 등을 명시하는 데 사용됩니다.</p>
</li>
<li>
<p>컨트랙트는 주로 설계 문서, 명세서 또는 코드 주석과 같은 형태로 사용되며, 코드의 의도를 명확히 설명하고 오류를 방지하며 코드의 안정성을 향상시키는 데 도움이 됩니다.</p>
</li>
<li>
<p>컨트랙트는 프로그래밍 언어나 특정 기술과 직접적으로 연결되어 있지 않으며, 개발자와 사용자 간의 계약이나 기능의 예상 동작을 설명하는 데 사용됩니다.</p>
</li>
</ul>
<p>요약하면, 어노테이션은 코드에 메타데이터를 추가하는 데 사용되고, 컨트랙트는 코드의 동작을 설명하고 계약을 정의하는 데 사용됩니다.</p>
<p><strong>JSR (Java Specification Requests)</strong><br>
JSR는 Java Specification Requests의 약자로, 자바 엔터프라이즈 에디션(Java EE)와 관련된 기술 표준을 제정하기 위한 프로세스입니다. JSR은 새로운 자바 기술 또는 API를 제안하고 명세를 작성하는 과정을 포함합니다. </p>
<ul>
<li><strong>JSR-250 (Common Annotations for the Java Platform)</strong>: 이 JSR은 자바 플랫폼에 공통으로 사용되는 어노테이션(annotation)을 정의합니다. 그 중에는 <code>@PostConstruct</code>와 <code>@PreDestroy</code>가 있습니다. <code>@PostConstruct</code>는 빈이 생성된 후에 초기화를 수행하는 메서드에 붙이며, <code>@PreDestroy</code>는 빈이 소멸되기 전에 정리를 수행하는 메서드에 붙입니다.</li>
<li><strong>JSR-330 (Dependency Injection for Java)</strong>: 이 JSR은 자바의 의존성 주입(Dependency Injection)을 위한 표준을 제정합니다. 그 중에는 <code>@Inject</code>와 <code>@Qualifier</code> 등이 있습니다. <code>@Inject</code>는 의존성 주입을 지정하는 데 사용되며, <code>@Qualifier</code>는 여러 구현 중에서 어떤 것을 선택할지 지정하는 데 사용됩니다.</li>
</ul>
<hr>
<p>내부적으로 Spring Framework는 <code>BeanPostProcessor</code> 구현을 사용하여 적절한 메소드를 찾아 호출할 수 있는 콜백 인터페이스를 처리합니다. 사용자 정의 기능이나 Spring이 기본적으로 제공하지 않는 다른 lifecycle 동작이 필요한 경우 <code>BeanPostProcessor</code>를 직접 구현할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html">컨테이너 확장 지점</a>을 참조하세요.</p>
<p>초기화(initialization) 및 소멸(destruction) 콜백 외에도 Spring이 관리하는 객체는 컨테이너 자체 lifecycle에 따라 해당 객체가 시작(startup) 및 종료(shutdown) 프로세스에 참여할 수 있도록 <code>Lifecycle</code> 인터페이스를 구현할 수도 있습니다.</p>
<p>이 섹션에서는 lifecycle 콜백 인터페이스에 대해 설명합니다.</p>
<h2 id="initialization-callbacks">Initialization Callbacks</h2>
<p><code>org.springframework.beans.factory.InitializingBean</code> 인터페이스를 사용하면 컨테이너가 빈에 필요한 모든 속성(property)을 설정(set)한 후 빈이 초기화(initialization) 작업을 수행할 수 있습니다. <code>InitializingBean</code> 인터페이스는 단일 메서드를 지정합니다.</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">afterPropertiesSet</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span><span class="token punctuation">;</span></code></pre>
<p>코드를 불필요하게 Spring에 결합(couples)하기 때문에 <code>InitializingBean</code> 인터페이스를 사용하지 않는 것이 좋습니다. 또는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html"><code>@PostConstruct</code></a> 어노테이션을 사용하거나 POJO 초기화 방법을 지정하는 것이 좋습니다. XML 기반 구성 메타데이터의 경우 <code>init-method</code> 속성(attribute)을 사용하여 인수 없는 void 서명이 있는 메서드의 이름을 지정할 수 있습니다. Java 구성을 사용하면 <code>@Bean</code>의 <code>initMethod</code> 속성을 사용할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html#beans-java-lifecycle-callbacks">Lifecylce 콜백 수신</a>을 참조하세요. 다음 예를 고려하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleInitBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">init-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>init<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// do some initialization work</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예는 다음 예(두 개의 목록으로 구성됨)와 거의 동일한 효과를 갖습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleInitBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.AnotherExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AnotherExampleBean</span> <span class="token keyword">implements</span> <span class="token class-name">InitializingBean</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">afterPropertiesSet</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// do some initialization work</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그러나 앞의 두 예제 중 첫 번째는 코드를 Spring에 결합(couple)하지 않습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@PostConstruct</code> 및 초기화 메서드는 일반적으로 컨테이너의 싱글톤 생성 잠금(creation lock) 내에서 실행된다는 점에 유의하세요. bean 인스턴스는 <code>@PostConstruct</code> 메소드에서 반환된 후에 완전히 초기화되고, 다른 것들에 게시할(publish) 준비가 된 것으로 간주됩니다. 이러한 개별 초기화 방법은 구성 상태(configuration state)를 검증(validate)하고 주어진 구성(configuration)을 기반으로 일부 데이터 구조를 준비하는 데만 사용되며 외부 Bean 액세스에 대한 추가 활동은 없습니다. 그렇지 않으면 초기화 교착 상태(deadlock)가 발생할 위험이 있습니다.</p>
<p>비용이 많이 드는 post-initialization이 트리거되는 시나리오의 경우, 예를 들어 비동기 데이터베이스 준비 단계에서 빈은 <code>SmartInitializingSingleton.afterSingletonsInstantiated()</code>를 구현하거나 <code>ApplicationListener&lt;ContextRefreshedEvent&gt;</code>을 구현, 또는 <code>@EventListener(ContextRefreshedEvent.class)</code>과 동등한 어노테이션을 선언함으로써 컨텍스트 새로 고침 이벤트(refresh event)에 의존(rely on)해야 합니다. 이러한 변형은 모든 일반 싱글톤 초기화 이후에 나오므로 싱글톤 생성 잠금 외부에 있습니다.</p>
<p>또는 <code>(Smart)Lifecycle</code> 인터페이스를 구현하여 자동 시작(auto-startup) 메커니즘, 사전 삭제(pre-destroy) 중지 단계 및 잠재적인 중지/다시 시작 콜백(아래 참조)을 포함한, 컨테이너의 전체 lifecycle 관리와 통합(integrate)할 수 있습니다.</p>
</blockquote>
<h2 id="destruction-callbacks">Destruction Callbacks</h2>
<p><code>org.springframework.beans.factory.DisposableBean</code> 인터페이스를 구현하면 빈을 포함하는 컨테이너가 파괴될 때 빈이 콜백을 얻을 수 있습니다. <code>DisposableBean</code> 인터페이스는 단일 메서드를 지정합니다.</p>
<pre><code class="language-java"><span class="token keyword">void</span> <span class="token function">destroy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span><span class="token punctuation">;</span></code></pre>
<p><code>DisposableBean</code> 콜백 인터페이스는 불필요하게 코드를 Spring에 결합(couple)하므로 사용하지 않는 것이 좋습니다. 대안으로 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html"><code>@PreDestroy</code></a> 어노테이션을 사용하거나 Bean 정의에서 지원하는 제네릭 메서드를 지정하는 것이 좋습니다. XML 기반 구성 메타데이터를 사용하면 <code>&lt;bean/&gt;</code>에서 <code>destroy-method</code> 속성(attribute)을 사용할 수 있습니다. Java 구성을 사용하면 <code>@Bean</code>의 <code>destroyMethod</code> 속성을 사용할 수 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/java/bean-annotation.html#beans-java-lifecycle-callbacks">Lifecycle 콜백 수신</a>을 참조하세요. 다음 정의를 고려하십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleDestructionBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.ExampleBean<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>cleanup<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleBean</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">cleanup</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// do some destruction work (like releasing pooled connections)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 정의는 다음 정의와 거의 동일한 효과를 갖습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exampleDestructionBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>examples.AnotherExampleBean<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AnotherExampleBean</span> <span class="token keyword">implements</span> <span class="token class-name">DisposableBean</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">destroy</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// do some destruction work (like releasing pooled connections)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그러나 앞의 두 정의 중 첫 번째 정의는 코드를 Spring에 결합(couple)하지 않습니다.</p>
<p>Spring은 public <code>close</code> 또는 <code>shutdown</code> 메소드를 감지하여 파괴(destroy) 메소드 추론(inference)도 지원합니다. 이는 Java 구성 클래스의 <code>@Bean</code> 메소드에 대한 기본 동작이며 자동으로 <code>java.lang.AutoCloseable</code> 또는 <code>java.io.Closeable</code> 구현과 match하며 파괴(destruction) 로직을 Spring에 결합(couple)하지 않습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
XML을 사용한 destroy 메소드 추론의 경우 <code>&lt;bean&gt;</code> 요소의 <code>destroy-method</code> 속성에 특수<code>(inferred)</code> 값을 할당할 수 있습니다. <code>(infered)</code> 값은 Spring이 특정 Bean 정의에 대한 Bean 클래스의 public <code>close</code> 또는 <code>shutdown</code> 메소드를 자동으로 감지하도록 지시합니다. 또한 이 동작을 전체 Bean 정의 세트에 적용하기 위해 <code>&lt;beans&gt;</code> 요소의 <code>default-destroy-method</code> 속성에 이 특수<code>(inferred)</code> 값을 설정할 수도 있습니다(<a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-default-init-destroy-methods">기본 초기화 및 삭제 방법</a> 참조).</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
확장된 종료 단계의 경우 <code>Lifecycle</code> 인터페이스를 구현하고 싱글톤 Bean의 destroy 메소드가 호출되기 전에 조기 중지 신호를 수신할 수 있습니다. 컨테이너가 메서드 삭제(destroy) 단계로 이동하기 전에 모든 중지 처리가 완료될 때까지 기다리는 시간 제한(time-bound) 중지 단계에 대해 <code>SmartLifecycle</code>을 구현할 수도 있습니다.</p>
</blockquote>
<h2 id="default-initialization-and-destroy-methods">Default Initialization and Destroy Methods</h2>
<p>Spring 고유의 <code>InitializingBean</code> 및 <code>DisposableBean</code> 콜백 인터페이스를 사용하지 않는 초기화 및 삭제 메서드 콜백을 작성할 때 일반적으로 <code>init()</code>, <code>initialize()</code>, <code>dispose()</code> 등과 같은 이름을 가진 메서드를 작성합니다. 이상적으로는 모든 개발자가 동일한 메서드 이름을 사용하고 일관성을 보장할 수 있도록 이러한 수명 주기 콜백 메서드의 이름이 프로젝트 전체에서 표준화됩니다.</p>
<p>명명된 초기화를 "검색"하고 모든 Bean에서 콜백 메서드 이름을 삭제하도록 Spring 컨테이너를 구성할 수 있습니다. 이는 애플리케이션 개발자로서 각 Bean 정의에 <code>init-method="init"</code> 속성(attribute)을 구성할 필요 없이 애플리케이션 클래스를 작성하고 <code>init()</code>라는 초기화 콜백을 사용할 수 있음을 의미합니다. Spring IoC 컨테이너는 Bean이 생성될 때(그리고 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle">이전에 설명한</a> 표준 수명 주기 콜백 계약에 따라) 해당 메서드를 호출합니다. 또한 이 기능은 초기화 및 삭제 메서드 콜백에 대해 일관된 명명 규칙을 적용합니다.</p>
<p>초기화 콜백 메서드의 이름이 <code>init()</code>이고 파괴 콜백 메서드의 이름이 <code>destroy()</code>라고 가정합니다. 그러면 클래스는 다음 예제의 클래스와 유사(resembles)합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultBlogService</span> <span class="token keyword">implements</span> <span class="token class-name">BlogService</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">BlogDao</span> blogDao<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBlogDao</span><span class="token punctuation">(</span><span class="token class-name">BlogDao</span> blogDao<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>blogDao <span class="token operator">=</span> blogDao<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// this is (unsurprisingly) the initialization callback method</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">.</span>blogDao <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token keyword">throw</span> <span class="token keyword">new</span> <span class="token class-name">IllegalStateException</span><span class="token punctuation">(</span><span class="token string">"The [blogDao] property must be set."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그러면 다음과 유사한 Bean에서 해당 클래스를 사용할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">default-init-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>init<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blogService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.DefaultBlogService<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blogDao<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blogDao<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>최상위 <code>&lt;beans/&gt;</code> 요소 속성에 <code>default-init-method</code> 속성(attribute)이 있으면 Spring IoC 컨테이너가 Bean 클래스의 <code>init</code>라는 메서드를 초기화 메서드 콜백으로 인식하게 됩니다. Bean이 생성되고 조립될 때 Bean 클래스에 해당 메소드가 있으면 적절한 시간에 호출됩니다.</p>
<p>최상위 <code>&lt;beans/&gt;</code> 요소의 <code>default-destroy-method</code> 속성(attribute)을 사용하여 유사한 방식으로(즉, XML에서) 소멸 메소드 콜백을 구성할 수 있습니다.</p>
<p>기존 Bean 클래스에 이미 규칙에 따라 이름이 지정된 콜백 메소드가 있는 경우 <code>&lt;bean/&gt;</code> 그 자체의 <code>init-method</code> 및 <code>destroy-method</code> 속성을 사용하여 메소드 이름을 XML로 지정하여 기본값을 대체할 수 있습니다. </p>
<p>Spring 컨테이너는 Bean이 모든 종속성과 함께 제공된(supplied) 직후 구성된(confitured) 초기화 콜백이 호출되도록 보장합니다. 따라서 초기화 콜백은 원시(raw) Bean 참조에서 호출됩니다. 이는 AOP 인터셉터 등이 아직 Bean에 적용되지 않았음을 의미합니다. 먼저 대상(target) Bean이 완전히 생성된 다음(예를 들어) 인터셉터 체인이 포함된 AOP 프록시가 적용됩니다. 대상 Bean과 프록시가 별도로 정의된 경우 코드는 프록시를 우회(bypass)하여 원시 대상 Bean과 상호 작용할 수도 있습니다. 따라서 <code>init</code> 메소드에 인터셉터를 적용하는 것은 일관성이 없습니다. 그렇게 하면 대상 Bean의 라이프사이클이 프록시 또는 인터셉터에 결합되고 코드가 원시 대상 Bean과 직접 상호작용할 때 이상한 의미(sementics)가 남게 되기 때문입니다.</p>
<h2 id="combining-lifecycle-mechanisms">Combining Lifecycle Mechanisms</h2>
<p>Spring 2.5부터 Bean 라이프사이클 동작을 제어하기 위한 세 가지 옵션이 있습니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean"><code>InitializingBean</code></a> 및 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-disposablebean"><code>DisposableBean</code></a> 콜백 인터페이스</p>
</li>
<li>
<p>사용자 정의 <code>init()</code> 및 <code>destroy()</code> 메소드</p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html"><code>@PostConstruct</code> 및 <code>@PreDestroy</code> 어노테이션</a></p>
</li>
</ul>
<p>이러한 메커니즘을 combine 특정 Bean을 제어할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Bean에 대해 여러 라이프사이클 메커니즘이 구성되고 각 메커니즘이 다른 메소드 이름으로 구성된 경우 구성된 각 메소드는 이 참고 사항 뒤에 나열된 순서대로 실행됩니다. 그러나 동일한 메서드 이름이 구성되면(예: 초기화 메서드에 대한 <code>init()</code>) 이러한 수명 주기 메커니즘 중 두 개 이상에 대해 해당 메서드는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-default-init-destroy-methods">이전 섹션에서 설명한 대로</a> 한 번 실행됩니다.</p>
</blockquote>
<p>동일한 Bean에 대해 서로 다른 초기화 방법으로 구성된 여러 수명주기 메커니즘이 다음과 같이 호출됩니다.</p>
<ol>
<li>
<p><code>@PostConstruct</code>로 어노테이션이 달린 메소드</p>
</li>
<li>
<p><code>InitializingBean</code> 콜백 인터페이스에 의해 정의된 <code>afterPropertiesSet()</code></p>
</li>
<li>
<p>사용자 정의 구성된 <code>init()</code> 메소드</p>
</li>
</ol>
<p>Destroy 메소드는 동일한 순서로 호출됩니다.</p>
<ol>
<li>
<p><code>@PreDestroy</code>로 어노테이션이 달린 메소드</p>
</li>
<li>
<p><code>DisposableBean</code> 콜백 인터페이스에 의해 정의된 <code>destroy()</code></p>
</li>
<li>
<p>사용자 정의로 구성된 <code>destroy()</code> 메소드</p>
</li>
</ol>
<h2 id="startup-and-shutdown-callbacks">Startup and Shutdown Callbacks</h2>
<p><code>Lifecycle</code> 인터페이스는 자체 수명 주기 요구 사항(예: 일부 백그라운드 프로세스 시작 및 중지)이 있는 모든 객체에 대한 필수(esseintial) 메서드를 정의합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Lifecycle</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">start</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">void</span> <span class="token function">stop</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">boolean</span> <span class="token function">isRunning</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>Spring이 관리하는 모든 객체는 <code>Lifecycle</code> 인터페이스를 구현할 수 있습니다. 그런 다음 <code>ApplicationContext</code> 자체가 시작 및 중지 신호를 수신하면(예: 런타임 시 중지/다시 시작 시나리오의 경우) 해당 호출을 해당 컨텍스트 내에 정의된 모든 <code>Lifecycle</code> 구현에 계단식으로(cascades) 연결합니다. 다음 목록에 표시된 <code>LifecycleProcessor</code>에 위임하여 이를 수행합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">LifecycleProcessor</span> <span class="token keyword">extends</span> <span class="token class-name">Lifecycle</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">onRefresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">void</span> <span class="token function">onClose</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>LifecycleProcessor</code> 자체는 <code>Lifecycle</code> 인터페이스의 확장(extension)입니다. 또한 새로 고쳐지고 닫히는 컨텍스트에 반응하기 위한 두 가지 다른 메서드도 추가합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
일반(regular) <code>org.springframework.context.Lifecycle</code> 인터페이스는 명시적인 시작 및 중지 알림에 대한 일반(plain) 계약이며 컨텍스트 새로 고침 시 자동 시작을 암시(imply)하지 않습니다. 자동 시작에 대한 세밀한 제어와 (시작 및 중지 단계를 포함하는)특정 Bean의 우아한(graceful) 중지를 위해 대신 확장된(extended) <code>org.springframework.context.SmartLifecycle</code> 인터페이스 구현을 고려하세요.</p>
<p>또한, 중지(stop) 알림이 파기(destruction)되기 전에 오는 것은 보장되지 않습니다. 정기적인(regular) 종료(shutdown) 시 모든 <code>Lifecycle</code> Bean은 일반 소멸(destruction) 콜백이 전파되기 전에 먼저 중지(stop) 알림을 받습니다. 그러나 컨텍스트 수명 동안 핫 새로 고침(hot refresh)이 수행되거나 새로 고침 시도가 중지된 경우에는 destroy 메소드만 호출됩니다.</p>
</blockquote>
<hr>
<p><strong>Hot refresh</strong><br>
"Hot refresh"는 소프트웨어나 애플리케이션을 실행 중인 동안 변경 사항을 적용하기 위해 사용되는 용어입니다. 일반적으로 웹 애플리케이션과 같은 동적 환경에서 많이 사용됩니다.</p>
<p>일반적으로 애플리케이션을 개발하고 테스트하는 동안 코드나 리소스를 변경할 수 있습니다. 이러한 변경 사항은 종종 애플리케이션을 다시 시작하거나 재배포해야 적용되지만, "hot refresh" 기능이 있는 환경에서는 애플리케이션을 중단시키지 않고도 변경 사항을 즉시 적용할 수 있습니다.</p>
<p>Spring Framework에서는 hot refresh를 지원하는데, 이를 통해 개발자가 애플리케이션을 다시 시작하지 않고도 코드의 변경 사항을 즉시 반영할 수 있습니다. 이는 개발 및 디버깅을 더 효율적으로 만들어줍니다. </p>
<p>그러나 문서에서 말하는 것처럼 hot refresh가 발생하는 동안에는 stop notification이 오지 않을 수 있습니다. 이는 일반적인 애플리케이션 종료 프로세스가 아니기 때문입니다. 따라서 이러한 상황에서는 destroy 메서드가 호출되지만, stop notification은 전달되지 않을 수 있습니다.</p>
<hr>
<p>시작 및 종료 호출 순서가 중요할 수 있습니다. 두 객체 사이에 "의존" 관계가 존재하는 경우 종속시키는(dependent) 측은 그것의 종속성 이후에 시작하고 종속성 이전에 중지됩니다. 그러나 때로는 직접적인 종속성을 알 수 없는 경우도 있습니다. 특정 유형의 객체가 다른 유형의 객체보다 먼저 시작되어야 한다는 것만 알 수 있습니다. 이러한 경우 <code>SmartLifecycle</code> 인터페이스는 슈퍼 인터페이스인 <code>Phased</code>에 정의된 <code>getPhase()</code> 메서드라는 또 다른 옵션을 정의합니다. 다음 목록은 <code>Phased</code> 인터페이스의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Phased</span> <span class="token punctuation">{</span>

	<span class="token keyword">int</span> <span class="token function">getPhase</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 목록은 <code>SmartLifecycle</code> 인터페이스의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">SmartLifecycle</span> <span class="token keyword">extends</span> <span class="token class-name">Lifecycle</span><span class="token punctuation">,</span> <span class="token class-name">Phased</span> <span class="token punctuation">{</span>

	<span class="token keyword">boolean</span> <span class="token function">isAutoStartup</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token keyword">void</span> <span class="token function">stop</span><span class="token punctuation">(</span><span class="token class-name">Runnable</span> callback<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>시작할 때 단계가 가장 낮은 개체가 먼저 시작됩니다. 정지할 때는 역순으로 진행됩니다. 따라서 <code>SmartLifecycle</code>을 구현하고 <code>getPhase()</code> 메서드가 <code>Integer.MIN_VALUE</code>를 반환하는 객체는 가장 먼저 시작되고 마지막으로 중지됩니다. 스펙트럼의 반대쪽 끝에서 <code>Integer.MAX_VALUE</code> 단계 값은 개체가 마지막에 시작되고 먼저 중지되어야 함을 나타냅니다(실행 중인 다른 프로세스에 따라 달라지기 때문일 수 있음). 단계 값을 고려할 때 <code>SmartLifecycle</code>을 구현하지 않는 "정상" <code>Lifecycle</code> 객체의 기본 단계(default phase)가 <code>0</code>이라는 것을 아는 것도 중요합니다. 따라서 음수 단계 값은 객체가 해당 표준 구성 요소보다 먼저 시작되어야 하고, 그들 이후 종료 되어야 함을 나타냅니다. 양의 위상 값에 대해서는 그 반대가 적용됩니다.</p>
<p><code>SmartLifecycle</code>에서 정의한 중지(stop) 메서드는 콜백을 허용합니다. 모든 구현은 해당 구현의 종료 프로세스가 완료된 후 해당 콜백의 <code>run()</code> 메서드를 호출해야 합니다. <code>LifecycleProcessor</code> 인터페이스의 기본 구현인 <code>DefaultLifecycleProcessor</code>는 해당 콜백을 호출하기 위해 각 단계 내의 개체 그룹에 대한 제한 시간 값까지 기다리므로 필요한 경우 비동기식 종료가 가능합니다. 기본 단계별 제한 시간은 30초입니다. 컨텍스트 내에서 <code>lifecycleProcessor</code>라는 Bean을 정의하여 기본 lifecycle 프로세서 인스턴스를 재정의할 수 있습니다. 시간 초과만 수정하려면 다음을 정의하면 충분합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>lifecycleProcessor<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.support.DefaultLifecycleProcessor<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- timeout value in milliseconds --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>timeoutPerShutdownPhase<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>10000<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞서 언급했듯이 <code>LifecycleProcessor</code> 인터페이스는 컨텍스트 새로 고침 및 닫기를 위한 콜백 메서드도 정의합니다. 후자는 마치 <code>stop()</code>이 명시적으로 호출된 것처럼 종료 프로세스를 구동하지만 이는 컨텍스트가 닫힐 때 발생합니다. 반면에 '새로 고침(refresh)' 콜백은 <code>SmartLifecycle</code> Bean의 또 다른 기능을 활성화합니다. 컨텍스트가 새로 고쳐지면(모든 객체가 인스턴스화(instantiated)되고 초기화(initialized)된 후) 해당 콜백이 호출됩니다. 이 시점에서 기본 수명 주기 프로세서는 각 <code>SmartLifecycle</code> 객체의 <code>isAutoStartup()</code> 메서드에서 반환된 부울 값을 확인합니다. <code>true</code>인 경우 해당 객체는 컨텍스트 또는 자체 <code>start()</code> 메서드의 명시적인 호출을 기다리지 않고 해당 시점에서 시작됩니다(컨텍스트 새로 고침과 달리 표준 컨텍스트 구현에 대해 컨텍스트 시작이 자동으로 발생하지 않습니다). <code>phase</code> 값과 "종속" 관계는 앞에서 설명한 대로 시작 순서를 결정합니다.</p>
<h2 id="shutting-down-the-spring-ioc-container-gracefully-in-non-web-applications">Shutting Down the Spring IoC Container Gracefully in Non-Web Applications</h2>
<blockquote>
<p><strong>[Note]</strong><br>
이 섹션은 웹 애플리케이션이 아닌 애플리케이션에만 적용됩니다. Spring의 웹 기반 <code>ApplicationContext</code> 구현에는 관련 웹 애플리케이션이 종료될 때 Spring IoC 컨테이너를 정상적으로(gracefully) 종료하는 코드가 이미 있습니다.</p>
</blockquote>
<p>웹 애플리케이션이 아닌 환경(예: 리치 클라이언트 데스크톱 환경)에서 Spring의 IoC 컨테이너를 사용하는 경우 JVM에 종료(shutdown) 후크를 등록하세요. 이렇게 하면 정상적인 종료가 보장되고 싱글톤 Bean에서 관련 삭제(destroy) 메소드를 호출하여 모든 리소스가 해제됩니다. 이러한 삭제(destroy) 콜백을 올바르게 구성하고 구현해야 합니다.</p>
<p>종료(shutdown) 후크를 등록하려면 다음 예제와 같이 <code>ConfigurableApplicationContext</code> 인터페이스에 선언된 <code>registerShutdownHook()</code> 메서드를 호출합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ConfigurableApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">Boot</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
		<span class="token class-name">ConfigurableApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// add a shutdown hook for the above context...</span>
		ctx<span class="token punctuation">.</span><span class="token function">registerShutdownHook</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token comment">// app runs here...</span>

		<span class="token comment">// main method exits, hook is called prior to the app shutting down...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<hr>
<p><strong>Hook</strong><br>
"Hook"은 컴퓨터 프로그래밍에서 다른 프로그램이나 이벤트 처리에 대한 사용자 정의 코드를 삽입할 수 있는 기능을 말합니다. 이것은 보통 다음과 같은 두 가지 유형으로 나뉩니다:</p>
<ol>
<li>
<p><strong>Callback Hook</strong>: 다른 시스템이나 프레임워크에서 호출되는 특정 함수나 메서드에 사용자 정의 코드를 삽입하는 것을 의미합니다. 예를 들어, GUI 프레임워크에서 버튼 클릭 이벤트가 발생할 때 호출되는 콜백 함수를 등록할 수 있습니다.</p>
</li>
<li>
<p><strong>Shutdown Hook</strong>: 프로그램이 종료되기 전에 실행되는 코드입니다. Java의 shutdown hook은 JVM이 종료되기 전에 실행되는 코드를 등록할 수 있게 해줍니다. 이를 통해 프로그램이 graceful하게 종료될 때 필요한 작업을 수행할 수 있습니다.</p>
</li>
</ol>
<p>이 경우에 "shutdown hook"은 프로그램이 종료되기 전에 호출되는 특정 메서드나 코드 블록을 가리킵니다. 이것은 일반적으로 리소스 정리, 파일 닫기, 데이터베이스 연결 해제 등과 같은 마무리 작업을 수행하는 데 사용됩니다. Java에서는 shutdown hook을 JVM에 등록하여 프로그램이 종료될 때 이러한 작업을 수행할 수 있습니다.</p>
<hr>
<h2 id="thread-safety-and-visibility">Thread Safety and Visibility</h2>
<p>Spring 코어 컨테이너는 생성된 싱글톤 인스턴스를 스레드로부터 안전한 방식으로 게시하여(publish) 싱글톤 잠금(lock)을 통해 액세스를 보호하고 다른 스레드의 가시성(visibility)을 보장합니다.</p>
<hr>
<p><strong>Publish</strong><br>
생성된 싱글톤 인스턴스를 다른 부분에 알리거나 공개하는 것을 의미합니다. Spring core container가 생성한 싱글톤 인스턴스는 다른 부분에서 사용할 수 있어야 합니다. </p>
<p><strong>Visibility</strong><br>
다중 스레드 환경에서의 변수의 상태를 나타냅니다. 특히, 변수의 변경 사항이 다른 스레드에서 즉시 보이는지 여부를 나타냅니다. </p>
<p>스레드는 동일한 자원을 공유할 수 있지만, 각각의 스레드는 자신의 CPU 캐시를 가지고 있습니다. 따라서 한 스레드에서 변수의 값을 변경하면 이 변경 사항이 즉시 다른 스레드에 반영되지 않을 수 있습니다. 이는 CPU 캐시와 메인 메모리 간의 데이터 일관성 문제 때문에 발생합니다.</p>
<p><code>volatile</code>이나 <code>synchronized</code>를 사용하는 것은 이러한 데이터 일관성 문제를 해결하기 위한 것입니다.</p>
<ul>
<li>
<p><code>volatile</code>: 변수를 <code>volatile</code>로 선언하면 해당 변수의 값을 변경할 때 CPU 캐시가 아닌 항상 메인 메모리에서 읽거나 쓰도록 보장됩니다. 따라서 다른 스레드에서 이 값을 읽을 때 항상 최신 값을 얻을 수 있습니다.</p>
</li>
<li>
<p><code>synchronized</code>: <code>synchronized</code> 블록이나 메서드를 사용하면 해당 블록 또는 메서드에 대한 접근을 동기화할 수 있습니다. 이는 한 번에 하나의 스레드만이 <code>synchronized</code> 블록 또는 메서드에 접근할 수 있도록 보장합니다. 따라서 하나의 스레드가 변수를 변경하는 동안 다른 스레드가 해당 변수를 읽을 수 없습니다. 이를 통해 데이터 일관성을 보장할 수 있습니다.</p>
</li>
</ul>
<p><strong>singleton lock</strong><br>
"Singleton lock"은 여러 스레드가 동시에 싱글톤 인스턴스에 접근하지 못하도록 제어하는 메커니즘입니다. 이것은 스레드 간의 경합 조건(race condition)을 방지하여 싱글톤 인스턴스에 안전하게 접근할 수 있도록 보장합니다. 이러한 방식으로 싱글톤 인스턴스를 "publish"하고, 여러 스레드가 안전하게 "access"할 수 있도록 보장합니다.</p>
<hr>
<p>결과적으로, 애플리케이션이 제공하는 Bean 클래스는 초기화 상태의 가시성에 대해 걱정할 필요가 없습니다. 일반 구성(regular configuration) 필드는 초기화 단계에서만 변경(mutated)되는 한 <code>volatile</code>으로 표시할 필요가 없으며, 초기 단계에서 변경 가능한 setter 기반 구성 상태에 대해서도 <code>final</code>과 유사한 가시성을 보장합니다. 이러한 필드가 Bean 생성 단계 및 후속 초기 공개(publication) 후에 변경되면 액세스할 때마다 <code>volatile</code>으로 선언하거나 공통 잠금으로 보호해야 합니다.</p>
<p>싱글톤 Bean 인스턴스에서 이러한 구성 상태에 대한 동시(concurrent) 액세스에 유의하세요. 컨트롤러 인스턴스 또는 저장소 인스턴스의 경우 컨테이너 측에서 안전한 초기 게시 후 완벽하게 스레드로부터 안전합니다. 여기에는 일반 싱글톤 잠금 내에서 처리되는 일반 싱글톤 <code>FactoryBean</code> 인스턴스도 포함됩니다.</p>
<p>소멸 콜백의 경우 구성 상태는 스레드로부터 안전하게 유지되지만 초기화와 소멸 사이에 누적된 런타임 상태는 일반적인(common) Java 지침대로 스레드로부터 안전한 구조(또는 간단한 경우에는 <code>volatile</code> 필드)에 유지되어야 합니다.</p>
<p>위에 표시된 심층 <code>Lifecycle</code> 통합에는 <code>volatile</code>으로 선언해야 하는 <code>runnable</code> 필드와 같은 runtime-mutable 상태가 포함됩니다. 일반적인(common) 수명 주기 콜백은 특정(certain) 순서를 따르지만, 시작(start) 콜백은 전체 초기화 후에만 발생하고 중지(stop) 콜백은 초기 시작 후에만 발생하도록 보장됩니다. 파괴 전 정리 과정에서 중지(stop)가 있는 특별한 경우도 있습니다. 이러한 Bean의 내부 상태도, 앞서 stop 없이 즉각적인 destroy 콜백을 허용하는 것을 권장합니다. 왜냐하면 취소된 bootstrap 또는 다른 bean에 의해 야기된 stop 시간초과 이후 비정상적인 shutdown에서 종종 발생하기 때문입니다. </p>
<h1 id="applicationcontextaware-and-beannameaware"><code>ApplicationContextAware</code> and <code>BeanNameAware</code></h1>
<p><code>ApplicationContext</code>가 <code>org.springframework.context.ApplicationContextAware</code> 인터페이스를 구현하는 객체 인스턴스를 생성할 때 해당 인스턴스는 해당 <code>ApplicationContext</code>에 대한 참조와 함께 제공됩니다. 다음 목록은 <code>ApplicationContextAware</code> 인터페이스의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">ApplicationContextAware</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">setApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">ApplicationContext</span> applicationContext<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">BeansException</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>따라서 빈은 그들을 생성한 <code>ApplicationContext</code>를 프로그래밍 방식으로 조작할 수 있습니다. <code>ApplicationContext</code> 인터페이스를 통해서, 또는 이 인터페이스의 알려진 하위 클래스(이를테면, 추가 기능을 노출하는 <code>ConfigurableApplicationContext</code>)의 참조(reference)를 캐스팅(casting) 함으로써 말이죠. </p>
<hr>
<p><strong>Reference를 casting 한다</strong><br>
이 용어는 하나의 클래스로부터 생성된 객체를 다른 클래스의 객체로 변환하는 작업을 의미합니다. 이 때, 원래 클래스의 객체를 새로운 클래스의 객체로 변환하기 위해 명시적으로 타입을 변환하는 것을 "캐스팅(casting)"이라고 합니다.</p>
<p>만약 어떤 객체가 <code>ApplicationContext</code> 인터페이스로 선언되었지만 실제로는 <code>ConfigurableApplicationContext</code> 인터페이스를 구현한 객체를 참조하고 있다면, 이 객체를 <code>ConfigurableApplicationContext</code>로 명시적으로 캐스팅하여 추가적인 기능을 사용할 수 있습니다. </p>
<p>간단한 예를 보여드리겠습니다:</p>
<pre><code class="language-java"><span class="token class-name">ApplicationContext</span> context <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token class-name">MyConfiguration</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">if</span> <span class="token punctuation">(</span>context <span class="token keyword">instanceof</span> <span class="token class-name">ConfigurableApplicationContext</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">ConfigurableApplicationContext</span> configurableContext <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">ConfigurableApplicationContext</span><span class="token punctuation">)</span> context<span class="token punctuation">;</span>
    <span class="token comment">// 여기서 configurableContext는 ApplicationContext로 선언되었지만 ConfigurableApplicationContext의 기능을 사용할 수 있게 됩니다.</span>
    configurableContext<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예시에서는 <code>ApplicationContext</code> 객체를 <code>ConfigurableApplicationContext</code>로 캐스팅하여 <code>refresh()</code> 메서드를 호출할 수 있게 되었습니다.</p>
<hr>
<p>한 가지 용도는 다른 Bean을 프로그래밍 방식으로 검색하는 것입니다. 때로는 이 기능이 유용할 때도 있습니다. 그러나 일반적으로 이를 피해야 합니다. 왜냐하면 코드를 Spring에 결합하고 collaborators(협력자=의존성)가 Bean에 속성(property)으로 제공되는 Inversion of Control 스타일을 따르지 않기 때문입니다. <code>ApplicationContext</code>의 다른 메서드는 파일 리소스에 대한 액세스, 애플리케이션 이벤트 게시(publish) 및 <code>MessageSource</code> 액세스를 제공합니다. 이러한 추가 기능은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html"><code>ApplicationContext</code>의 추가 기능</a>에 설명되어 있습니다.</p>
<p>Autowiring은 <code>ApplicationContext</code>에 대한 참조를 얻는 또 다른 대안입니다. 전통적인 <code>constructor</code> 및 <code>byType</code> autowiring 모드(<a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html">Autowiring Collaborators에 설명된 대로</a>)는 각각 생성자 인수 또는 setter 메서드 매개 변수에 대해 <code>ApplicationContext</code> type의 종속성을 제공할 수 있습니다. 필드와 여러 매개변수 메서드를 autowiring 하는 기능을 포함한 유연성을 높이려면 어노테이션 기반 autowiring 기능을 사용하세요. 그렇게 하면 문제의 필드, 생성자 또는 메소드가 <code>@Autowired</code> 어노테이션을 전달하는 경우 <code>ApplicationContext</code> 타입을 예상하는 필드, 생성자 인수 또는 메소드 매개변수에 <code>ApplicationContext</code>가 자동으로 연결됩니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html"><code>@Autowired</code> 사용</a>을 참조하세요.</p>
<p><code>ApplicationContext</code>가 <code>org.springframework.beans.factory.BeanNameAware</code> 인터페이스를 구현하는 클래스를 생성할 때, 클래스에는 연관된 객체 정의에 정의된 이름에 대한 참조가 제공됩니다. 다음 목록은 BeanNameAware 인터페이스의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">BeanNameAware</span> <span class="token punctuation">{</span>

	<span class="token keyword">void</span> <span class="token function">setBeanName</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">BeansException</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>콜백은 일반 Bean 속성(propertiy)을 채운(population) 이후, 그러나 <code>InitializingBean.afterPropertiesSet()</code> 또는 사용자 정의 init-method와 같은 초기화 콜백 이전에 호출됩니다.</p>
<h1 id="other-aware-interfaces">Other <code>Aware</code> Interfaces</h1>
<p><code>ApplicationContextAware</code> 및 <code>BeanNameAware</code>(<a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-aware">앞서 논의</a>) 외에도 Spring은 Bean이 특정 인프라 종속성을 필요로 한다는 것을 컨테이너에 지시할 수 있는 광범위한 <code>Aware</code> 콜백 인터페이스를 제공합니다. 일반적으로 이름은 종속성 type을 나타냅니다. 다음 표에는 가장 중요한 <code>Aware</code> 인터페이스가 요약되어 있습니다.</p>
<p></p>
<table id="beans-factory-nature-aware-list" class="tableblock frame-all grid-all stretch">
<caption class="title">표 1. 인지 인터페이스</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">이름</th>
<th class="tableblock halign-left valign-top">주입된 종속성</th>
<th class="tableblock halign-left valign-top">다음에서 설명됨…​</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContextAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContext</code> 선언.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-aware"><code>ApplicationContextAware</code> 및 <code>BeanNameAware</code></a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationEventPublisherAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">포함된 <code>ApplicationContext</code>의 이벤트 게시자.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="context-introduction.html" class="xref page">ApplicationContext의 추가 기능</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>BeanClassLoaderAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">빈 클래스를 로드하는 데 사용되는 클래스 로더.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="definition.html#beans-factory-class" class="xref page">빈 생성</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>BeanFactoryAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>BeanFactory</code> 선언.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="beanfactory.html" class="xref page">BeanFactory API</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>BeanNameAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">선언된 빈의 이름.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="#beans-factory-aware"><code>ApplicationContextAware</code> 및 <code>BeanNameAware</code></a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>LoadTimeWeaverAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">로드 시간에 클래스 정의를 처리하는 정의된 위버.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../aop/using-aspectj.html#aop-aj-ltw" class="xref page">스프링 프레임워크에서 AspectJ와 함께 로드 시간 위빙</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>MessageSourceAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">메시지 해결을 위한 구성된 전략 (매개변수화 및 국제화 지원).</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="context-introduction.html" class="xref page">ApplicationContext의 추가 기능</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>NotificationPublisherAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">스프링 JMX 알림 게시자.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../../integration/jmx/notifications.html" class="xref page">알림</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ResourceLoaderAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">리소스에 대한 저수준 액세스를 위한 구성된 로더.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../../web/webflux-webclient/client-builder.html#webflux-client-builder-reactor-resources" class="xref page">리소스</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ServletConfigAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">컨테이너가 실행 중인 현재 <code>ServletConfig</code>. 웹-인식 스프링 <code>ApplicationContext</code>에서만 유효합니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../../web/webmvc.html#mvc" class="xref page">Spring MVC</a></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ServletContextAware</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">컨테이너가 실행 중인 현재 <code>ServletContext</code>. 웹-인식 스프링 <code>ApplicationContext</code>에서만 유효합니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><a href="../../web/webmvc.html#mvc" class="xref page">Spring MVC
</a></p><p></p></td></tr>
</tbody>
</table>
<p>이러한 인터페이스를 사용하면 코드가 Spring API에 결합되고 Inversion of Control 스타일을 따르지 않는다는 점을 다시 한 번 참고하세요. 결과적으로 컨테이너에 프로그래밍 방식으로 액세스해야 하는 인프라 Bean에 권장됩니다.</p>