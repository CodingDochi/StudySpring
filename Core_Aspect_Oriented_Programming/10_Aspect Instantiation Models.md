<blockquote>
<p><strong>[Note]</strong><br>
이것은 고급 주제입니다. AOP를 막 시작했다면 나중에 안전하게 건너뛸 수 있습니다.</p>
</blockquote>
<p>기본적으로 애플리케이션 컨텍스트 내에는 각 aspect의 단일 인스턴스가 있습니다. AspectJ는 이것을 싱글톤 인스턴스화 모델이라고 부릅니다. 대체 수명주기로 aspect을 정의하는 것이 가능합니다. Spring은 인스턴스화 모델 내에서 AspectJ의 <code>perthis</code>, <code>pertarget</code> 및 <code>pertype</code>을 지원합니다. <code>percflow</code> 및 <code>percflowlow</code>는 현재 지원되지 않습니다.</p>
<p><code>@Aspect</code> annotation에 <code>perthis</code> 절을 지정하여 <code>perthis</code> aspect을 선언할 수 있습니다. 다음 예를 고려하십시오.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Aspect</span><span class="token punctuation">(</span><span class="token string">"perthis(execution(* com.xyz..service.*.*(..)))"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyAspect</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">int</span> someState<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Before</span><span class="token punctuation">(</span><span class="token string">"execution(* com.xyz..service.*.*(..))"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">recordServiceUsage</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// ...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 예에서 <code>perthis</code> 절의 효과는 비즈니스 서비스를 수행하는 각 고유 서비스 객체(포인트컷 표현식과 일치하는 조인 포인트에 바인딩된 각 고유 객체)에 대해 하나의 aspect 인스턴스가 생성된다는 것입니다. aspect 인스턴스는 서비스 개체에서 메서드가 처음 호출될 때 생성됩니다. 서비스 객체가 범위를 벗어나면 애스펙트도 범위를 벗어납니다. aspect 인스턴스가 생성되기 전에는 그 안에 있는 어드바이스가 실행되지 않습니다. aspect 인스턴스가 생성되자마자 그 안에 선언된 어드바이스는 일치하는 조인 포인트에서 실행되지만, 서비스 객체가 이 aspect과 연관된 것일 때만 실행됩니다. <code>per</code>절에 대한 자세한 내용은 AspectJ 프로그래밍 가이드를 참조하세요.</p>
<p><code>pertarget</code> 인스턴스화 모델은 <code>perthis</code>와 정확히 동일한 방식으로 작동하지만 일치하는 조인 포인트에서 각 고유 대상 개체에 대해 하나의 aspect 인스턴스를 생성합니다.</p>