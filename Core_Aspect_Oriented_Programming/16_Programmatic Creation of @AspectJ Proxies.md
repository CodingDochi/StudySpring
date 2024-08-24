<p><code>&lt;aop:config&gt;</code> 또는 <code>&lt;aop:aspectj-autoproxy&gt;</code>를 사용하여 구성에서 aspect을 선언하는 것 외에도 대상 객체에 advice하는 프록시를 프로그래밍 방식으로 생성하는 것도 가능합니다. Spring의 AOP API에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/aop-api.html">다음 장</a>을 참조하세요. 여기서는 @AspectJ aspect을 사용하여 자동으로 프록시를 생성하는 기능에 중점을 두고 싶습니다.</p>
<p>하나 이상의 @AspectJ aspect에서 advice하는 대상 객체에 대한 프록시를 생성하기 위해 <code>org.springframework.aop.aspectj.annotation.AspectJProxyFactory</code> 클래스를 사용할 수 있습니다. 이 클래스의 기본 사용법은 다음 예제와 같이 매우 간단합니다.</p>
<pre><code class="language-java"><span class="token comment">// create a factory that can generate a proxy for the given target object</span>
<span class="token class-name">AspectJProxyFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AspectJProxyFactory</span><span class="token punctuation">(</span>targetObject<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// add an aspect, the class must be an @AspectJ aspect</span>
<span class="token comment">// you can call this as many times as you need with different aspects</span>
factory<span class="token punctuation">.</span><span class="token function">addAspect</span><span class="token punctuation">(</span><span class="token class-name">SecurityManager</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// you can also add existing aspect instances, the type of the object supplied</span>
<span class="token comment">// must be an @AspectJ aspect</span>
factory<span class="token punctuation">.</span><span class="token function">addAspect</span><span class="token punctuation">(</span>usageTracker<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// now get the proxy object...</span>
<span class="token class-name">MyInterfaceType</span> proxy <span class="token operator">=</span> factory<span class="token punctuation">.</span><span class="token function">getProxy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.6/javadoc-api/org/springframework/aop/aspectj/annotation/AspectJProxyFactory.html">javadoc</a>을 참조하세요.</p>