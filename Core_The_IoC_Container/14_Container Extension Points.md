<p>일반적으로 애플리케이션 개발자는 하위 클래스로<code>ApplicationContext</code> 구현할 필요가 없습니다. 대신, 특수 통합(integration) 인터페이스 구현을 플러그인하여 Spring IoC 컨테이너를 확장할 수 있습니다. 다음 몇 섹션에서는 이러한 통합 인터페이스에 대해 설명합니다.</p>
<h1 id="customizing-beans-by-using-abeanpostprocessor">Customizing Beans by Using a<code>BeanPostProcessor</code></h1>
<p><code>BeanPostProcessor</code> 인터페이스는 자신만의 인스턴스화 논리(또는 컨테이너의 기본 인스턴스화를 재정의), 종속성 resolution 논리 등을 제공하기 위해 구현할 수 있는 콜백 메서드를 정의합니다. Spring 컨테이너가 인스턴스화, 구성(configuration) 및 bean 초기화를 마친 후에 이어지는 일부 사용자 정의 논리를 구현하려는 경우 하나 이상의 사용자 정의 <code>BeanPostProcessor</code> 구현을 플러그인할 수 있습니다.</p>
<p>여러 개의 <code>BeanPostProcessor</code> 인스턴스를 구성할 수 있으며 <code>order</code> 속성(property)을 설정하여 이러한 <code>BeanPostProcessor</code> 인스턴스가 실행되는 순서를 제어할 수 있습니다. <code>BeanPostProcessor</code>가 <code>Ordered</code> 인터페이스를 구현하는 경우에만 이 속성(property)을 설정할 수 있습니다. 자신만의 <code>BeanPostProcessor</code>를 작성한다면 <code>Ordered</code> 인터페이스 구현도 고려해야 합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html"><code>BeanPostProcessor</code></a> 및 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/core/Ordered.html"><code>Ordered</code></a> 인터페이스의 javadoc를 참조하세요. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-programmatically-registering-beanpostprocessors"><code>BeanPostProcessor</code> 인스턴스의 프로그래밍 방식 등록</a>에 대한 참고 사항도 참조하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>BeanPostProcessor</code> 인스턴스는 Bean(또는 객체) 인스턴스에서 작동합니다. 즉, Spring IoC 컨테이너는 빈 인스턴스를 인스턴스화한 다음, <code>BeanPostProcessor</code> 인스턴스가 작업을 수행한다는 말입니다.</p>
<p><code>BeanPostProcessor</code> 인스턴스는 per-container로 범위가 지정됩니다. 이는 컨테이너 계층을 사용하는 경우에만 관련됩니다. 하나의 컨테이너에 <code>BeanPostProcessor</code>를 정의하면 해당 컨테이너의 Bean만 사후 처리합니다. 즉, 한 컨테이너에 정의된 Bean은 두 컨테이너가 모두 동일한 계층 구조에 속하더라도 다른 컨테이너에 정의된 <code>BeanPostProcessor</code>에 의해 사후 처리되지 않습니다.</p>
<p>실제 빈 정의(즉, 빈을 정의하는 청사진)를 변경하려면, <a href="%60BeanFactoryPostProcessor%60"><code>BeanFactoryPostProcessor</code>로 구성 메타데이터 사용자 정의하기</a>에 설명된 대로, 대신 <code>BeanFactoryPostProcessor</code>를 사용해야 합니다.</p>
</blockquote>
<p><code>org.springframework.beans.factory.config.BeanPostProcessor</code> 인터페이스는 정확히 두 개의 콜백 메소드로 구성됩니다. 이러한 클래스가 컨테이너에 사후 프로세서로 등록되면 컨테이너에 의해 생성된 각 Bean 인스턴스에 대해 사후 프로세서는 컨테이너 초기화 메서드(예: <code>InitializingBean.afterPropertiesSet()</code> 또는 선언된 <code>init</code> 메소드)가 호출되고 Bean 초기화 콜백 이후에 호출됩니다. 후처리자(post-processor)는 콜백을 완전히 무시하는 것을 포함하여 Bean 인스턴스에 대해 모든 작업을 수행할 수 있습니다. Bean 사후 프로세서는 일반적으로 콜백 인터페이스를 확인하거나 프록시로 Bean을 래핑할 수 있습니다. 일부 Spring AOP 인프라 클래스는 프록시 래핑 논리를 제공하기 위해 Bean 사후 프로세서로 구현됩니다.</p>
<p><code>ApplicationContext</code>는 <code>BeanPostProcessor</code> 인터페이스를 구현하는 구성 메타데이터에 정의된 모든 Bean을 자동으로 감지합니다. <code>ApplicationContext</code>는 이러한 Bean을 사후 프로세서로 등록하여 나중에 Bean 생성 시 호출할 수 있도록 합니다. Bean 후처리자는 다른 Bean과 동일한 방식으로 컨테이너에 배치될 수 있습니다.</p>
<p>구성(configuration) 클래스에서 <code>@Bean</code> 팩토리 메소드를 사용하여 <code>BeanPostProcessor</code>를 선언할 때 팩토리 메소드의 반환 유형은 구현 클래스 자체이거나 최소한 <code>org.springframework.beans.factory.config.BeanPostProcessor</code> 인터페이스여야 하며, 해당 Bean의 사후 프로세서의 nature를 명백하게 나타내야 합니다. 그렇지 않으면 <code>ApplicationContext</code>는 bean이 완전히 생성되기 전, by type 방식으로 자동 감지할 수 없습니다. <code>BeanPostProcessor</code>는 컨텍스트에서 다른 Bean의 초기화에 적용하기 위해 먼저(early) 인스턴스화되어야 하기 때문에, 이러한 초기(early) type 감지가 중요합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<em>프로그래밍 방식으로 <code>BeanPostProcessor</code> 인스턴스 등록하기</em><br>
<code>BeanPostProcessor</code> 등록에 권장되는 접근 방식은 <code>ApplicationContext</code> 자동 감지(앞서 설명한 대로)를 통한 것이지만 <code>addBeanPostProcessor</code> 메소드를 사용하여 <code>ConfigurableBeanFactory</code>에 대해(against) 프로그래밍 방식으로 등록할 수 있습니다. 이는 등록 전에 조건부 논리를 평가해야 하거나 계층 구조의 컨텍스트 전체에 걸쳐 Bean 사후 프로세서를 복사해야 할 때 유용할 수 있습니다. 그러나 프로그래밍 방식으로 추가된 <code>BeanPostProcessor</code> 인스턴스는 <code>Ordered</code> 인터페이스를 따르지 않습니다. 여기서 실행 순서를 결정하는 것은 등록 순서입니다. 프로그래밍 방식으로 등록된 <code>BeanPostProcessor</code> 인스턴스는 명시적인 순서에 관계없이 항상 자동 감지를 통해 등록된 인스턴스보다 먼저 처리됩니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
<em><code>BeanPostProcessor</code> 인스턴스 및 AOP 자동 프록시</em><br>
<code>BeanPostProcessor</code> 인터페이스를 구현하는 클래스는 특별하며 컨테이너에 의해 다르게 처리됩니다. 직접 참조하는 모든 <code>BeanPostProcessor</code> 인스턴스와 빈은 <code>ApplicationContext</code>의 특수 시작 단계의 일부로서, 시작 시 인스턴스화됩니다. 다음으로 모든 <code>BeanPostProcessor</code> 인스턴스는 정렬된 방식으로 등록되고 컨테이너의 모든 추가 Bean에 적용됩니다. 왜냐하면, AOP 자동 프록시는 <code>BeanPostProcessor</code> 자체로 구현되기 때문에,  <code>BeanPostProcessor</code> 인스턴스나 그들이 직접 참조하는 Bean은 자동 프록시에 적합하지 않으므로 그들에게 포함되는 aspects woven이 없습니다.</p>
</blockquote>
<hr>
<p><strong>AOP와 Bean Post Processor</strong><br>
AOP(Aspect-Oriented Programming)는 프로그래밍에서 관심사를 모듈화하고 분리하여 코드의 재사용성과 유지보수성을 향상시키는 기술입니다. </p>
<p>Proxy는 다른 객체에 대한 대리자 역할을 하는 객체를 말합니다. 이것은 클라이언트가 직접 객체에 접근하는 대신에 프록시를 통해 객체에 간접적으로 접근하게 하는데 사용됩니다. Proxy는 주로 보안, 로깅, 트랜잭션 처리 등과 같은 부가적인 기능을 추가하기 위해 사용됩니다.</p>
<p>Auto-proxying은 스프링에서 AOP를 구현하는 방법 중 하나입니다. 이것은 스프링 컨테이너가 빈을 만들 때 자동으로 프록시를 생성하여 AOP를 적용하는 것을 의미합니다. 스프링에서는 AOP를 적용하기 위해 프록시를 사용하거나 빈의 생성 및 수정 과정에 BeanPostProcessor를 사용하는데, 이것이 auto-proxying의 핵심 메커니즘입니다</p>
<p>BeanPostProcessor는 스프링 프레임워크에서 빈(Bean)을 초기화하고 수정하는 데 사용되는 인터페이스입니다. 이 인터페이스를 구현한 클래스는 스프링 컨테이너가 빈을 만들거나 수정할 때 특별한 방식으로 처리됩니다.</p>
<p>여기서 Aspect와 BeanPostProcessor의 관련성이 나타납니다. AOP는 주로 AspectJ와 같은 라이브러리를 사용하여 구현됩니다. 이러한 AOP 라이브러리는 Aspect를 적용하기 위해 BeanPostProcessor와 같은 메커니즘을 사용합니다. AspectJ의 경우, AspectJ 라이브러리는 컴파일 타임이나 런타임에 Aspect를 자바 클래스에 "woven"합니다. 이것은 AspectJ 컴파일러나 런타임이 빈을 만들거나 수정할 때 BeanPostProcessor를 사용하여 Aspect를 적용하는 것과 유사한 방식으로 작동합니다.</p>
<p>AOP auto-proxying이 BeanPostProcessor를 사용하여 구현되어 있지만 BeanPostProcessor 인스턴스나 그들이 직접 참조하는 빈에 대해서는 적용되지 않는 이유는 다음과 같습니다:</p>
<ol>
<li>
<p><strong>순서 문제</strong>: BeanPostProcessor는 빈이 생성되고 초기화되기 전과 후에 호출됩니다. 그러나 AOP auto-proxying은 빈이 생성된 후에 해당 빈에 적용됩니다. 따라서 BeanPostProcessor 인스턴스 자체나 그들이 참조하는 빈들에 대한 AOP auto-proxying은 BeanPostProcessor가 이미 적용된 이후에 발생하므로 순서적으로 적용되지 않습니다.</p>
</li>
<li>
<p><strong>무한 루프와 충돌 방지</strong>: BeanPostProcessor 인스턴스나 그들이 참조하는 빈들에 대해 AOP auto-proxying을 적용하면 무한 루프가 발생할 수 있습니다. 즉, BeanPostProcessor를 통해 빈을 수정하고, 그 수정된 빈을 다시 BeanPostProcessor가 처리하고, 이런 식으로 계속됩니다. 이는 예기치 않은 동작을 초래할 수 있으며, 이를 방지하기 위해 BeanPostProcessor와 AOP auto-proxying은 별도로 처리됩니다.</p>
</li>
</ol>
<p>따라서 BeanPostProcessor 인스턴스나 그들이 참조하는 빈들은 AOP auto-proxying 대상이 아닙니다. 이로써 AOP auto-proxying이 예상치 않은 동작이나 무한 루프와 같은 문제를 일으키는 것을 방지하고, 스프링 애플리케이션의 안정성을 유지할 수 있습니다.</p>
<hr>
<blockquote>
<p>어떤 경우에도<br>
해당 bean의 경우 다음과 같은 정보 로그 메시지가 표시되어야 합니다. <code> Bean someBean is not eligible for getting processed by all BeanPostProcessor interfaces (for example: not eligible for auto-proxying)</code> <code>Bean someBean</code>은 모든 <code>BeanPostProcessor</code> 인터페이스에서 처리할 수 없습니다(예: 자동 프록시에 적합하지 않음).</p>
<p>autowiring 또는 <code>@Resource</code>(autowiring으로 대체될 수 있음)를 사용하여 <code>BeanPostProcessor</code>에 빈을 연결한 경우 Spring이 타입 매칭 종속성 후보를 검색할 때 예기치 않은 빈에 액세스할 수 있으므로 자동 프록시 또는 다른 종류의 빈 후처리 과정에 적합하지 않게 만듭니다. 예를 들어 필드 또는 setter 이름이 Bean의 선언된 이름과 직접적으로 일치하지 않고 name 속성(attribue)이 사용되지 않는 <code>@Resource</code> 어노테이션이 달린 종속성이 있는 경우 Spring은 by type으로 일치시키기 위해 다른 Bean에 액세스합니다.</p>
</blockquote>
<p>다음 예제는 <code>ApplicationContext</code>에서 <code>BeanPostProcessor</code> 인스턴스를 작성, 등록 및 사용하는 방법을 보여줍니다.</p>
<h2 id="example-hello-world-beanpostprocessor-style">Example: Hello World, <code>BeanPostProcessor</code>-style</h2>
<p>이 첫 번째 예는 기본적인 사용법을 보여줍니다. 예제는 컨테이너에 의해 생성된 각 Bean의 <code>toString()</code> 메소드를 호출하고 결과 문자열을 시스템 콘솔에 인쇄하는 사용자 정의 <code>BeanPostProcessor</code> 구현을 보여줍니다.</p>
<p>다음 목록은 사용자 정의 <code>BeanPostProcessor</code> 구현 클래스 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> scripting<span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>config</span><span class="token punctuation">.</span><span class="token class-name">BeanPostProcessor</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">InstantiationTracingBeanPostProcessor</span> <span class="token keyword">implements</span> <span class="token class-name">BeanPostProcessor</span> <span class="token punctuation">{</span>

	<span class="token comment">// simply return the instantiated bean as-is</span>
	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">postProcessBeforeInitialization</span><span class="token punctuation">(</span><span class="token class-name">Object</span> bean<span class="token punctuation">,</span> <span class="token class-name">String</span> beanName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> bean<span class="token punctuation">;</span> <span class="token comment">// we could potentially return any object reference here...</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">postProcessAfterInitialization</span><span class="token punctuation">(</span><span class="token class-name">Object</span> bean<span class="token punctuation">,</span> <span class="token class-name">String</span> beanName<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Bean '"</span> <span class="token operator">+</span> beanName <span class="token operator">+</span> <span class="token string">"' created : "</span> <span class="token operator">+</span> bean<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> bean<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 <code>bean</code> 요소는 <code>InstantiationTracingBeanPostProcessor</code>를 사용합니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>lang</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/lang<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/lang
		https://www.springframework.org/schema/lang/spring-lang.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">lang:</span>groovy</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>messenger<span class="token punctuation">"</span></span>
			<span class="token attr-name">script-source</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:org/springframework/scripting/groovy/Messenger.groovy<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">lang:</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>message<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Fiona Apple Is Just So Dreamy.<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">lang:</span>groovy</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!--
	when the above bean (messenger) is instantiated, this custom
	BeanPostProcessor implementation will output the fact to the system console
	--&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>scripting.InstantiationTracingBeanPostProcessor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>InstantiationTracingBeanPostProcessor</code>가 어떻게 정의되었는지 주목하세요. 이름도 없고 Bean이므로 다른 Bean과 마찬가지로 종속성을 주입할 수 있습니다. (이전 구성은 Groovy 스크립트가 지원하는(backed by) Bean도 정의합니다. Spring 동적 언어 지원은 <a href="https://docs.spring.io/spring-framework/reference/languages/dynamic.html">동적 언어 지원</a> 장에 자세히 설명되어 있습니다.)</p>
<p>다음 Java 애플리케이션은 이전 코드와 구성을 실행합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context</span><span class="token punctuation">.</span><span class="token class-name">ApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>context<span class="token punctuation">.</span>support</span><span class="token punctuation">.</span><span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>scripting</span><span class="token punctuation">.</span><span class="token class-name">Messenger</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">Boot</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
		<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"scripting/beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">Messenger</span> messenger <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token string">"messenger"</span><span class="token punctuation">,</span> <span class="token class-name">Messenger</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>messenger<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p>이전 애플리케이션의 출력은 다음과 유사합니다.</p>
<pre><code class="language-null">Bean 'messenger' created : org.springframework.scripting.groovy.GroovyMessenger@272961
org.springframework.scripting.groovy.GroovyMessenger@272961</code></pre>
<h2 id="example-the-autowiredannotationbeanpostprocessor">Example: The <code>AutowiredAnnotationBeanPostProcessor</code></h2>
<p>사용자 정의 <code>BeanPostProcessor</code> 구현과 함께(in conjunction with) 콜백 인터페이스 또는 어노테이션을 사용하는 것은 Spring IoC 컨테이너를 확장(extending)하는 일반적인 방법입니다. 예를 들어 Spring의 <code>AutowiredAnnotationBeanPostProcessor</code> 는 Spring 배포판과 함께 제공되고 주석이 달린 필드, setter 메소드 및 임의 구성 메소드를 자동 연결하는 <code>BeanPostProcessor</code> 구현입니다.</p>
<h1 id="customizing-configuration-metadata-with-a-beanfactorypostprocessor">Customizing Configuration Metadata with a <code>BeanFactoryPostProcessor</code></h1>
<p>우리가 살펴볼 다음 확장점은 <code>org.springframework.beans.factory.config.BeanFactoryPostProcessor</code>입니다. 이 인터페이스의 의미(seantics)는 <code>BeanPostProcessor</code>의 의미와 유사하지만 한 가지 주요 차이점은 <code>BeanFactoryPostProcessor</code>가 빈 구성 메타데이터에서 작동한다는 것입니다. 즉, Spring IoC 컨테이너는 <code>BeanFactoryPostProcessor</code>가 구성 메타데이터를 읽고 잠재적으로 컨테이너가 <code>BeanFactoryPostProcessor</code> 인스턴스 이외의 빈을 인스턴스화하기 전에 이를 변경할 수 있도록 합니다.</p>
<p>여러 개의 <code>BeanFactoryPostProcessor</code> 인스턴스를 구성할 수 있으며 <code>order</code> 속성(property)을 설정하여 이러한 <code>BeanFactoryPostProcessor</code> 인스턴스가 실행되는 순서를 제어할 수 있습니다. 그러나 <code>BeanFactoryPostProcessor</code>가 <code>Ordered</code> 인터페이스를 구현하는 경우에만 이 속성을 설정할 수 있습니다. 자신만의 <code>BeanFactoryPostProcessor</code>를 작성한다면 <code>Ordered</code> 인터페이스 구현도 고려해야 합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/config/BeanFactoryPostProcessor.html"><code>BeanFactoryPostProcessor</code></a> 및 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/core/Ordered.html"><code>Ordered</code></a> 인터페이스의 javadoc를 참조하세요.</p>
<blockquote>
<p><strong>[Note]</strong><br>
실제 빈 인스턴스(즉, 구성 메타데이터에서 생성된 객체)를 변경하려면 대신 <code>BeanPostProcessor</code>를 사용해야 합니다(앞서 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-bpp"><code>BeanPostProcessor</code>를 사용하여 빈 사용자 정의하기</a>에서 설명). <code>BeanFactoryPostProcessor</code> 내에서 Bean 인스턴스로 작업하는 것이 기술적으로 가능하지만(예: <code>BeanFactory.getBean()</code> 사용) 그렇게 하면 조기(premature) Bean 인스턴스화가 발생하여 표준 컨테이너 lifecycle를 위반하게 됩니다. 이는 Bean 사후 처리를 우회하는 등 부정적인 부작용을 일으킬 수 있습니다.</p>
<p>또한 <code>BeanFactoryPostProcessor</code> 인스턴스는 per-container로 범위가 지정됩니다. 이는 컨테이너 계층 구조를 사용하는 경우에만 관련됩니다. 하나의 컨테이너에 <code>BeanFactoryPostProcessor</code>를 정의하면 해당 컨테이너의 Bean 정의에만 적용됩니다. 한 컨테이너의 Bean 정의는 두 컨테이너가 동일한 계층의 일부인 경우에도 다른 컨테이너의 <code>BeanFactoryPostProcessor</code> 인스턴스에 의해 사후 처리되지 않습니다.</p>
</blockquote>
<p>Bean 팩토리 후처리자(post-processor)는 컨테이너를 정의하는 구성 메타데이터에 변경사항을 적용하기 위해 <code>ApplicationContext</code> 내에서 선언될 때 자동으로 실행됩니다. Spring에는 <code>PropertyOverrideConfigurer</code> 및 <code>PropertySourcesPlaceholderConfigurer</code>와 같은 사전 정의된 여러 Bean 팩토리 후처리기가 포함되어 있습니다. 예를 들어 사용자 정의 속성 편집기를 등록하기 위해 사용자 정의 <code>BeanFactoryPostProcessor</code>를 사용할 수도 있습니다.</p>
<p><code>ApplicationContext</code>는 <code>BeanFactoryPostProcessor</code> 인터페이스를 구현하는, 모든 배포된 Bean을 자동으로 감지합니다. 적절한 시간에 이러한 Bean을 Bean Factory 후처리자로 사용합니다. 다른 Bean과 마찬가지로 이러한 사후 프로세서 Bean을 배포할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>BeanPostProcessors</code>와 마찬가지로 일반적으로 지연 초기화를 위해 <code>BeanFactoryPostProcessors</code>를 구성하고 싶지 않을 것입니다. 다른 Bean이 <code>Bean(Factory)PostProcessor</code>를 참조하지 않으면 해당 사후 프로세서는 전혀 인스턴스화되지 않습니다. 따라서 지연 초기화로 표시하는 것은 무시되며 <code>&lt;beans /&gt;</code> 요소 선언에서 <code>default-lazy-init</code> 속성을 <code>true</code>로 설정하더라도 <code>Bean(Factory)PostProcessor</code>는 즉시 인스턴스화됩니다.</p>
</blockquote>
<h2 id="example--the-class-name-substitution-propertysourcesplacdeholderconfigurer">Example : The Class Name Substitution <code>PropertySourcesPlacdeholderConfigurer</code></h2>
<p><code>PropertySourcesPlaceholderConfigurer</code>는 표준 Java <code>Properties</code> 형식을 사용하여 별도의 파일에 있는 Bean 정의의 속성 값을 외부화(externalize)합니다. 이렇게 하면 애플리케이션을 배포하는 사람이 기본 XML 정의 파일이나 컨테이너용 파일을 수정하는 복잡성이나 위험 없이 데이터베이스 URL 및 비밀번호와 같은 환경별 속성을 사용자 정의할 수 있습니다.</p>
<p>자리 표시자(placeholder) 값이 있는 <code>DataSource</code>가 정의된 다음 XML 기반 구성 메타데이터 조각을 고려하세요.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.support.PropertySourcesPlaceholderConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>locations<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/something/jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.apache.commons.dbcp.BasicDataSource<span class="token punctuation">"</span></span> <span class="token attr-name">destroy-method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>close<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>driverClassName<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.driverClassName}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>url<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.url}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>username<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.username}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${jdbc.password}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>이 예에서는 외부 <code>Properties</code> 파일에서 구성된 속성을 보여줍니다. 런타임 시 DataSource의 일부 속성을 대체하는 메타데이터에 <code>PropertySourcesPlaceholderConfigurer</code>가 적용됩니다. 대체할 값은 Ant, log4j 및 JSP EL 스타일을 따르는 <code>${property-name}</code> 형식의 자리 표시자로 지정됩니다.</p>
<p>실제 값은 표준 Java <code>Properties</code> 형식의 다른 파일에서 가져옵니다.</p>
<pre><code class="language-null">jdbc.driverClassName=org.hsqldb.jdbcDriver
jdbc.url=jdbc:hsqldb:hsql://production:9002
jdbc.username=sa
jdbc.password=root</code></pre>
<p>따라서 <code>${jdbc.username}</code> 문자열은 런타임 시 'sa' 값으로 대체되며, 속성 파일의 키와 일치하는 다른 자리 표시자 값에도 동일하게 적용됩니다. <code>PropertySourcesPlaceholderConfigurer</code>는 빈 정의의 대부분의 속성(properties)과 속성(attributes)에서 자리 표시자를 확인합니다. 또한 자리 표시자 접두사 및 접미사를 사용자 정의할 수 있습니다.</p>
<p>Spring 2.5에 도입된 <code>context</code> 네임스페이스를 사용하면 전용 구성 요소로 속성 자리 표시자를 구성할 수 있습니다. 다음 예와 같이 <code>location</code> 속성(attribute)에 하나 이상의 위치를 쉼표로 구분된 목록으로 제공할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-placeholder</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/something/jdbc.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p><code>PropertySourcesPlaceholderConfigurer</code>는 사용자가 지정하는 <code>Properties</code> 파일에서 속성(property)을 찾을 뿐만 아니라 기본적으로 지정된 속성 파일에서 속성을 찾을 수 없으면 Spring <code>Environment</code> 속성(property)과 일반 Java <code>System</code> 속성(property)을 확인합니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
필요한 속성을 가진 특정 애플리케이션에 대해 그러한 요소 하나만 정의해야 합니다. 고유한 자리 표시자 구문(<code>${…​}</code>)이 있어야 여러 속성 자리 표시자를 구성할 수 있습니다.</p>
<p>교체에 사용되는 속성 소스를 모듈화해야 하는 경우 여러 속성 자리 표시자를 생성하면 안 됩니다. 오히려 사용할 속성을 수집하는 자체 <code>PropertySourcesPlaceholderConfigurer</code> 빈을 생성해야 합니다.</p>
</blockquote>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>PropertySourcesPlaceholderConfigurer</code>를 사용하여 클래스 이름을 대체할 수 있습니다. 이는 런타임 시 특정 구현 클래스를 선택해야 할 때 유용할 때가 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.beans.factory.config.PropertySourcesPlaceholderConfigurer<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>locations<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>classpath:com/something/strategy.properties<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>properties<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		 <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>custom.strategy.class=com.something.DefaultStrategy<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
 <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>serviceStrategy<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>${custom.strategy.class}<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<p>클래스가 런타임에 유효한 클래스로 분석(resolve)될 수 없는 경우, 지연 초기화가 아닌 Bean에 대한 <code>ApplicationContext</code>의 <code>preInstantiateSingletons()</code> 단계 중에, 빈이 생성되려고 할 때 빈 분석(resolution)이 실패합니다. </p>
</blockquote>
<h2 id="examplethe-propertyoverrideconfigurer">Example:The <code>PropertyOverrideConfigurer</code></h2>
<p>또 다른 빈 팩토리 후처리자인 <code>PropertyOverrideConfigurer</code>는 <code>PropertySourcesPlaceholderConfigurer</code>와 유사하지만 후자와는 달리 원래 정의에는 빈 속성(property)에 대한 기본값이 있거나 값이 전혀 없을 수 있습니다. 재정의하는 <code>Properties</code> 파일에 특정 Bean property에 대한 항목(entry)이 없으면 기본 컨텍스트 정의가 사용됩니다.</p>
<p>Bean 정의는 재정의된 것을 인식하지 못하므로 재정의 구성자(override configurer)가 사용되고 있다는 것이 XML 정의 파일에서 즉시 명백하지 않습니다. 동일한 Bean 속성에 대해 서로 다른 값을 정의하는 여러 <code>PropertyOverrideConfigurer</code> 인스턴스의 경우 재정의 메커니즘으로 인해 마지막 항목이 우선합니다.</p>
<p>속성 파일 구성 줄은 다음 형식을 사용합니다.</p>
<pre><code class="language-null">beanName.property=value</code></pre>
<p>다음 목록은 형식의 예를 보여줍니다</p>
<pre><code class="language-null">dataSource.driverClassName=com.mysql.jdbc.Driver
dataSource.url=jdbc:mysql:mydb</code></pre>
<p>이 예제 파일은 <code>driver</code> 및 <code>url</code> 속성(property)이 있는 <code>dataSource</code>라는 Bean을 포함하는 컨테이너 정의와 함께 사용할 수 있습니다.</p>
<p>재정의되는 최종 속성을 제외한 경로의 모든 구성 요소가 이미 null이 아닌 경우(아마도(presumably) 생성자에 의해 초기화된 경우) 복합(compound) 속성(property) 이름도 지원됩니다. 다음 예에서는 <code>tom</code> bean의 <code>fred</code> 속성(property)에 대한 <code>bob</code> 속성(property)의 <code>sammy</code> 속성(property)이 스칼라 값 <code>123</code>으로 설정됩니다.</p>
<pre><code class="language-null">tom.fred.bob.sammy=123</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
지정된 재정의 값은 항상 리터럴 값입니다. Bean 참조로 변환되지 않습니다. 이 규칙은 XML bean 정의의 원래 값이 bean 참조를 지정할 때도 적용됩니다.</p>
</blockquote>
<p>Spring 2.5에 도입된 <code>context</code> 네임스페이스를 사용하면 다음 예제와 같이 전용 구성 요소로 속성 재정의를 구성할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>property-override</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:override.properties<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<h1 id="customizing-instantiation-logic-with-a-factorybean">Customizing Instantiation Logic with a FactoryBean</h1>
<p>그 자체가 팩토리인 객체에 대해 <code>org.springframework.beans.factory.FactoryBean</code> 인터페이스를 구현할 수 있습니다.</p>
<p><code>FactoryBean</code> 인터페이스는 Spring IoC 컨테이너의 인스턴스화 로직에 대한 연결 지점입니다. (잠재적으로) 장황한 양의 XML과 달리 Java로 더 잘 표현되는 복잡한 초기화 코드가 있는 경우 자체 <code>FactoryBean</code>을 생성하고 해당 클래스 내에 복잡한 초기화를 작성한 다음 사용자 정의 <code>FactoryBean</code>을 컨테이너에 연결할 수 있습니다.</p>
<p><code>FactoryBean&lt;T&gt;</code> 인터페이스는 세 가지 메서드를 제공합니다.</p>
<ul>
<li>
<p><code>T getObject()</code>: 이 팩토리가 생성하는 객체의 인스턴스를 반환합니다. 이 팩토리가 싱글톤을 반환하는지 아니면 프로토타입을 반환하는지에 따라 인스턴스를 공유할 수 있습니다.</p>
</li>
<li>
<p><code>boolean isSingleton()</code>: 이 <code>FactoryBean</code>이 싱글톤을 반환하면 <code>true</code>를 반환하고 그렇지 않으면 <code>false</code>를 반환합니다. 이 메서드의 기본 구현은 <code>true</code>를 반환합니다.</p>
</li>
<li>
<p><code>Class&lt;?&gt; getObjectType()</code>: <code>getObject()</code> 메서드에서 반환된 객체 type을 반환하거나 type을 미리(in advance) 알 수 없는 경우 <code>null</code>을 반환합니다.</p>
</li>
</ul>
<p><code>FactoryBean</code> 개념과 인터페이스는 Spring Framework 내의 여러 위치에서 사용됩니다. 50개 이상의 <code>FactoryBean</code> 인터페이스 구현이 Spring 자체와 함께 제공됩니다.</p>
<p>생성된 Bean 대신 실제 <code>FactoryBean</code> 인스턴스 자체를 컨테이너에 요청해야 하는 경우 <code>ApplicationContext</code>의 <code>getBean()</code> 메서드를 호출할 때 Bean의 <code>id</code> 앞에 앰퍼샌드 기호(<code>&amp;</code>)를 붙입니다. 따라서 <code>id</code>가 <code>myBean</code>인 특정 <code>FactoryBean</code>에 대해 컨테이너에서 <code>getBean("myBean")</code>을 호출하면 <code>FactoryBean</code>의 제품이 반환되는 반면, <code>getBean("&amp;myBean")</code>을 호출하면 <code>FactoryBean</code> 인스턴스 자체가 반환됩니다.</p>