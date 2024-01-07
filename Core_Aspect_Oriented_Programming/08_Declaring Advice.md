<p>조언은 포인트컷 표현식과 연관되어 있으며 포인트컷과 일치하는 메소드 실행 전, 후 또는 주변에서 실행됩니다. 포인트컷 표현식은 인라인 포인트컷이거나 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-common-pointcuts">명명된 포인트컷</a>에 대한 참조일 수 있습니다.</p>
<h1 id="before-advice">Before Advice</h1>
<p><code>@Before</code> 주석을 사용하여 관점에서 사전 조언을 선언할 수 있습니다. 다음 예에서는 인라인 포인트컷 표현식을 사용합니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Before</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BeforeExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAccessCheck</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-common-pointcuts">명명된 포인트컷</a>을 사용하면 이전 예제를 다음과 같이 다시 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Before</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BeforeExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"com.xyz.CommonPointcuts.dataAccessOperation()"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAccessCheck</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="after-returning-advice">After Returning Advice</h1>
<p>반환 후 일치하는 메서드 실행이 정상적으로 반환되면 조언이 실행됩니다. <code>@AfterReturning</code> 어노테이션을 사용하여 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AfterReturning</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AfterReturningExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@AfterReturning</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAccessCheck</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
동일한 관점 내에서 여러 개의 advice 선언(및 다른 멤버도 포함)을 가질 수 있습니다. 이 예제에서는 각 조언의 효과에 초점을 맞추기 위해 단 하나의 조언 선언만 표시합니다.</p>
</blockquote>
<p>때로는 반환된 실제 값에 대한 조언 본문에 액세스해야 하는 경우가 있습니다. 다음 예제와 같이 반환 값을 바인딩하여 해당 액세스 권한을 얻는 <code>@AfterReturning</code> 형식을 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AfterReturning</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AfterReturningExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@AfterReturning</span><span class="token punctuation">(</span>
		pointcut<span class="token operator">=</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">,</span>
		returning<span class="token operator">=</span><span class="token string">"retVal"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAccessCheck</span><span class="token punctuation">(</span><span class="token class-name">Object</span> retVal<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>returning</code> 속성(attribute)에 사용된 이름은 조언 메소드의 매개변수 이름과 일치해야 합니다. 메서드 실행이 반환되면 반환 값은 해당 인수 값으로 조언 메서드에 전달됩니다. 또한 <code>returning</code> 절은 지정된 유형(이 경우 모든 반환 값과 일치하는 <code>Object</code>)의 값을 반환하는 메서드 실행으로만 일치를 제한합니다.</p>
<p>어드바이스 반환 후 사용시 완전히 다른 참조 반환은 불가능하므로 주의하시기 바랍니다.</p>
<h1 id="after-throwing-advice">After Throwing Advice</h1>
<p><code>After throwing advice</code> 는 예외를 던지면서 일치하는 메서드 실행이 종료될 때 실행됩니다. 다음 예제와 같이 <code>@AfterThrowing</code> 어노테이션을 사용하여 이를 선언할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AfterThrowing</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AfterThrowingExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@AfterThrowing</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doRecoveryActions</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>종종 주어진 유형의 예외가 던져질 때만 advice가 실행되기를 원하며 advice 본문에서 던져진 예외에 액세스해야 하는 경우도 많습니다. <code>throwing</code> 속성(attribute)을 사용하여 일치(matching)를 제한하고(원하는 경우 <code>Throwable</code>을 예외 유형으로 사용) 던진 예외를 조언 매개변수에 바인딩할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">AfterThrowing</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AfterThrowingExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@AfterThrowing</span><span class="token punctuation">(</span>
		pointcut<span class="token operator">=</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">,</span>
		throwing<span class="token operator">=</span><span class="token string">"ex"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doRecoveryActions</span><span class="token punctuation">(</span><span class="token class-name">DataAccessException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>throwing</code> 속성(attribute)에 사용된 이름은 advice 메소드의 매개변수 이름과 일치해야 합니다. 예외를 발생시켜 메소드 실행이 종료되면 해당 예외는 해당 인수 값으로 조언 메소드에 전달됩니다. <code>throwing</code> 절은 또한 지정된 유형의 예외(이 경우 <code>DataAccessException</code>)를 발생시키는 메소드 실행으로만 일치(matching)를 제한합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@AfterThrowing</code>은 일반적인 예외 처리 콜백을 나타내지 않습니다. 특히, <code>@AfterThrowing</code> 어드바이스 메소드는 조인 포인트(사용자가 선언한 대상 메소드) 자체에서만 예외를 수신해야 하며 동반된 <code>@After</code>/<code>@AfterReturning</code> 메소드에서는 예외를 수신하지 않아야 합니다.</p>
</blockquote>
<h1 id="after-finally-advice">After (Finally) Advice</h1>
<p>(최종적으로) 일치하는 메서드 실행이 종료되면 조언이 실행됩니다. <code>@After</code> 어노테이션을 사용하여 선언됩니다. 이후 advice은 정상 및 예외 반환 조건을 모두 처리하도록 준비되어야 합니다. 일반적으로 리소스 해제 및 유사한 목적에 사용됩니다. 다음 예에서는 after finally advice 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">After</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AfterFinallyExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@After</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doReleaseLock</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
AspectJ의 <code>@After</code> 어드바이스는 try-catch 문의 finally 블록과 유사한 "after finally 어드바이스"로 정의됩니다. 성공적인 일반 반환에만 적용되는 <code>@AfterReturning</code>과 달리 조인 포인트(사용자가 선언한 대상 메서드)에서 발생한 모든 결과, 일반 반환 또는 예외에 대해 호출됩니다.</p>
</blockquote>
<h1 id="around-advice">Around Advice</h1>
<p>  마지막 종류의 advice은 around advice입니다. Around 어드바이스는 일치하는 메서드 실행 "주변"으로 실행합니다. 메서드 실행 전후에 작업을 수행하고 메서드가 실제로 실행되는 시기, 방법, 심지어 실행 여부를 결정할 수 있는 기회가 있습니다. 스레드로부터 안전한 방식으로 메서드 실행 전후에 상태를 공유해야 하는 경우(예: 타이머 시작 및 중지) Around 어드바이스가 자주 사용됩니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
항상 귀하의 요구 사항을 충족하는 가장 강력한 형태의 advice을 사용하십시오.</p>
<p>예를 들어, before advice가 귀하의 요구 사항에 충분하다면 around advice을 사용하지 마십시오.</p>
</blockquote>
<p>Around 어드바이스는 <code>@Around</code> annotation을 메소드에 추가하여 선언됩니다. 메서드는 <code>Object</code>를 반환 type으로 선언해야 하며 메서드의 첫 번째 매개 변수는 <code>ProceedingJoinPoint</code> type이어야 합니다. advice 메소드의 본문 내에서 기본 메소드를 실행하려면 <code>ProceedingJoinPoint</code>에서 <code>proceed()</code>를 호출해야 합니다. 인수 없이 <code>proceed()</code>를 호출하면 호출 시 호출자의 원래 인수가 기본 메서드에 제공됩니다. 고급 사용 사례의 경우 인수 배열(<code>Object[]</code>)을 허용하는 <code>proceed()</code> 메서드의 오버로드된 변형이 있습니다. 배열의 값은 호출될 때 기본 메서드에 대한 인수로 사용됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>Object[]</code>와 함께 호출될 때의 <code>proceed</code> 동작은 AspectJ 컴파일러에 의해 컴파일된 어라운드 어드바이스에 대한 <code>proceed</code> 동작과 약간 다릅니다. 전통적인 AspectJ 언어를 사용하여 작성된 around 어드바이스의 경우, <code>proceed</code>하기 위해 전달된 인수 수는 around 어드바이스에 전달된 인수 수(기본 조인 포인트에서 사용하는 인수 수가 아님)와 일치해야 하며, proceed하기 위해 주어진 인수 position에 전달된 값은 값이 바인딩된 엔터티에 대한 조인 포인트의 원래 값을 대체합니다(지금 당장 이해가 되지 않더라도 걱정하지 마세요).</p>
<p>Spring이 취한 접근 방식은 더 간단하고 프록시 기반의 실행 전용 의미 체계와 더 잘 일치합니다. Spring용으로 작성된 <code>@AspectJ</code> aspects을 컴파일하고 AspectJ 컴파일러 및 위버를 사용하여 인수를 <code>proceed</code>하는 경우에만 이 차이점을 인식하면 됩니다. Spring AOP와 AspectJ 모두에서 100% 호환되는 측면을 작성하는 방법이 있으며 이는 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html#aop-ataspectj-advice-proceeding-with-the-call">advice 매개변수에 대한 다음 섹션</a>에서 논의됩니다.</p>
</blockquote>
<p>around 어드바이스에 의해 반환된 값은 메서드 호출자가 본 반환 값입니다. 예를 들어, 간단한 캐싱 aspect은 캐시에 값이 있는 경우 캐시에서 값을 반환하거나, 그렇지 않은 경우 <code>proceed()</code>를 호출하고 해당 값을 반환할 수 있습니다. proceed 상황은 around 어드바이스 본문 내에서 한 번, 여러 번 호출되거나 전혀 호출되지 않을 수 있습니다. 이 모든 것은 합법적입니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
around 어드바이스 메소드의 반환 유형을 <code>void</code>로 선언하면 항상 <code>null</code>이 호출자에게 반환되어 ㅔ<code>proceed()</code> 호출 결과가 사실상 무시됩니다. 따라서 around 어드바이스 메소드는 반환 유형을 <code>Object</code>로 선언하는 것이 좋습니다. advice 메소드는 기본 메소드가 <code>void</code> 반환 유형을 가지고 있더라도 일반적으로 <code>proceed()</code> 호출에서 반환된 값을 반환해야 합니다. 그러나 advice은 선택적으로 캐시된 값, 래핑된 값 또는 사용 사례에 따라 다른 값을 반환할 수 있습니다.</p>
</blockquote>
<p>다음 예에서는 around 어드바이스를 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Around</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang</span><span class="token punctuation">.</span><span class="token class-name">ProceedingJoinPoint</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AroundExample</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Around</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz..service.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">doBasicProfiling</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> pjp<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
		<span class="token comment">// start stopwatch</span>
		<span class="token class-name">Object</span> retVal <span class="token operator">=</span> pjp<span class="token punctuation">.</span><span class="token function">proceed</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// stop stopwatch</span>
		<span class="token keyword">return</span> retVal<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="advice-parameters">Advice Parameters</h1>
<p>Spring은 완전히 type화된 advice을 제공합니다. 즉, 항상 <code>Object[]</code> 배열로 작업하는 대신 advice 시그니처에 필요한 매개변수를 선언한다는 의미입니다(앞서 returning 및 throwing 예제에서 본 것처럼). 이 섹션의 뒷부분에서 advice 본문에 인수 및 기타 상황별 값을 제공하는 방법을 살펴보겠습니다. 먼저, 현재 어드바이스가 조언하고 있는 메소드에 대해 알아볼 수 있는 제네릭 어드바이스를 작성하는 방법을 살펴본다.</p>
<h2 id="access-to-the-current-joinpoint">Access to the Current <code>JoinPoint</code></h2>
<p>모든 어드바이스 메소드는 첫 번째 매개변수로 <code>org.aspectj.lang.JoinPoint</code> type의 매개변수를 선언할 수 있습니다. <code>JoinPoint</code>의 하위 클래스인 <code>ProceedingJoinPoint</code> 유형의 첫 번째 매개변수를 선언하려면 around 어드바이스가 필요합니다.</p>
<p><code>JoinPoint</code> 인터페이스는 다음과 같은 유용한 메소드를 제공합니다.</p>
<ul>
<li>
<p><code>getArgs()</code>: 메서드 인수를 반환합니다.</p>
</li>
<li>
<p><code>getThis()</code>: 프록시 객체를 반환합니다.</p>
</li>
<li>
<p><code>getTarget()</code>: 대상 객체를 반환합니다.</p>
</li>
<li>
<p><code>getSignature()</code>: advice되는 메소드에 대한 설명을 반환합니다.</p>
</li>
<li>
<p><code>toString()</code>: advice되는 메소드에 대한 유용한 설명을 인쇄합니다.</p>
</li>
</ul>
<p>자세한 내용은 <a href="https://www.eclipse.org/aspectj/doc/released/runtime-api/org/aspectj/lang/JoinPoint.html">javadoc</a>을 참조하세요.</p>
<h2 id="passing-parameters-to-advice">Passing Parameters to Advice</h2>
<p>우리는 반환된 값이나 예외 값을 바인딩하는 방법을 이미 살펴보았습니다(어드바이스를 반환한 후 및 던진 후 사용). 어드바이스 본문에서 인수 값을 사용할 수 있도록 하려면 <code>args</code>의 바인딩 형식을 사용할 수 있습니다. <code>args</code> 표현식에서 type 이름 대신 매개변수 이름을 사용하는 경우 해당 인수의 값은 advice가 호출될 때 매개변수 값으로 전달됩니다. 예를 들어 이를 더 명확하게 해야 합니다. <code>Account</code> 객체를 첫 번째 매개변수로 사용하는 DAO 작업 실행을 advice하고 싶고 advice 본문의 계정에 액세스해야 한다고 가정해 보겠습니다. 다음과 같이 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..)) &amp;&amp; args(account,..)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">validateAccount</span><span class="token punctuation">(</span><span class="token class-name">Account</span> account<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>포인트컷 표현식의 <code>args(account,..)</code> 부분은 두 가지 용도로 사용됩니다. 첫째, 메서드가 하나 이상의 매개 변수를 사용하고 해당 매개 변수에 전달된 인수가 <code>Account</code> 인스턴스인 메서드 실행으로만 일치를 제한합니다. 둘째, <code>account</code> 매개변수를 통해 조언에 실제 <code>Account</code> 개체를 사용할 수 있게 만듭니다.</p>
<p>이를 작성하는 또 다른 방법은 조인 포인트와 일치할 때 <code>Account</code> 개체 값을 "제공"하는 포인트컷을 선언한 다음 어드바이스에서 명명된 포인트컷을 참조하는 것입니다. 이는 다음과 같이 보일 것입니다:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Pointcut</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz.dao.*.*(..)) &amp;&amp; args(account,..)"</span><span class="token punctuation">)</span>
<span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">accountDataAccessOperation</span><span class="token punctuation">(</span><span class="token class-name">Account</span> account<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>

<span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"accountDataAccessOperation(account)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">validateAccount</span><span class="token punctuation">(</span><span class="token class-name">Account</span> account<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>자세한 내용은 AspectJ 프로그래밍 가이드를 참조하세요.</p>
<p>프록시 객체(<code>this</code>), 대상 객체(<code>target</code>) 및 annotation(<code>@within</code>, <code>@target</code>, <code>@annotation</code> 및 <code>@args</code>)은 모두 비슷한 방식으로 바인딩될 수 있습니다. 다음 예제 세트에서는 <code>@Auditable</code> annotation이 달린 메서드 실행을 일치시키고 감사 코드를 추출하는 방법을 보여줍니다.</p>
<p>다음은 <code>@Auditable</code> annotation의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>METHOD<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Auditable</span> <span class="token punctuation">{</span>
	<span class="token class-name">AuditCode</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다음은 <code>@Auditable</code> 메서드 실행과 일치하는 advice을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"com.xyz.Pointcuts.publicMethod() &amp;&amp; @annotation(auditable)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">audit</span><span class="token punctuation">(</span><span class="token class-name">Auditable</span> auditable<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AuditCode</span> code <span class="token operator">=</span> auditable<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p><a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/pointcuts.html#aop-pointcuts-combining">포인트컷 표현식 결합</a>에 정의된 pointcut이라는 <code>publicMethod</code>를 참조합니다.</p>
<h2 id="advice-parameters-and-generics">Advice Parameters and Generics</h2>
<p>Spring AOP는 클래스 선언과 메소드 매개변수에 사용되는 제네릭을 처리할 수 있습니다. 다음과 같은 일반 유형이 있다고 가정해 보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Sample</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
	<span class="token keyword">void</span> <span class="token function">sampleGenericMethod</span><span class="token punctuation">(</span><span class="token class-name">T</span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">void</span> <span class="token function">sampleGenericCollectionMethod</span><span class="token punctuation">(</span><span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> param<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>메소드를 가로채려는 매개변수 type에 advice 매개변수를 연결하여 메소드 type의 가로채기를 특정 매개변수 type으로 제한할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"execution(* ..Sample+.sampleGenericMethod(*)) &amp;&amp; args(param)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">beforeSampleMethod</span><span class="token punctuation">(</span><span class="token class-name">MyType</span> param<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Advice implementation</span>
<span class="token punctuation">}</span></code></pre>
<p>이 접근 방식은 일반 컬렉션에는 작동하지 않습니다. 따라서 다음과 같이 포인트컷을 정의할 수 없습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"execution(* ..Sample+.sampleGenericCollectionMethod(*)) &amp;&amp; args(param)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">beforeSampleMethod</span><span class="token punctuation">(</span><span class="token class-name">Collection</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MyType</span><span class="token punctuation">&gt;</span></span> param<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Advice implementation</span>
<span class="token punctuation">}</span></code></pre>
<p>이 작업을 수행하려면 컬렉션의 모든 요소를 검사해야 하는데 이는 일반적으로 <code>null</code> 값을 처리하는 방법을 결정할 수 없기 때문에 합리적이지 않습니다. 이와 유사한 결과를 얻으려면 <code>Collection&lt;?&gt;</code>에 매개변수를 입력하고 요소 유형을 수동으로 확인해야 합니다.</p>
<h2 id="determining-argument-names">Determining Argument Names</h2>
<p>advice 호출의 매개변수 바인딩은 포인트컷 표현식에 사용된 이름을 조언 및 포인트컷 메소드 시그니처에 선언된 매개변수 이름과 일치시키는 데 의존합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
AspectJ API는 매개변수 이름을 인수 이름으로 참조하므로 이 섹션에서는 인수와 매개변수라는 용어를 같은 의미로 사용합니다.</p>
</blockquote>
<p>Spring AOP는 매개변수 이름을 결정하기 위해 다음 <code>ParameterNameDiscoverer</code> 구현을 사용합니다. 각 발견자에게는 매개변수 이름을 발견할 수 있는 기회가 주어지며, 첫 번째로 성공한 발견자가 승리합니다. 등록된 발견자 중 어느 것도 매개변수 이름을 결정할 수 없으면 예외가 발생합니다.</p>
<p><em>AspectJAnnotationParameterNameDiscoverer</em></p>
<p>해당 조언이나 포인트컷 annotation의 <code>argNames</code> 속성(attribute)을 통해 사용자가 명시적으로 지정한 매개변수 이름을 사용합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html#aop-ataspectj-advice-params-names-explicit">명시적 인수 이름</a>을 참조하세요.</p>
<p><em>KotlinReflectionParameterNameDiscoverer</em><br>
Kotlin 리플렉션 API를 사용하여 매개변수 이름을 결정합니다. 이 Discoverer는 해당 API가 classpath에 있는 경우에만 사용됩니다.</p>
<p><em>StandardReflectionParameterNameDiscoverer</em><br>
표준 <code>java.lang.reflect.Parameter</code> API를 사용하여 매개변수 이름을 결정합니다. <code>javac</code>에 대해 <code>-parameters</code> 플래그를 사용하여 코드를 컴파일해야 합니다. Java 8 이상에서 권장되는 접근 방식입니다.</p>
<p><em>AspectJAdviceParameterNameDiscoverer</em><br>
포인트컷 표현식, <code>returning</code> 및 <code>throwing</code> 절에서 매개변수 이름을 추론합니다. 사용된 알고리즘에 대한 자세한 내용은 javadoc를 참조하세요.</p>
<h2 id="explicit-argument-names">Explicit Argument Names</h2>
<p>@AspectJ 어드바이스와 포인트컷 annotation에는 annotation이 달린 메서드의 인수 이름을 지정하는 데 사용할 수 있는 선택적 <code>argNames</code> 속성이 있습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
@AspectJ aspect가 디버그 정보 없이도 AspectJ 컴파일러(<code>ajc</code>)에 의해 컴파일된 경우 컴파일러가 필요한 정보를 유지하므로 <code>argNames</code> 속성을 추가할 필요가 없습니다.</p>
<p>마찬가지로 @AspectJ aspect가 <code>-parameters</code> 플래그를 사용하여 <code>javac</code>로 컴파일된 경우 컴파일러가 필요한 정보를 유지하므로 <code>argNames</code> 속성(attribute)을 추가할 필요가 없습니다.</p>
</blockquote>
<p>다음 예에서는 <code>argNames</code> 특성을 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span>
	value <span class="token operator">=</span> <span class="token string">"com.xyz.Pointcuts.publicMethod() &amp;&amp; target(bean) &amp;&amp; @annotation(auditable)"</span><span class="token punctuation">,</span> <span class="token comment">// (1)</span>
	argNames <span class="token operator">=</span> <span class="token string">"bean,auditable"</span><span class="token punctuation">)</span> <span class="token comment">// (2)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">audit</span><span class="token punctuation">(</span><span class="token class-name">Object</span> bean<span class="token punctuation">,</span> <span class="token class-name">Auditable</span> auditable<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AuditCode</span> code <span class="token operator">=</span> auditable<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// ... use code and bean</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 포인트컷 표현식 결합에 정의된 pointcut이라는 <code>publicMethod</code>를 참조합니다.<br>
(2) 인수 이름으로 Bean과 auditable을 선언합니다.</p>
<p>첫 번째 매개 변수가 <code>JoinPoint</code>, <code>ProceedingJoinPoint</code> 또는 <code>JoinPoint.StaticPart</code> type인 경우 <code>argNames</code> 특성(attribute) 값에서 매개 변수 이름을 생략할 수 있습니다. 예를 들어, 조인 포인트 객체를 수신하기 위해 이전 advice을 수정하는 경우 <code>argNames</code> 속성(attribute)은 이를 포함할 필요가 없습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span>
	value <span class="token operator">=</span> <span class="token string">"com.xyz.Pointcuts.publicMethod() &amp;&amp; target(bean) &amp;&amp; @annotation(auditable)"</span><span class="token punctuation">,</span> <span class="token comment">// (1)</span>
	argNames <span class="token operator">=</span> <span class="token string">"bean,auditable"</span><span class="token punctuation">)</span> <span class="token comment">// (2)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">audit</span><span class="token punctuation">(</span><span class="token class-name">JoinPoint</span> jp<span class="token punctuation">,</span> <span class="token class-name">Object</span> bean<span class="token punctuation">,</span> <span class="token class-name">Auditable</span> auditable<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">AuditCode</span> code <span class="token operator">=</span> auditable<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token comment">// ... use code, bean, and jp</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 포인트컷 표현식 결합에 정의된 pointcut이라는 <code>publicMethod</code>를 참조합니다.<br>
(2) 인수 이름으로 Bean과 auditable을 선언합니다.</p>
<p><code>JoinPoint</code>, <code>ProceedingJoinPoint</code> 또는 <code>JoinPoint.StaticPart</code> 유형의 첫 번째 매개변수에 제공되는 특별 처리는 다른 조인 포인트 컨텍스트를 수집하지 않는 어드바이스 메소드에 특히 편리합니다. 이러한 상황에서는 <code>argNames</code> 속성을 생략할 수 있습니다. 예를 들어 다음 조언에서는 <code>argNames</code> 속성(attribute)을 선언할 필요가 없습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"com.xyz.Pointcuts.publicMethod()"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">audit</span><span class="token punctuation">(</span><span class="token class-name">JoinPoint</span> jp<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ... use jp</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 포인트컷 표현식 결합에 정의된 pointcut이라는 <code>publicMethod</code>를 참조합니다.</p>
<h2 id="proceeding-with-arguments">Proceeding with Arguments</h2>
<p>우리는 Spring AOP와 AspectJ에서 일관되게 작동하는 인수를 사용하여 <code>proceed</code> 호출을 작성하는 방법을 설명할 것이라고 앞서 언급했습니다. 해결책은 advice signature이 각 메소드 매개변수를 순서대로 바인딩하는지 확인하는 것입니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Around</span><span class="token punctuation">(</span><span class="token string">"execution(List&lt;Account&gt; find*(..)) &amp;&amp; "</span> <span class="token operator">+</span>
		<span class="token string">"com.xyz.CommonPointcuts.inDataAccessLayer() &amp;&amp; "</span> <span class="token operator">+</span>
		<span class="token string">"args(accountHolderNamePattern)"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">preProcessQueryPattern</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> pjp<span class="token punctuation">,</span>
		<span class="token class-name">String</span> accountHolderNamePattern<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
	<span class="token class-name">String</span> newPattern <span class="token operator">=</span> <span class="token function">preProcess</span><span class="token punctuation">(</span>accountHolderNamePattern<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> pjp<span class="token punctuation">.</span><span class="token function">proceed</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Object</span><span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span>newPattern<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 명명된 Pointcut 정의 공유에 정의된 <code>inDataAccessLayer</code> 명명된 pointcut을 참조합니다.</p>
<p>대부분의 경우 앞의 예와 같이 이 바인딩을 수행합니다.</p>
<h1 id="advice-ordering">Advice Ordering</h1>
<p>여러 개의 어드바이스가 모두 동일한 조인 포인트에서 실행되길 원하면 어떻게 될까요? Spring AOP는 advice 실행 순서를 결정하기 위해 AspectJ와 동일한 우선순위 규칙을 따릅니다. 우선순위가 가장 높은 advice가 "들어가는 중에" 먼저 실행됩니다(따라서 before advice 두 개가 주어지면 우선순위가 가장 높은 advice이 먼저 실행됩니다). 조인 포인트에서 "나가는 동안" 가장 높은 우선순위의 advice이 마지막에 실행됩니다(따라서 두 개의 after advice이 주어지면 가장 높은 우선순위를 가진 advice이 두 번째로 실행됩니다).</p>
<p>서로 다른 aspect에 정의된 두 가지 advice이 모두 동일한 조인 포인트에서 실행되어야 하는 경우 달리 지정하지 않는 한 실행 순서는 정의되지 않습니다. 우선순위를 지정하여 실행 순서를 제어할 수 있습니다. 이는 aspect 클래스에서 <code>org.springframework.core.Ordered</code> 인터페이스를 구현하거나 <code>@Order</code> annotation으로 주석을 달아 일반적인 Spring 방식으로 수행됩니다. 두 가지 aspect이 주어지면 <code>Ordered.getOrder()</code>(또는 annotation 값)에서 더 낮은 값을 반환하는 aspect이 더 높은 우선 순위를 갖습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
특정 aspect의 각각의 고유한 advice type은 개념적으로 조인 포인트에 직접 적용되도록 의도됩니다. 결과적으로 <code>@AfterThrowing</code> 어드바이스 메소드는 동반된 <code>@After</code>/<code>@AfterReturning</code> 메소드로부터 예외를 수신해서는 안 됩니다.</p>
<p>Spring Framework 5.2.7부터 동일한 조인 포인트에서 실행되어야 하는 동일한 <code>@Aspect</code> 클래스에 정의된 어드바이스 메서드는 어드바이스 type에 따라 다음 순서로 가장 높은 우선 순위에서 가장 낮은 우선 순위로 우선 순위가 할당됩니다. <code>@Around</code>, <code>@Before</code> , <code>@After</code>, <code>@AfterReturning</code>, <code>@AfterThrowing</code>. 그러나 <code>@After</code> 어드바이스 메서드는 <code>@After</code>에 대한 AspectJ의 "after finally 어드바이스" 의미 체계에 따라 동일한 aspect에서 <code>@AfterReturning</code> 또는 <code>@AfterThrowing</code> 어드바이스 메서드 이후에 효과적으로 호출된다는 점에 유의하세요.</p>
<p>동일한 <code>@Aspect</code> 클래스에 정의된 두 개의 동일한 유형의 advice(예: 두 개의 <code>@After</code> advice 메소드)이 모두 동일한 조인 포인트에서 실행되어야 하는 경우 순서는 정의되지 않습니다(소스를 검색할 방법이 없기 때문에). javac 컴파일된 클래스에 대한 리플렉션을 통한 코드 선언 순서). 이러한 조언 메서드를 각 <code>@Aspect</code> 클래스의 조인 포인트당 하나의 조언 메서드로 축소하거나 <code>Ordered</code> 또는 <code>@Order</code>를 통해 측면 수준에서 주문할 수 있는 별도의 <code>@Aspect</code> 클래스로 advice 조각을 리팩터링하는 것을 고려하세요.</p>
</blockquote>