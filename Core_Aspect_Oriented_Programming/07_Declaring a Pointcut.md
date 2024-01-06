<p>포인트컷은 관심사의 조인 포인트를 결정하므로 어드바이스가 실행되는 시점을 제어할 수 있습니다. Spring AOP는 Spring 빈에 대한 메소드 실행 조인 포인트만 지원하므로 포인트컷을 Spring 빈에 대한 메소드 실행과 매칭시키는 것으로 생각할 수 있습니다. 포인트컷 선언은 두 부분으로 구성됩니다. 이름과 매개변수로 구성(comprise)된 시그니처와 관심 있는 메소드 실행을 정확하게 결정하는 포인트컷 표현식입니다. AOP의 @AspectJ 어노테이션 스타일에서 포인트컷 시그니처는 일반 메소드 정의에 의해 제공됩니다. 포인트컷 표현식은 <code>@Pointcut</code> 어노테이션을 사용하여 표시됩니다(포인트컷 시그니처 역할을 하는 메소드는 <code>void</code> 반환 타입을 가져야 함).</p>
<p>예제는 포인트컷 시그니처와 포인트컷 표현식 사이의 구별을 명확하게 하는 데 도움이 될 수 있습니다. 다음 예제에서는 <code>transfer</code>라는 메서드의 실행과 매칭되는 <code>anyOldTransfer</code>라는 포인트컷을 정의합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"execution(* transfer(..))"</span><span class="token punctuation">)</span> <span class="token comment">// the pointcut expression</span>
<span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">anyOldTransfer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">// the pointcut signature</span></code></pre>
<p><code>@Pointcut</code> 어노테이션의 값을 형성하는 포인트컷 표현식은 일반(regular) AspectJ 포인트컷 표현식입니다. AspectJ의 포인트컷 언어에 대한 전체 토론을 보려면 <a href="https://www.eclipse.org/aspectj/doc/released/progguide/index.html">AspectJ 프로그래밍 가이드</a>(및 확장에 대해서는 <a href="https://www.eclipse.org/aspectj/doc/released/adk15notebook/index.html">AspectJ 5 개발자 노트북</a>) 또는 AspectJ에 관한 책(예: Colyer 등의 Eclipse AspectJ 또는 AspectJ in Action, Ramnivas Laddad 작성)을 참고하세요.</p>
<h1 id="supported-pointcut-designators">Supported Pointcut Designators</h1>
<p>Spring AOP는 포인트컷 표현식에 사용하기 위해 다음 AspectJ 포인트컷 지정자(designator)(PCD)를 지원합니다.</p>
<ul>
<li>
<p><code>execution</code> : 메서드 실행 조인 포인트를 일치(matching)시키는 데 사용됩니다. 이는 Spring AOP로 작업할 때 사용하는 기본(primary) 포인트컷 지정자입니다.</p>
</li>
<li>
<p><code>within</code> : 특정 타입 내의 조인 포인트로 일치(matching)를 제한합니다(Spring AOP를 사용할 때 매칭되는 타입 내에 선언된 메서드 실행).</p>
</li>
<li>
<p><code>this</code> : Bean 참조(Spring AOP 프록시)가 주어진 타입의 인스턴스인 조인 포인트(Spring AOP를 사용할 때 메소드 실행)에 대한 일치(matching)를 제한합니다.</p>
</li>
<li>
<p><code>target</code>: 대상 객체(프록시되는 애플리케이션 객체)가 주어진 타입의 인스턴스인 조인 포인트(Spring AOP를 사용할 때 메소드 실행)에 대한 일치를 제한합니다.</p>
</li>
<li>
<p><code>args</code>: 인수가 주어진 타입의 인스턴스인 조인 포인트(Spring AOP를 사용할 때 메소드 실행)에 대한 일치를 제한합니다.</p>
</li>
<li>
<p><code>@target</code> : 실행 객체의 클래스에 지정된 타입의 어노테이션이 있는 조인 포인트(Spring AOP를 사용할 때 메서드 실행)에 대한 일치를 제한합니다.</p>
</li>
<li>
<p><code>@args</code> : 전달된 실제 인수의 런타임 유형에 지정된 타입의 어노테이션이 있는 조인 포인트(Spring AOP를 사용할 때 메소드 실행)에 대한 일치를 제한합니다.</p>
</li>
<li>
<p><code>@within</code> : 지정된 어노테이션이 있는 타입 내의 조인 포인트로 일치를 제한합니다(Spring AOP를 사용할 때 지정된 어노테이션이 있는 타입에 선언된 메서드 실행).</p>
</li>
</ul>
<p><code>@annotation</code> : 조인 포인트의 주체(Spring AOP에서 실행되는 메서드)에 지정된 어노테이션이 있는 조인 포인트로 일치를 제한합니다.</p>
<blockquote>
<h3 id="다른-포인트컷-유형">다른 포인트컷 유형</h3>
<p>전체 AspectJ 포인트컷 언어는 Spring에서 지원되지 않는 추가 포인트컷 지정자(<code>call</code>, <code>get</code>, <code>set</code>, <code>preinitialization</code>, <code>staticinitialization</code>, <code>initialization</code>, <code>handler</code>, <code>adviceexecution</code>, <code>withincode</code>, <code>cflow</code>, <code>cflowbelow</code>, <code>if</code>, <code>@this</code> 및 <code>@withincode</code>)를 지원합니다. Spring AOP에 의해 해석된(interpret) 포인트컷 표현식에서 이러한 포인트컷 지정자(designator)를 사용하면 <code>IllegalArgumentException</code>이 발생합니다.</p>
<p>Spring AOP가 지원하는 포인트컷 지정자 세트는 향후 릴리스에서 더 많은 AspectJ 포인트컷 지정자를 지원하도록 확장될 수 있습니다.</p>
</blockquote>
<p>Spring AOP는 메소드 실행 조인포인트로만 매칭을 제한하기 때문에, 포인트컷 지정자에 대한 이전 논의는 AspectJ 프로그래밍 가이드에서 찾을 수 있는 것보다 더 좁은 정의를 제공합니다. 또한 AspectJ 자체에는 타입 기반 의미 체계(semantic)가 있으며 실행 조인 포인트에서 <code>this</code>와 <code>target</code>은 모두 동일한 객체, 즉 메서드를 실행하는 객체를 참조합니다. Spring AOP는 프록시 기반 시스템이며 프록시 객체 자체(<code>this</code>에 바인딩됨)와 프록시 뒤에 있는 대상 객체(<code>target</code>에 바인딩됨)를 구별합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring AOP 프레임워크의 프록시 기반 특성으로 인해, 대상 객체 내의 호출은, 정의에 따라, 차단(intercepted)되지 않습니다. JDK 프록시의 경우 프록시에 대한 public 인터페이스 메서드 호출만 가로챌 수 있습니다. CGLIB를 사용하면 프록시에 대한 public 및 protected 메서드 호출이 차단(intercepted)됩니다(필요한 경우 package-visible 메서드도 포함). 그러나 프록시를 통한 일반적인 상호 작용은 항상 public 시그니처를 통해 설계되어야 합니다.</p>
<p>포인트컷 정의는 일반적으로 가로채는(intercepted) 메소드와 일치합니다. 포인트컷이 엄밀히 public-only 이어야 한다면, 프록시를 통한 잠재적인 non-public 상호 작용이 있는 CGLIB 프록시 시나리오에서도 그에 따라 정의해야 합니다.</p>
<p>차단(interception)에 대상 클래스 내의 메서드 호출이나 생성자가 포함되어야 하는 경우, Spring의 프록시 기반 AOP 프레임워크 대신 Spring 기반 <a href="https://docs.spring.io/spring-framework/reference/core/aop/using-aspectj.html#aop-aj-ltw">네이티브 AspectJ 위빙</a> 사용을 고려하세요. 이는 서로 다른 특성을 지닌 서로 다른 AOP 사용 모드를 구성(constitute)하므로, 결정을 내리기 전에 위빙에 익숙해지도록 하십시오.</p>
</blockquote>
<p>Spring AOP는 <code>bean</code>이라는 추가 PCD도 지원합니다. 이 PCD를 사용하면 조인 포인트 일치를 특정 이름의 Spring 빈 또는 이름이 지정된 Spring 빈 세트(와일드카드 사용 시)로 제한할 수 있습니다. <code>bean</code> PCD의 형식은 다음과 같습니다.</p>
<pre><code class="language-null">bean(idOrNameOfBean)</code></pre>
<p><code>idOrNameOfBean</code> 토큰은 모든 Spring 빈의 이름이 될 수 있습니다. <code>* </code>문자를 사용하는 제한된 와일드카드 지원이 제공되므로, Spring 빈에 대한 일부 명명 규칙을 설정하면 <code>bean</code> PCD 표현식을 작성하여 선택할 수 있습니다. 다른 포인트컷 지정자의 경우와 마찬가지로, <code>bean</code> PCD는 <code>&amp;&amp;</code>(and), <code>||</code> (or) 및 <code>!</code> (부정) 연산자와 함께 사용될 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>bean</code> PCD는 Spring AOP에서만 지원되며 native AspectJ 위빙에서는 지원되지 않습니다. 이는 AspectJ가 정의하는 표준 PCD에 대한 Spring 특정 확장이므로, <code>@Aspect</code> 모델에 선언된 aspect에는 사용할 수 없습니다.</p>
<p><code>bean</code> PCD는 타입 수준(위빙 기반 AOP가 제한됨)보다는 인스턴스 수준(Spring 빈 이름 개념을 기반으로 구축)에서 작동합니다. 인스턴스 기반 포인트컷 지정자는 Spring의 프록시 기반 AOP 프레임워크의 특수 기능이며 Spring 빈 팩토리와의 긴밀한 통합으로, 특정 빈을 이름으로 식별하는 것이 자연스럽고 간단합니다.</p>
</blockquote>
<h1 id="combining-pointcut-expressions">Combining Pointcut Expressions</h1>
<p><code>&amp;&amp;</code>, <code>||</code>, 그리고 <code>!</code>를 사용하여 포인트컷 표현식을 결합(combine)할 수 있습니다. 이름으로 포인트컷 표현식을 참조(refer to)할 수도 있습니다. 다음 예에서는 세 가지 포인트컷 표현식을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>xyz</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Pointcuts</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"execution(public * *(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">publicMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">// (1)</span>

	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"within(com.xyz.trading..*)"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">inTrading</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">// (2)</span>

	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"publicMethod() &amp;&amp; inTrading()"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">tradingOperation</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span> <span class="token comment">// (3)</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>publicMethod</code>는 메서드 실행 조인 포인트가 public 메서드의 실행을 나타내는 경우 일치합니다.<br>
(2) <code>inTrading</code>은 메서드 실행이 거래 모듈에 있는 경우 일치합니다.<br>
(3) <code>tradingOperation</code>은 메서드 실행이 거래 모듈의 public 메서드를 나타내는 경우 일치합니다.</p>
<p>위에 표시된 것처럼 더 작은 named 포인트컷으로 더 복잡한 포인트컷 표현식을 작성하는 것이 가장 좋습니다. 이름으로 포인트컷을 참조할 때, 일반적인 Java 가시성(visibility) 규칙이 적용됩니다(동일한 타입의 <code>private</code> 포인트컷, 계층 구조의 <code>protected</code> 포인트컷, 어디서나 <code>public</code> 포인트컷 등을 볼 수 있음). 가시성(visibility)은 포인트컷 일치에 영향을 주지 않습니다.</p>
<h1 id="sharing-named-pointcut-definitions">Sharing Named Pointcut Definitions</h1>
<p>엔터프라이즈 애플리케이션으로 작업할 때, 개발자는 여러 aspect에서 애플리케이션 모듈과 특정 작업 세트를 참조해야 하는 경우가 많습니다. 이 목적을 위해 일반적으로 사용되는 명명된 포인트컷 표현식을 캡처하는 전용 클래스를 정의하는 것이 좋습니다. 이러한 클래스는 일반적으로 다음 <code>CommonPointcuts</code> 예제와 유사합니다(클래스 이름은 사용자에게 달려 있습니다).</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>xyz</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Pointcut</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CommonPointcuts</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * A join point is in the web layer if the method is defined
	 * in a type in the com.xyz.web package or any sub-package
	 * under that.
	 */</span>
	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"within(com.xyz.web..*)"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">inWebLayer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

	<span class="token comment">/**
	 * A join point is in the service layer if the method is defined
	 * in a type in the com.xyz.service package or any sub-package
	 * under that.
	 */</span>
	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"within(com.xyz.service..*)"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">inServiceLayer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

	<span class="token comment">/**
	 * A join point is in the data access layer if the method is defined
	 * in a type in the com.xyz.dao package or any sub-package
	 * under that.
	 */</span>
	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"within(com.xyz.dao..*)"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">inDataAccessLayer</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

	<span class="token comment">/**
	 * A business service is the execution of any method defined on a service
	 * interface. This definition assumes that interfaces are placed in the
	 * "service" package, and that implementation types are in sub-packages.
	 *
	 * If you group service interfaces by functional area (for example,
	 * in packages com.xyz.abc.service and com.xyz.def.service) then
	 * the pointcut expression "execution(* com.xyz..service.*.*(..))"
	 * could be used instead.
	 *
	 * Alternatively, you can write the expression using the 'bean'
	 * PCD, like so "bean(*Service)". (This assumes that you have
	 * named your Spring service beans in a consistent fashion.)
	 */</span>
	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz..service.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">businessService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

	<span class="token comment">/**
	 * A data access operation is the execution of any method defined on a
	 * DAO interface. This definition assumes that interfaces are placed in the
	 * "dao" package, and that implementation types are in sub-packages.
	 */</span>
	<span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">dataAccessOperation</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

<span class="token punctuation">}</span></code></pre>
<p><code>@Pointcut</code> 메서드 이름과 결합된(combined) 클래스의 정규화된(fully-qualified) 이름을 참조하여 포인트컷 표현식이 필요한 곳 어디에서나 그러한 클래스에 정의된 포인트컷을 참조할 수 있습니다. 예를 들어, 서비스 계층을 트랜잭션 가능하게 만들려면 pointcut이라는 <code>com.xyz.CommonPointcuts.businessService()</code>를 참조하는 다음을 작성할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>advisor</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.CommonPointcuts.businessService()<span class="token punctuation">"</span></span>
		<span class="token attr-name">advice-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tx-advice<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>advice</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tx-advice<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>attributes</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>method</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*<span class="token punctuation">"</span></span> <span class="token attr-name">propagation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>REQUIRED<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">tx:</span>attributes</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">tx:</span>advice</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>&lt;aop:config&gt;</code> 및 <code>&lt;aop:advisor&gt;</code> 요소는 <a href="https://docs.spring.io/spring-framework/reference/core/aop/schema.html">스키마 기반 AOP 지원에서 논의</a>됩니다. 트랜잭션 요소는 <a href="https://docs.spring.io/spring-framework/reference/data-access/transaction.html">트랜잭션 관리</a>에서 논의됩니다.</p>
<h1 id="examples">Examples</h1>
<p>Spring AOP 사용자는 <code>execution</code> 포인트컷 지정자를 가장 자주 사용할 가능성이 높습니다. execution 표현식의 형식은 다음과 같습니다.</p>
<pre><code class="language-null">execution(modifiers-pattern?
			ret-type-pattern
			declaring-type-pattern?name-pattern(param-pattern)
			throws-pattern?)</code></pre>
<p>반환 타입 패턴(이전 코드 조각의 <code>ret-type-pattern</code>), 이름 패턴 및 매개 변수 패턴을 제외한 모든 부분은 선택 사항입니다. 반환 타입 패턴은 조인 포인트가 일치하기 위해 필요한 메서드의 반환 타입을 결정합니다. <code>*</code>는 반환 타입 패턴으로 가장 자주 사용됩니다. 모든 반환 타입과 일치합니다. 정규화된(fully-qualified) 타입 이름은 메서드가 지정된 타입을 반환하는 경우에만 일치합니다. 이름 패턴은 메서드 이름과 일치합니다. <code>* </code>와일드카드를 이름 패턴의 전부 또는 일부로 사용할 수 있습니다. 선언 타입 패턴을 지정하는 경우, 후행 <code>.</code> 이름 패턴 컴포넌트에 결합합니다. 매개변수 패턴은 약간 더 복잡합니다. <code>()</code>는 매개변수를 사용하지 않는 메서드와 일치하는 반면, <code>(..)</code>는 임의 개수(0개 이상)의 매개변수와 일치합니다. <code>(*)</code> 패턴은 모든 타입의 매개변수 하나를 사용하는 메소드와 일치합니다. <code>(*,String)</code>은 두 개의 매개 변수를 사용하는 메서드와 일치합니다. 첫 번째는 모든 타입이 될 수 있지만 두 번째는 <code>String</code>이어야 합니다. 자세한 내용은 AspectJ 프로그래밍 가이드의 <a href="https://www.eclipse.org/aspectj/doc/released/progguide/semantics-pointcuts.html">언어 의미 섹션</a>을 참조하세요.</p>
<p>다음 예는 몇 가지 일반적인 포인트컷 표현식을 보여줍니다.</p>
<ul>
<li>public 메소드 실행:</li>
</ul>
<pre><code class="language-null">execution(public * *(..))</code></pre>
<ul>
<li><code>set</code>으로 시작하는 이름을 가진 메소드 실행:</li>
</ul>
<pre><code class="language-null">execution(* set*(..))</code></pre>
<ul>
<li><code>AccountService</code> 인터페이스에 의해 정의된 메소드의 실행:</li>
</ul>
<pre><code class="language-null">execution(* com.xyz.service.AccountService.*(..))</code></pre>
<ul>
<li><code>service</code> 패키지에 정의된 메소드 실행:</li>
</ul>
<pre><code class="language-null">execution(* com.xyz.service.*.*(..))</code></pre>
<ul>
<li>서비스 패키지 또는 해당 하위 패키지 중 하나에 정의된 메서드 실행:</li>
</ul>
<pre><code class="language-null">execution(* com.xyz.service..*.*(..))</code></pre>
<ul>
<li>서비스 패키지 내의 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">within(com.xyz.service.*)</code></pre>
<ul>
<li>서비스 패키지 또는 해당 하위 패키지 중 하나 내의 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">within(com.xyz.service..*)</code></pre>
<ul>
<li>프록시가 <code>AccountService</code> 인터페이스를 구현하는 모든 조인 포인트(Spring AOP에서만 메소드 실행):</li>
</ul>
<pre><code class="language-null">this(com.xyz.service.AccountService)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>this</code>는 바인딩 형식(binding form)에서 더 일반적으로 사용됩니다. 어드바이스 본문에서 프록시 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언</a> 섹션을 참조하세요.</p>
</blockquote>
<ul>
<li>대상 객체가 <code>AccountService</code> 인터페이스를 구현하는 모든 조인 포인트(Spring AOP에서만 메소드 실행):</li>
</ul>
<pre><code class="language-null">target(com.xyz.service.AccountService)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>target</code>은 바인딩 형식(binding form)에서 더 일반적으로 사용됩니다. 어드바이스 본문에서 대상 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요.</p>
</blockquote>
<ul>
<li>단일 매개변수를 취하고 런타임에 전달된 인수가 <code>Serializable</code>인 모든 조인포인트(Spring AOP에서만 메소드 실행):</li>
</ul>
<pre><code class="language-null">args(java.io.Serializable)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>args</code>는 바인딩 형식에서 더 일반적으로 사용됩니다. 어드바이스 본문에서 메서드 인수를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요.</p>
</blockquote>
<p>이 예제에 제공된 포인트컷은 <code>execution(* *(java.io.Serialized))</code>과 다릅니다. args 버전은 런타임에 전달된 인수가 <code>Serializable</code>이면 일치하고, 메서드 시그니처가 <code>Serializable</code> 타입의 단일 매개 변수를 선언하면 execution 버전이 일치합니다.</p>
<ul>
<li>대상 객체에 <code>@Transactional</code> 어노테이션이 있는 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">@target(org.springframework.transaction.annotation.Transactional)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
바인딩 형식에서 <code>@target</code>을 사용할 수도 있습니다. 어드바이스 본문에서 어노테이션 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요.</p>
</blockquote>
<ul>
<li>대상 객체의 선언된 타입에 <code>@Transactional</code> 어노테이션이 있는 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">@within(org.springframework.transaction.annotation.Transactional)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
바인딩 형식에서 <code>@within</code>을 사용할 수도 있습니다. 어드바이스 본문에서 어노테이션 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요.</p>
</blockquote>
<ul>
<li>실행 메서드에 <code>@Transactional</code> 어노테이션이 있는 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">@annotation(org.springframework.transaction.annotation.Transactional)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
바인딩 형식에서 <code>@annotation</code>을 사용할 수도 있습니다. 어드바이스 본문에서 어노테이션 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요.</p>
</blockquote>
<ul>
<li>단일 매개변수를 취하고 전달된 인수의 런타임 타입에 <code>@Classified</code> 어노테이션이 있는 모든 조인 포인트(Spring AOP에서만 메소드 실행):</li>
</ul>
<pre><code class="language-null">@args(com.xyz.security.Classified)</code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
바인딩 형식에서 <code>@args</code>를 사용할 수도 있습니다. 어드바이스 본문에서 어노테이션 객체를 사용 가능하게 만드는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html">어드바이스 선언 섹션</a>을 참조하세요. </p>
</blockquote>
<ul>
<li><code>tradeService</code>라는 Spring Bean의 모든 조인 포인트(Spring AOP에서만 메서드 실행):</li>
</ul>
<pre><code class="language-null">bean(tradeService)</code></pre>
<ul>
<li>와일드카드 표현식 <code>*Service</code> 과 일치하는 이름을 가진 Spring Bean의 모든 조인 포인트(Spring AOP에서만 메소드 실행) :</li>
</ul>
<pre><code class="language-null">bean(*Service)</code></pre>
<h1 id="writing-good-pointcuts">Writing Good Pointcuts</h1>
<p>컴파일하는 동안 AspectJ는 매칭 성능을 최적화하기 위해 포인트컷을 처리합니다. 코드를 검사하고 각 조인 포인트가 주어진 포인트컷과 일치하는지(정적으로 또는 동적으로) 결정하는 것은 비용이 많이 드는 프로세스입니다. (동적 일치는 정적 분석에서 일치를 완전히 확인할 수 없으며 코드가 실행될 때 실제 일치가 있는지 확인하기 위해 코드에 테스트가 배치됨을 의미합니다.) 처음으로 포인트컷 선언을 만나면 AspectJ는 이를 매칭 프로세스에 대한 최적의 형식으로 다시 작성합니다. 이것은 무엇을 의미 하는가? 기본적으로 포인트컷은 DNF(Disjunctive Normal Form)로 다시 작성되고 포인트컷의 컴포넌트는 평가 비용이 더 저렴한 컴포넌트를 먼저 확인하도록 정렬됩니다. 이는 다양한 포인트컷 지정자의 성능을 이해하는 데 대해 걱정할 필요가 없으며 포인트컷 선언에서 순서에 관계없이 이를 제공할 수 있음을 의미합니다.</p>
<p>그러나 AspectJ는 지시된 대로만 작동할 수 있습니다. 최적의 매칭 성능을 위해서는, 달성하려는 것이 무엇인지 생각하고 정의에서 일치 항목에 대한 검색 공간을 최대한 좁혀야 합니다. 기존 지정자는 자연스럽게 종류 지정(kinded), 범위 지정(scoping) 및 상황 지정(contextual)의 세 그룹 중 하나에 속합니다.</p>
<ul>
<li>
<p>kinded 지정자는 특정 종류의 조인 포인트(<code>execution</code>, <code>get</code>, <code>set</code>, <code>call</code> 및 <code>handler</code>)를 선택합니다.</p>
</li>
<li>
<p>scopint 지정자는 관심사의 조인 포인트 그룹(아마도 다양한 종류)을 선택합니다: <code>withincode</code> 및 <code>within</code></p>
</li>
<li>
<p>contextual 지정자는 컨텍스트(<code>this</code>, <code>target</code> 및 <code>@annotation</code>)를 기반으로 일치(및 선택적으로 바인딩)됩니다.</p>
</li>
</ul>
<p>잘 작성된 포인트컷에는 최소한 처음 두 가지 타입(kinded 및 scoping)이 포함되어야 합니다. 조인 포인트 컨텍스트를 기반으로 일치하도록 컨텍스트 지정자를 포함하거나 어드바이스에 사용하기 위해 해당 컨텍스트를 바인딩할 수 있습니다. kinded 지정자만 제공하거나 contextual 지정자만 제공하면, 작동하지만, 추가 처리 및 분석으로 인해 weaving 성능(사용된 시간 및 메모리)에 영향을 미칠 수 있습니다. scoping 지정자는 일치하는 속도가 매우 빠르며 이를 사용하면 AspectJ가 더 이상 처리해서는 안 되는 조인 포인트 그룹을 매우 빠르게 무시(dismiss)할 수 있습니다. 좋은 포인트컷은 가능하다면 항상 포인트컷을 포함해야 합니다.</p>