<p>Spring AOP는 JDK 동적 프록시 또는 CGLIB를 사용하여 주어진 대상 객체에 대한 프록시를 생성합니다. JDK 동적 프록시는 JDK에 내장된 반면 CGLIB는 일반적인 오픈 소스 클래스 정의 라이브러리(<code>spring-core</code>로 다시 패키징됨)입니다.</p>
<p>프록시할 대상 개체가 하나 이상의 인터페이스를 구현하는 경우 JDK 동적 프록시가 사용됩니다. 대상 유형으로 구현된 모든 인터페이스가 프록시됩니다. 대상 객체가 인터페이스를 구현하지 않으면 CGLIB 프록시가 생성됩니다.</p>
<p>CGLIB 프록싱을 강제로 사용하려는 경우(예를 들어 인터페이스에 의해 구현된 메소드뿐만 아니라 대상 객체에 대해 정의된 모든 메소드를 프록시하려는 경우) 그렇게 할 수 있습니다. 그러나 다음 문제를 고려해야 합니다.</p>
<ul>
<li>
<p>CGLIB를 사용하면 런타임에서 생성된 하위 클래스에서 재정의할 수 없기 때문에 <code>final</code> 메서드를 advice할 수 없습니다.</p>
</li>
<li>
<p>Spring 4.0부터 CGLIB 프록시 인스턴스가 Objenesis를 통해 생성되므로 프록시 객체의 생성자는 더 이상 두 번 호출되지 않습니다. JVM이 생성자 우회를 허용하지 않는 경우에만 Spring의 AOP 지원에서 이중 호출 및 해당 디버그 로그 항목을 볼 수 있습니다.</p>
</li>
</ul>
<p>CGLIB 프록시를 강제로 사용하려면 다음과 같이 <code>&lt;aop:config&gt;</code> 요소의 <code>proy-target-class</code> 속성 값을 <code>true</code>로 설정하세요.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>config</span> <span class="token attr-name">proxy-target-class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- other beans defined here... --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">aop:</span>config</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>AspectJ 자동 프록시 지원을 사용할 때 CGLIB 프록시를 강제하려면 다음과 같이 <code>&lt;aop:aspectj-autoproxy&gt;</code> 요소의 <code>proxy-target-class</code> 속성을 <code>true</code>로 설정하세요.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">aop:</span>aspectj-autoproxy</span> <span class="token attr-name">proxy-target-class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>true<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
여러 <code>&lt;aop:config/&gt;</code> 섹션은 런타임 시 단일 통합 자동 프록시 생성자로 축소되며, 이는 <code>&lt;aop:config/&gt;</code> 섹션(일반적으로 다른 XML Bean 정의 파일에서)이 지정한 가장 강력한 프록시 설정을 적용합니다. 이는 <code>&lt;tx:annotation-driven/&gt;</code> 및 <code>&lt;aop:aspectj-autoproxy/&gt;</code> 요소에도 적용됩니다.</p>
<p>명확하게 말하면, <code>&lt;tx:annotation-driven/&gt;</code>, <code>&lt;aop:aspectj-autoproxy/&gt;</code> 또는 <code>&lt;aop:config/&gt;</code> 요소에서 <code>proxy-target-class="true"</code>를 사용하면 세 가지 모두에 대해 CGLIB 프록시를 강제로 사용하게 됩니다.</p>
</blockquote>
<h1 id="understanding-aop-proxies">Understanding AOP Proxies</h1>
<p>Spring AOP는 프록시 기반입니다. 자신만의 aspect을 작성하거나 Spring Framework와 함께 제공되는 Spring AOP 기반 aspect을 사용하기 전에 마지막 문장이 실제로 의미하는 바의 의미를 파악하는 것이 매우 중요합니다.</p>
<p>먼저 다음 코드 조각에서 볼 수 있듯이 일반 바닐라, 프록시되지 않은, 특별한 내용이 없는 객체 참조가 있는 시나리오를 생각해 보세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimplePojo</span> <span class="token keyword">implements</span> <span class="token class-name">Pojo</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// this next method invocation is a direct call on the 'this' reference</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">bar</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">bar</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// some logic...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>객체 참조에 대해 메서드를 호출하면 다음 이미지와 목록에 표시된 것처럼 해당 객체 참조에서 메서드가 직접 호출됩니다.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/aop-proxy-plain-pojo-call.png"></p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Main</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">Pojo</span> pojo <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimplePojo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// this is a direct method call on the 'pojo' reference</span>
		pojo<span class="token punctuation">.</span><span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>클라이언트 코드가 갖는 참조가 프록시인 경우 상황이 약간 변경됩니다. 다음 다이어그램과 코드 조각을 고려하세요.</p>
<p><img src="https://docs.spring.io/spring-framework/reference/_images/aop-proxy-call.png"></p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Main</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ProxyFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ProxyFactory</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">SimplePojo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		factory<span class="token punctuation">.</span><span class="token function">addInterface</span><span class="token punctuation">(</span><span class="token class-name">Pojo</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		factory<span class="token punctuation">.</span><span class="token function">addAdvice</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">RetryAdvice</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token class-name">Pojo</span> pojo <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Pojo</span><span class="token punctuation">)</span> factory<span class="token punctuation">.</span><span class="token function">getProxy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// this is a method call on the proxy!</span>
		pojo<span class="token punctuation">.</span><span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 이해해야 할 중요한 점은 <code>Main</code> 클래스의 <code>main(..)</code> 메서드 내부에 있는 클라이언트 코드에 프록시에 대한 참조가 있다는 것입니다. 이는 해당 객체 참조에 대한 메서드 호출이 프록시에 대한 호출임을 의미합니다. 결과적으로 프록시는 특정 메서드 호출과 관련된 모든 인터셉터(어드바이스)에 위임할 수 있습니다. 그러나 호출이 최종적으로 대상 객체(이 경우 <code>SimplePojo</code> 참조)에 도달하면 <code>this.bar()</code> 또는 <code>this.foo()</code>와 같이 자체적으로 수행할 수 있는 모든 메서드 호출이 호출됩니다. 프록시가 아닌 <code>this</code> 참조입니다. 이는 중요한 의미를 갖습니다. 이는 자체 호출로 인해 메서드 호출과 관련된 advice이 실행될 기회를 얻지 못한다는 것을 의미합니다.</p>
<p>좋습니다. 그러면 이에 대해 어떻게 해야 합니까? 가장 좋은 접근 방식(여기서는 "최고"라는 용어가 느슨하게 사용됨)은 자체 호출이 발생하지 않도록 코드를 리팩터링하는 것입니다. 이를 위해서는 귀하의 노력이 수반되지만 가장 좋고 최소 침습적 접근 방식입니다. 다음 접근 방식은 정말 끔찍합니다. 우리는 그것을 지적하기를 주저합니다. 왜냐하면 그것이 너무 끔찍하기 때문입니다. 다음 예제에서 볼 수 있듯이 (우리에게는 고통스럽기는 하지만) 클래스 내의 로직을 Spring AOP에 완전히 연결할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimplePojo</span> <span class="token keyword">implements</span> <span class="token class-name">Pojo</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// this works, but... gah!</span>
		<span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">Pojo</span><span class="token punctuation">)</span> <span class="token class-name">AopContext</span><span class="token punctuation">.</span><span class="token function">currentProxy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">bar</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">bar</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// some logic...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이는 코드를 Spring AOP에 완전히 연결하고 클래스 자체가 AOP에 맞서는 AOP 컨텍스트에서 사용되고 있다는 사실을 인식하게 합니다. 또한 다음 예제와 같이 프록시를 생성할 때 몇 가지 추가 구성이 필요합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Main</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">ProxyFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ProxyFactory</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">SimplePojo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		factory<span class="token punctuation">.</span><span class="token function">addInterface</span><span class="token punctuation">(</span><span class="token class-name">Pojo</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		factory<span class="token punctuation">.</span><span class="token function">addAdvice</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">RetryAdvice</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		factory<span class="token punctuation">.</span><span class="token function">setExposeProxy</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

		<span class="token class-name">Pojo</span> pojo <span class="token operator">=</span> <span class="token punctuation">(</span><span class="token class-name">Pojo</span><span class="token punctuation">)</span> factory<span class="token punctuation">.</span><span class="token function">getProxy</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token comment">// this is a method call on the proxy!</span>
		pojo<span class="token punctuation">.</span><span class="token function">foo</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 AspectJ는 프록시 기반 AOP 프레임워크가 아니기 때문에 이러한 자체 호출 문제가 없다는 점에 유의해야 합니다.</p>