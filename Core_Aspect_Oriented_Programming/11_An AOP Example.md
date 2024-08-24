<p>이제 모든 구성 요소가 어떻게 작동하는지 살펴보았으므로 이를 함께 모아 유용한 작업을 수행할 수 있습니다.</p>
<p>동시성 문제(예: 교착 상태 패자)로 인해 비즈니스 서비스 실행이 실패하는 경우가 있습니다. 작업을 다시 시도하면 다음 시도에서 성공할 가능성이 높습니다. 이러한 조건(충돌 해결을 위해 사용자에게 돌아갈 필요가 없는 멱등성 작업)에서 재시도하는 것이 적절한 비즈니스 서비스의 경우 클라이언트가 <code>PessimisticLockingFailureException</code>을 보지 않도록 작업을 투명하게 재시도하려고 합니다. 이는 서비스 계층의 여러 서비스를 명확하게 구분하는 요구 사항이므로 aspect을 통해 구현하는 데 이상적입니다.</p>
<p>작업을 다시 시도하고 싶기 때문에 진행을 여러 번 호출할 수 있도록 around 어드바이스를 사용해야 합니다. 다음 목록은 기본 aspect 구현을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConcurrentOperationExecutor</span> <span class="token keyword">implements</span> <span class="token class-name">Ordered</span> <span class="token punctuation">{</span>

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

	<span class="token annotation punctuation">@Around</span><span class="token punctuation">(</span><span class="token string">"com.xyz.CommonPointcuts.businessService()"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
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
<p>(1) 명명된 Pointcut 정의 공유에 정의된 pointcut이라는 이름의 <code>businessService</code>를 참조합니다.</p>
<p>애스펙트는 <code>Ordered</code> 인터페이스를 구현하므로 트랜잭션 aspect보다 애스펙트의 우선순위를 더 높게 설정할 수 있습니다(재시도할 때마다 새로운 트랜잭션을 원함). <code>maxRetries</code> 및 <code>order</code> 속성(properties)은 모두 Spring에 의해 구성됩니다. 주요 작업은 aspect과 관련된 <code>doConcurrentOperation</code>에서 발생합니다. 지금은 각 <code>businessService</code>에 재시도 논리를 적용합니다. 계속 진행하려고 시도하고 <code>PessimisticLockingFailureException</code>으로 실패하면 재시도 횟수를 모두 소진하지 않는 한 다시 시도합니다.</p>
<p>해당 Spring 구성은 다음과 같습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspectj-autoproxy</span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>concurrentOperationExecutor<span class="token punctuation">"</span></span>
		<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.service.impl.ConcurrentOperationExecutor<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>maxRetries<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>3<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>order<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>100<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>멱등성 작업만 재시도하도록 측면을 개선하기 위해 다음과 같은 <code>Idempotent</code> annotation을 정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token comment">// marker annotation</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Idempotent</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 annotation을 사용하여 서비스 작업 구현에 annotation을 달 수 있습니다. 멱등성 연산만 재시도하는 aspect 변경에는 다음과 같이 <code>@Idempotent</code> 연산만 일치하도록 포인트컷 표현식을 정제하는 작업이 포함됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Around</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz..service.*.*(..)) &amp;&amp; "</span> <span class="token operator">+</span>
		<span class="token string">"@annotation(com.xyz.service.Idempotent)"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">Object</span> <span class="token function">doConcurrentOperation</span><span class="token punctuation">(</span><span class="token class-name">ProceedingJoinPoint</span> pjp<span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Throwable</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>