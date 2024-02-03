<p><code>@Qualifier</code> 어노테이션 외에도 Java 일반 유형을 암시적 한정(qualification) 형식으로 사용할 수 있습니다. 예를 들어 다음과 같은 구성이 있다고 가정합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyConfiguration</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">StringStore</span> <span class="token function">stringStore</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">StringStore</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">IntegerStore</span> <span class="token function">integerStore</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">IntegerStore</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 Bean이 제네릭 인터페이스(즉, <code>Store&lt;String&gt;</code> 및 <code>Store&lt;Integer&gt;)</code>를 구현한다고 가정하면 <code>Store</code> 인터페이스를 <code>@Autowire</code>할 수 있으며 다음 예제와 같이 제네릭이 한정자로 사용됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Autowired</span>
<span class="token keyword">private</span> <span class="token class-name">Store</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> s1<span class="token punctuation">;</span> <span class="token comment">// &lt;String&gt; qualifier, injects the stringStore bean</span>

<span class="token annotation punctuation">@Autowired</span>
<span class="token keyword">private</span> <span class="token class-name">Store</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Integer</span><span class="token punctuation">&gt;</span></span> s2<span class="token punctuation">;</span> <span class="token comment">// &lt;Integer&gt; qualifier, injects the integerStore bean</span></code></pre>
<p>제네릭 한정자는 list, <code>Map</code> 인스턴스 및 배열을 자동 연결하는 경우에도 적용됩니다. 다음 예제에서는 제네릭 <code>List</code>을 자동 연결(autowires)합니다.</p>
<pre><code class="language-java"><span class="token comment">// Inject all Store beans as long as they have an &lt;Integer&gt; generic</span>
<span class="token comment">// Store&lt;String&gt; beans will not appear in this list</span>
<span class="token annotation punctuation">@Autowired</span>
<span class="token keyword">private</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Store</span><span class="token punctuation">&lt;</span><span class="token class-name">Integer</span><span class="token punctuation">&gt;</span><span class="token punctuation">&gt;</span></span> s<span class="token punctuation">;</span></code></pre>