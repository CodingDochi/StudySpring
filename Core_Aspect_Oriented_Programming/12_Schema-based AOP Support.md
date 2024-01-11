<p>XML 기반 형식을 선호한다면 Spring은 <code>aop</code> 네임스페이스 태그를 사용하여 측면을 정의하는 지원도 제공합니다. <code>@AspectJ</code> 스타일을 사용할 때와 똑같은 포인트컷 표현식과 advice 종류가 지원됩니다. 따라서 이 섹션에서는 해당 syntax에 중점을 두고 포인트컷 표현식 작성 및 advice 매개변수 바인딩에 대한 이해를 위해 이전 섹션(<a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj.html">@AspectJ 지원</a>)의 논의를 독자에게 안내합니다.</p>
<p>이 섹션에 설명된 aop 네임스페이스 태그를 사용하려면 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/xsd-schemas.html">XML 스키마 기반 구성</a>에 설명된 대로 <code>spring-aop</code> 스키마를 가져와야 합니다. <code>aop</code> 네임스페이스에서 태그를 가져오는 방법은 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/xsd-schemas.html#aop">AOP 스키마</a>를 참조하세요.</p>
<p>Spring 구성 내에서 모든 aspect 및 advisor 요소는 <code>&lt;aop:config&gt;</code> 요소 내에 배치되어야 합니다(애플리케이션 컨텍스트 구성에 하나 이상의 <code>&lt;aop:config&gt;</code> 요소가 있을 수 있음). <code>&lt;aop:config&gt;</code> 요소는 pointcut, advisor, aspect 요소를 포함할 수 있습니다(이 순서대로 선언해야 함).</p>
<blockquote>
<p><strong>[Warning]</strong><br>
<code>&lt;aop:config&gt;</code> 스타일의 구성은 Spring의 <a href="https://docs.spring.io/spring-framework/reference/core/aop-api/autoproxy.html">자동 프록시</a> 메커니즘을 많이 사용합니다. 이미 <code>BeanNameAutoProxyCreator</code> 또는 이와 유사한 것을 사용하여 명시적인 자동 프록시를 사용하는 경우 이로 인해 문제(예: 짜여지지 않은 조언)가 발생할 수 있습니다. 권장되는 사용 패턴은 <code>&lt;aop:config&gt;</code> 스타일만 사용하거나 <code>AutoProxyCreator</code> 스타일만 사용하고 절대 혼합하지 않는 것입니다.</p>
</blockquote>
<h1 id="declaring-an-aspect">Declaring an Aspect</h1>
<p>스키마 지원을 사용할 때 aspect은 Spring 애플리케이션 컨텍스트에서 Bean으로 정의된 일반 Java 객체입니다. 상태와 동작은 객체의 필드와 메소드에 캡처되고 포인트컷과 advice 정보는 XML에 캡처됩니다.</p>
<p>다음 예제와 같이 <code>&lt;aop:aspect&gt;</code> 요소를 사용하여aspect을 선언하고 <code>ref</code> 속성을 사용하여 지원 Bean을 참조할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myAspect<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>물론 aspect을 지원하는 Bean(이 경우 <code>aBean</code>)은 다른 Spring Bean과 마찬가지로 구성하고 종속성을 주입할 수 있습니다.</p>
<h1 id="declaring-a-pointcut">Declaring a Pointcut</h1>
<p><code>&lt;aop:config&gt;</code> 요소 내부에 명명된 포인트컷을 선언하여 포인트컷 정의가 여러 aspect과 어드바이저에서 공유되도록 할 수 있습니다.</p>
<p>서비스 계층에서 비즈니스 서비스의 실행을 나타내는 포인트컷은 다음과 같이 정의할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
		<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..))<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>포인트컷 표현 자체는 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj.html">@AspectJ 지원</a>에 설명된 것과 동일한 AspectJ 포인트컷 표현 언어를 사용한다는 점에 유의하세요. 스키마 기반 선언 스타일을 사용하는 경우 포인트컷 표현식 내의 <code>@Aspect</code> type에 정의된 명명된 포인트컷을 참조할 수도 있습니다. 따라서 위의 포인트컷을 정의하는 또 다른 방법은 다음과 같습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
		<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.CommonPointcuts.businessService()<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span> <span class="token comment">&lt;!--(1)--&gt;</span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>(1) <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-common-pointcuts">명명된 Pointcut 정의 공유</a>에 정의된 pointcut이라는 이름의 <code>businessService</code>를 참조합니다. </p>
<p>aspect 내부에 포인트컷을 선언하는 것은 다음 예제에서 볼 수 있듯이 최상위 수준 포인트컷을 선언하는 것과 매우 유사합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myAspect<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
			<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..))<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>@AspectJ 관점과 거의 같은 방식으로, 스키마 기반 정의 스타일을 사용하여 선언된 포인트컷은 조인 포인트 컨텍스트를 수집할 수 있습니다. 예를 들어, 다음 포인트컷은 조인 포인트 컨텍스트로 <code>this</code> 객체를 수집하고 이를 어드바이스에 전달합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myAspect<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
			<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..)) <span class="token entity">&amp;amp;</span><span class="token entity">&amp;amp;</span> this(service)<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span> <span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>monitor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음과 같이 일치하는 이름의 매개 변수를 포함하여 수집된 조인 포인트 컨텍스트를 수신하도록 advice을 선언해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">monitor</span><span class="token punctuation">(</span><span class="token class-name">Object</span> service<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>포인트컷 하위 표현식을 결합할 때 <code>&amp;&amp;</code>는 XML 문서 내에서 어색하므로 <code>&amp;&amp;</code>, <code>||</code> 및 <code>!</code> 대신 각각 <code>and</code>, <code>or</code> 및 <code>not</code> 키워드를 사용할 수 있습니다. 예를 들어 이전 포인트컷은 다음과 같이 더 잘 작성할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myAspect<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
			<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..)) and this(service)<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span> <span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>monitor<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		...
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이런 방식으로 정의된 포인트컷은 XML <code>id</code>로 참조되며 복합 포인트컷을 형성하기 위해 명명된 포인트컷으로 사용될 수 없습니다. 따라서 스키마 기반 정의 스타일의 명명된 포인트컷 지원은 @AspectJ 스타일에서 제공하는 것보다 더 제한적입니다.</p>
<h1 id="declaring-advice">Declaring Advice</h1>
<p>스키마 기반 AOP 지원은 @AspectJ 스타일과 동일한 5가지 종류의 조언을 사용하며 정확히 동일한 의미를 갖습니다.</p>
<h2 id="before-advice">Before Advice</h2>
<p>일치하는 메서드 실행 전에 조언이 실행되기 전입니다. 다음 예제와 같이 <code>&lt;aop:before&gt;</code> 요소를 사용하여 <code>&lt;aop:aspect&gt;</code> 내부에 선언됩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beforeExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span>
		<span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataAccessOperation<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doAccessCheck<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>위의 예에서 <code>dataAccessOperation</code>은 최상위(<code>&lt;aop:config&gt;</code>) 레벨에서 정의된 명명된 포인트컷의 <code>id</code>입니다(<a href="https://docs.spring.io/spring-framework/reference/core/aop/schema.html#aop-schema-pointcuts">포인트컷 선언</a> 참조).</p>
<blockquote>
<p><strong>[Note]</strong><br>
@AspectJ 스타일에 대한 논의에서 언급했듯이 명명된 포인트컷을 사용하면 코드의 가독성을 크게 향상시킬 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-common-pointcuts">명명된 Pointcut 정의 공유</a>를 참조하세요.</p>
</blockquote>
<p>대신에 포인트컷을 인라인으로 정의하려면 다음과 같이 <code>pointcut-ref</code> 속성을 <code>pointcut</code> 속성으로 바꾸십시오.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>beforeExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doAccessCheck<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>method</code> 속성은 advice의 본문을 제공하는 메소드(<code>doAccessCheck</code>)를 식별합니다. 이 메소드는 조언을 포함하는 aspect 요소가 참조하는 Bean에 대해 정의되어야 합니다. 데이터 액세스 작업(포인트컷 표현식과 일치하는 메서드 실행 조인 포인트)이 수행되기 전에 aspect 빈의 <code>doAccessCheck</code> 메서드가 호출됩니다.</p>
<h2 id="after-returning-advice">After Returning Advice</h2>
<p>advice을 반환한 후 일치하는 메서드 실행이 정상적으로 완료되면 실행됩니다. 이전 advice과 같은 방식으로 <code>&lt;aop:aspect&gt;</code> 내부에 선언됩니다. 다음 예에서는 이를 선언하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>afterReturningExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>after-returning</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doAccessCheck<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>@AspectJ 스타일과 마찬가지로 advice 본문 내에서 반환 값을 얻을 수 있습니다. 이렇게 하려면 다음 예제와 같이 <code>returning</code> 특성(attribute)을 사용하여 반환 값이 전달되어야 하는 매개 변수의 이름을 지정합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>afterReturningExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>after-returning</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">returning</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>retVal<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doAccessCheck<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>doAccessCheck</code> 메소드는 <code>retVal</code>이라는 매개변수를 선언해야 합니다. 이 매개변수의 type은 <code>@AfterReturning</code>에 대해 설명된 것과 동일한 방식으로 일치를 제한합니다. 예를 들어 다음과 같이 메서드 시그니처를 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAccessCheck</span><span class="token punctuation">(</span><span class="token class-name">Object</span> retVal<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span></code></pre>
<h2 id="after-throwing-advice">After Throwing Advice</h2>
<p>던진 후 advice은 예외를 던지면서 일치하는 메서드 실행이 종료될 때 실행됩니다. 다음 예제와 같이<code>after-throwing</code> 요소를 사용하여 <code>&lt;aop:aspect&gt;</code> 내부에 선언됩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>afterThrowingExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>after-throwing</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doRecoveryActions<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>@AspectJ 스타일에서처럼 어드바이스 본문 내에서 던져진 예외를 얻을 수 있습니다. 이렇게 하려면 다음 예제와 같이 <code>throwing</code> 속성(attribute)을 사용하여 예외를 전달해야 하는 매개변수의 이름을 지정합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>afterThrowingExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>after-throwing</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">throwing</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataAccessEx<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doRecoveryActions<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>doRecoveryActions</code> 메소드는 <code>dataAccessEx</code>라는 매개변수를 선언해야 합니다. 이 매개변수의 type은 <code>@AfterThrowing</code>에 대해 설명한 것과 동일한 방식으로 일치를 제한합니다. 예를 들어 메소드 시그니처는 다음과 같이 선언될 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doRecoveryActions</span><span class="token punctuation">(</span><span class="token class-name">DataAccessException</span> dataAccessEx<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span></code></pre>
<h2 id="after-finally-advice">After (Finally) Advice</h2>
<p>after (finally) 어드바이스는 일치하는 메소드 실행이 어떻게 종료되는지에 관계없이 실행됩니다. 다음 예제와 같이 <code>after</code> 요소를 사용하여 이를 선언할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>afterFinallyExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>after</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.dao.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doReleaseLock<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="around-advice">Around Advice</h2>
<p>마지막 종류의 advice은 around adivce 것입니다. Around 어드바이스는 일치하는 메서드 실행 "주변"으로 실행합니다. 메서드 실행 전후에 작업을 수행하고 메서드가 실제로 실행되는 시기, 방법, 심지어 실행 여부를 결정할 수 있는 기회가 있습니다. 스레드로부터 안전한 방식으로 메서드 실행 전후에 상태를 공유해야 하는 경우(예: 타이머 시작 및 중지) Around 어드바이스가 자주 사용됩니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
항상 귀하의 요구 사항을 충족하는 가장 강력한 형태의 advice을 사용하십시오.</p>
<p>예를 들어, 사전 조언이 귀하의 요구 사항에 충분하다면 주변 조언을 사용하지 마십시오.</p>
</blockquote>
<p><code>aop:around</code> 요소를 사용하여 around 어드바이스를 선언할 수 있습니다. advice 메소드는 <code>Object</code>를 반환 type으로 선언해야 하며 메소드의 첫 번째 매개변수는 <code>ProceedingJoinPoint</code> type이어야 합니다. advice 메소드의 본문 내에서 기본 메소드를 실행하려면 <code>ProceedingJoinPoint</code>에서 <code>proceed()</code>를 호출해야 합니다. 인수 없이 <code>proceed()</code>를 호출하면 호출 시 호출자의 원래 인수가 기본 메서드에 제공됩니다. 고급 사용 사례의 경우 인수 배열(<code>Object[]</code>)을 허용하는 <code>process()</code> 메서드의 오버로드된 변형이 있습니다. 배열의 값은 호출될 때 기본 메서드에 대한 인수로 사용됩니다. <code>Object[]</code>를 사용하여 호출 <code>proceed</code>에 대한 참고사항은 <code>Around Advice</code>를 참조하세요.</p>
<p>다음 예는 XML에서 어드바이스를 선언하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aroundExample<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>aBean<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>around</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..))<span class="token punctuation">"</span></span>
		<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doBasicProfiling<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	...
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>doBasicProfiling</code> 어드바이스의 구현은 다음 예제에서 볼 수 있듯이 @AspectJ 예제(물론 주석 제외)와 정확히 동일할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">doBasicProfiling</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> pjp<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
	<span class="token comment">// start stopwatch</span>
	<span class="token class-name">Object</span> retVal <span class="token operator">=</span> pjp<span class="token punctuation">.</span><span class="token function">proceed</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// stop stopwatch</span>
	<span class="token keyword">return</span> retVal<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="advice-parameters">Advice Parameters</h2>
<p>스키마 기반 선언 스타일은 @AspectJ 지원에 대해 설명된 것과 동일한 방식으로 완전한 type의 advice을 지원합니다. 즉, advice 메소드 매개변수에 대해 이름별로 포인트컷 매개변수를 일치시키는 것입니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html#aop-ataspectj-advice-params">advice 매개변수</a>를 참조하세요. advice 메소드에 대한 인수 이름을 명시적으로 지정하려면(앞에서 설명한 탐지 전략에 의존하지 않고) <code>argNames</code> 속성(attribute)과 동일한 방식으로 처리되는 advice 어노테이션의 <code>arg-names</code> 속성(attribyte)을 사용하여 그렇게 할 수 있습니다. (<a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html#aop-ataspectj-advice-params-names">인수 이름 결정</a>에 설명된 대로) 다음 예에서는 XML에서 인수 이름을 지정하는 방법을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span>
	<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.Pointcuts.publicMethod() and @annotation(auditable)<span class="token punctuation">"</span></span> <span class="token attr-name">&lt;!--</span> <span class="token attr-name">(1)</span> <span class="token attr-name">--</span><span class="token punctuation">&gt;</span></span>
method="audit"
arg-names="auditable" /&gt;</code></pre>
<p>(1) <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-pointcuts-combining">포인트컷 표현식 결합</a>에 정의된 pointcut이라는 <code>publicMethod</code>를 참조합니다.</p>
<p><code>arg-names</code> 속성은 쉼표로 구분된 매개변수 이름 목록을 허용합니다.</p>
<p>XSD 기반 접근 방식에 대해 약간 더 관련된 다음 예는 여러 강력한 type의 매개 변수와 함께 사용되는 몇 가지 주변 advice을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>xyz<span class="token punctuation">.</span>service</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">PersonService</span> <span class="token punctuation">{</span>

	<span class="token class-name">Person</span> <span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token class-name">String</span> personName<span class="token punctuation">,</span> <span class="token keyword">int</span> age<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultPersonService</span> <span class="token keyword">implements</span> <span class="token class-name">PersonService</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Person</span> <span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token keyword">int</span> age<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> age<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>다음은 aspect이다. <code>profile(..)</code> 메소드는 강력한 type의 매개변수를 허용하며, 그 중 첫 번째 매개변수는 메소드 호출을 proceed하는 데 사용되는 조인 포인트라는 사실에 주목하세요. 이 매개변수의 존재는 다음 예에서 볼 수 있듯이 <code>profile(..)</code>이 <code>around</code> 어드바이스로 사용됨을 나타냅니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>xyz</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang</span><span class="token punctuation">.</span><span class="token class-name">ProceedingJoinPoint</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util</span><span class="token punctuation">.</span><span class="token class-name">StopWatch</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleProfiler</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">profile</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> call<span class="token punctuation">,</span> <span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token keyword">int</span> age<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
		<span class="token class-name">StopWatch</span> clock <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">StopWatch</span><span class="token punctuation">(</span><span class="token string">"Profiling for '"</span> <span class="token operator">+</span> name <span class="token operator">+</span> <span class="token string">"' and '"</span> <span class="token operator">+</span> age <span class="token operator">+</span> <span class="token string">"'"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">try</span> <span class="token punctuation">{</span>
			clock<span class="token punctuation">.</span><span class="token function">start</span><span class="token punctuation">(</span>call<span class="token punctuation">.</span><span class="token function">toShortString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token keyword">return</span> call<span class="token punctuation">.</span><span class="token function">proceed</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">finally</span> <span class="token punctuation">{</span>
			clock<span class="token punctuation">.</span><span class="token function">stop</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>clock<span class="token punctuation">.</span><span class="token function">prettyPrint</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 다음 예제 XML 구성은 특정 조인 포인트에 대한 이전 advice의 실행에 영향을 미칩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>aop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/aop<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- this is the object that will be proxied by Spring's AOP infrastructure --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>personService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.DefaultPersonService<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token comment">&lt;!-- this is the actual advice itself --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>profiler<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.SimpleProfiler<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>profiler<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>theExecutionOfSomePersonServiceMethod<span class="token punctuation">"</span></span>
				<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.PersonService.getPerson(String,int))
				and args(name, age)<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>around</span> <span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>theExecutionOfSomePersonServiceMethod<span class="token punctuation">"</span></span>
				<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>profile<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 드라이버 스크립트를 고려하십시오.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Boot</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">PersonService</span> person <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">PersonService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		person<span class="token punctuation">.</span><span class="token function">getPerson</span><span class="token punctuation">(</span><span class="token string">"Pengo"</span><span class="token punctuation">,</span> <span class="token number">12</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이러한 <code>Boot</code> 클래스를 사용하면 표준 출력에서 ​​다음과 유사한 출력을 얻을 수 있습니다.</p>
<pre><code class="language-null">StopWatch 'Profiling for 'Pengo' and '12': running time (millis) = 0
-----------------------------------------
ms     %     Task name
-----------------------------------------
00000  ?  execution(getFoo)</code></pre>
<h2 id="advice-ordering">Advice Ordering</h2>
<p>여러 개의 advice이 동일한 조인 포인트(실행 방법)에서 실행되어야 하는 경우 순서 규칙은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html#aop-ataspectj-advice-ordering">advice 순서</a>에 설명된 대로입니다. aspect 사이의 우선순위는 <code>&lt;aop:aspect&gt;</code> 요소의 <code>order</code> 속성을 통해 결정되거나 aspect을 뒷받침하는 빈에 <code>@Order</code> annotation을 추가하거나 빈이 <code>Ordered</code> 인터페이스를 구현하도록 함으로써 결정됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
동일한 <code>@Aspect</code> 클래스에 정의된 어드바이스 메소드에 대한 우선순위 규칙과 달리, 동일한 <code>&lt;aop:aspect&gt;</code> 요소에 정의된 두 개의 어드바이스가 모두 동일한 조인 포인트에서 실행되어야 하는 경우 우선순위는 다음 순서에 따라 결정됩니다. 조언 요소는 둘러싸는 <code>&lt;aop:aspect&gt;</code> 요소 내에서 가장 높은 우선순위부터 가장 낮은 우선순위로 선언됩니다.</p>
<p>예를 들어, 동일한 조인 포인트에 적용되는 동일한 <code>&lt;aop:aspect&gt;</code> 요소에 정의된 <code>around</code> 어드바이스와 <code>before</code> 어드바이스가 주어지면 <code>around</code> 어드바이스가 <code>before</code> 어드바이스보다 높은 우선순위를 갖도록 보장하기 위해 <code>&lt;aop:around&gt;</code> 요소 <code>&lt;aop:before&gt;</code> 요소 앞에 선언해야 합니다.</p>
<p>경험상, 동일한 조인 포인트에 적용되는 동일한 <code>&lt;aop:aspect&gt;</code> 요소에 여러 개의 어드바이스가 정의되어 있는 경우 각 조인 포인트당 하나의 어드바이스 메서드로 이러한 어드바이스 메서드를 축소하는 것을 고려하세요. <code>&lt;aop:aspect&gt;</code> 요소를 사용하거나 advice 조각을 aspect 수준에서 order할 수 있는 별도의 <code>&lt;aop:aspect&gt;</code> 요소로 리팩토링하세요.</p>
</blockquote>
<h1 id="introductions">Introductions</h1>
<p>Introduction(AspectJ에서는 유형 간 선언으로 알려짐)를 통해 aspect은 어드바이스된 객체가 주어진 인터페이스를 구현하고 해당 객체를 대신하여 해당 인터페이스의 구현을 제공한다는 것을 선언할 수 있습니다.</p>
<p><code>aop:aspect</code> 내에서 <code>aop:declare-parents</code> 요소를 사용하여 소개를 만들 수 있습니다. <code>aop:declare-parents</code> 요소를 사용하여 일치하는 유형에 새로운 상위(따라서 이름)가 있음을 선언할 수 있습니다. 예를 들어, <code>UsageTracked</code>라는 인터페이스와 <code>DefaultUsageTracked</code>라는 인터페이스의 구현이 주어지면 다음 <code>aspect</code>은 서비스 인터페이스의 모든 구현자가 <code>UsageTracked</code> 인터페이스도 구현함을 선언합니다. (예를 들어 JMX를 통해 통계를 노출하기 위해.)</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>usageTrackerAspect<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>usageTracking<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>declare-parents</span>
		<span class="token attr-name">types-matching</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.*+<span class="token punctuation">"</span></span>
		<span class="token attr-name">implement-interface</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.tracking.UsageTracked<span class="token punctuation">"</span></span>
		<span class="token attr-name">default-impl</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.tracking.DefaultUsageTracked<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>before</span>
		<span class="token attr-name">pointcut</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz..service.*.*(..))
			and this(usageTracked)<span class="token punctuation">"</span></span>
			<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>recordUsage<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>UsageTracking 빈</code>을 지원하는 클래스에는 다음 메소드가 포함됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">recordUsage</span><span class="token punctuation">(</span><span class="token class-name">UsageTracked</span> usageTracked<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	usageTracked<span class="token punctuation">.</span><span class="token function">incrementUseCount</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>구현될 인터페이스는 <code>implement-interface</code> 속성에 의해 결정됩니다. <code>types-matching</code> 속성의 값은 AspectJ type 패턴입니다. 일치하는 유형의 모든 Bean은 <code>UsageTracked</code> 인터페이스를 구현합니다. 이전 예제의 before advice에서 서비스 Bean은 <code>UsageTracked</code> 인터페이스의 구현으로 직접 사용될 수 있습니다. 프로그래밍 방식으로 Bean에 액세스하려면 다음을 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">UsageTracked</span> usageTracked <span class="token operator">=</span> context<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token string">"myService"</span><span class="token punctuation">,</span> <span class="token class-name">UsageTracked</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h1 id="aspect-instantiation-models">Aspect Instantiation Models</h1>
<p>스키마 정의 aspect에 대해 지원되는 유일한 인스턴스화 모델은 싱글톤 모델입니다. 다른 인스턴스화 모델은 향후 릴리스에서 지원될 수 있습니다.</p>
<h1 id="advisors">Advisors</h1>
<p>"advisors" 개념은 Spring에 정의된 AOP 지원에서 비롯되었으며 AspectJ에는 직접적으로 동등한 것이 없습니다. 조언자(advisor)는 단일 advice을 포함하는 작은 독립형 aspect과 같습니다. advice 자체는 빈으로 표현되며 <a href="https://docs.spring.io/spring-framework/reference/core/aop-api/advice.html#aop-api-advice-types">Spring의 advice type</a>에 설명된 advice 인터페이스 중 하나를 구현해야 합니다. Advisor는 AspectJ 포인트컷 표현식을 활용할 수 있습니다.</p>
<p>Spring은 <code>&lt;aop:advisor&gt;</code> 요소로 Advisor 개념을 지원합니다. Spring에서 자체 네임스페이스를 지원하는 트랜잭션 조언과 함께 사용되는 것을 가장 일반적으로 볼 수 있습니다. 다음 예에서는 권고자를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
		<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..))<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>advisor</span>
		<span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>businessService<span class="token punctuation">"</span></span>
		<span class="token attr-name">advice-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tx-advice<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>advice</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tx-advice<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>attributes</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">tx:</span>method</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>*<span class="token punctuation">"</span></span> <span class="token attr-name">propagation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>REQUIRED<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">tx:</span>attributes</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">tx:</span>advice</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이전 예제에서 사용된 <code>pointcut-ref</code> 속성(attribute)뿐만 아니라 <code>pointcut</code> 속성(attribute)을 사용하여 포인트컷 표현식 인라인을 정의할 수도 있습니다.</p>
<p>adivce이 순서에 참여할 수 있도록 advisor의 우선순위를 정의하려면 <code>order</code> 속성을 사용하여 advisor의 <code>Ordered</code> 값을 정의하십시오.</p>
<h1 id="an-aop-schema-example">An AOP Schema Example</h1>
<p>이 섹션에서는 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/example.html">AOP 예제</a>의 동시 잠금 실패 재시도 예제가 스키마 지원으로 다시 작성되었을 때 어떻게 보이는지 보여줍니다.</p>
<p>동시성 문제(예: 교착 상태 패자)로 인해 비즈니스 서비스 실행이 실패하는 경우가 있습니다. 작업을 다시 시도하면 다음 시도에서 성공할 가능성이 높습니다. 이러한 조건(충돌 해결을 위해 사용자에게 돌아갈 필요가 없는 멱등성 작업)에서 재시도하는 것이 적절한 비즈니스 서비스의 경우 클라이언트가 <code>PessimisticLockingFailureException</code>을 보지 않도록 작업을 투명하게 재시도하려고 합니다. 이는 서비스 계층의 여러 서비스를 명확하게 구분하는 요구 사항이므로 aspect을 통해 구현하는 데 이상적입니다.</p>
<p>작업을 다시 시도하고 싶기 때문에 <code>proceed</code>을 여러 번 호출할 수 있도록 <code>around</code> 어드바이스를 사용해야 합니다. 다음 목록은 기본 aspect 구현(스키마 지원을 사용하는 일반 Java 클래스)을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConcurrentOperationExecutor</span> <span class="token keyword">implements</span> <span class="token class-name">Ordered</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">final</span> <span class="token keyword">int</span> DEFAULT_MAX_RETRIES <span class="token operator">=</span> <span class="token number">2</span><span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">int</span> maxRetries <span class="token operator">=</span> DEFAULT_MAX_RETRIES<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">int</span> order <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMaxRetries</span><span class="token punctuation">(</span><span class="token keyword">int</span> maxRetries<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>maxRetries <span class="token operator">=</span> maxRetries<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">getOrder</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>order<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setOrder</span><span class="token punctuation">(</span><span class="token keyword">int</span> order<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>order <span class="token operator">=</span> order<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">doConcurrentOperation</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> pjp<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
		<span class="token keyword">int</span> numAttempts <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
		<span class="token class-name">PessimisticLockingFailureException</span> lockFailureException<span class="token punctuation">;</span>
		<span class="token keyword">do</span> <span class="token punctuation">{</span>
			numAttempts<span class="token operator">++</span><span class="token punctuation">;</span>
			<span class="token keyword">try</span> <span class="token punctuation">{</span>
				<span class="token keyword">return</span> pjp<span class="token punctuation">.</span><span class="token function">proceed</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
			<span class="token keyword">catch</span><span class="token punctuation">(</span><span class="token class-name">PessimisticLockingFailureException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
				lockFailureException <span class="token operator">=</span> ex<span class="token punctuation">;</span>
			<span class="token punctuation">}</span>
		<span class="token punctuation">}</span> <span class="token keyword">while</span><span class="token punctuation">(</span>numAttempts <span class="token operator">&lt;=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>maxRetries<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">throw</span> lockFailureException<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>애스펙트는 <code>Ordered</code> 인터페이스를 구현하므로 트랜잭션 advice보다 애스펙트의 우선순위를 더 높게 설정할 수 있습니다(재시도할 때마다 새로운 트랜잭션을 원함). <code>maxRetries</code> 및 <code>order</code> 속성(properties)은 모두 Spring에 의해 구성됩니다. 주요 작업은 around advice 메소드의 <code>doConcurrentOperation</code>에서 발생합니다. 우리는 proceed하려고 노력합니다. <code>PessimisticLockingFailureException</code>으로 인해 실패하는 경우 재시도 횟수를 모두 소진하지 않는 한 다시 시도합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이 클래스는 @AspectJ 예제에 사용된 클래스와 동일하지만 어노테이션이 제거되었습니다.</p>
</blockquote>
<p>해당 Spring 구성은 다음과 같습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspect</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>concurrentOperationRetry<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>concurrentOperationExecutor<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>idempotentOperation<span class="token punctuation">"</span></span>
			<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..))<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>around</span>
			<span class="token attr-name">pointcut-ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>idempotentOperation<span class="token punctuation">"</span></span>
			<span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>doConcurrentOperation<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>aspect</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>concurrentOperationExecutor<span class="token punctuation">"</span></span>
	<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.impl.ConcurrentOperationExecutor<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>maxRetries<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>3<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>order<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>100<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>당분간은 모든 비즈니스 서비스가 멱등적이라고 가정합니다. 그렇지 않은 경우 다음 예와 같이 <code>Idempotent</code> 어노테이션을 도입하고 해당 어노테이션을 사용하여 서비스 작업 구현에 어노테이션을 추가하여 실제로 멱등성 작업만 재시도하도록 aspect을 개선할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token comment">// marker annotation</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Idempotent</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>멱등성 작업만 재시도하는 aspect 변경에는 다음과 같이 <code>@Idempotent</code> 작업만 일치하도록 포인트컷 표현식을 정제하는 작업이 포함됩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>pointcut</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>idempotentOperation<span class="token punctuation">"</span></span>
		<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>execution(* com.xyz.service.*.*(..)) and
		@annotation(com.xyz.service.Idempotent)<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>