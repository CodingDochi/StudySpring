<p>@AspectJ 지원이 활성화되면 @AspectJ aspect(<code>@Aspect</code> 어노테이션 포함)인 클래스를 사용하여 애플리케이션 컨텍스트에 정의된 모든 Bean이 Spring에 의해 자동으로 감지되고 Spring AOP를 구성(configure)하는 데 사용됩니다. 다음 두 예는 별로 유용하지 않은 aspect에 필요한 최소한의 단계를 보여줍니다.</p>
<p>두 예제 중 첫 번째는 <code>@Aspect</code>로 어노테이션이 달린 Bean 클래스를 가리키는 애플리케이션 컨텍스트의 일반 Bean 정의를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myAspect<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.xyz.NotVeryUsefulAspect<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token comment">&lt;!-- configure properties of the aspect here --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>두 예제 중 두 번째는 <code>@Aspect</code> 어노테이션이 달린 <code>NotVeryUsefulAspect</code> 클래스 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">package</span> <span class="token namespace">com<span class="token punctuation">.</span>xyz</span><span class="token punctuation">;</span>

<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>aspectj<span class="token punctuation">.</span>lang<span class="token punctuation">.</span>annotation</span><span class="token punctuation">.</span><span class="token class-name">Aspect</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Aspect</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NotVeryUsefulAspect</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>Aspect(<code>@Aspect</code>로 어노테이션이 달린 클래스)는 다른 클래스와 마찬가지로 메서드와 필드를 가질 수 있습니다. 또한 포인트컷, 어드바이스 및 introduction(inter-type) 선언을 포함할 수도 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<em>component scan을 통해 aspect 자동 감지</em></p>
<p><code>@Configuration</code> 클래스의 <code>@Bean</code> 메서드를 통해 Spring XML 구성에서 일반 Bean으로 aspect 클래스를 등록하거나 다른 Spring 관리 Bean과 마찬가지로 classpath 검색을 통해 Spring이 이를 자동 감지하도록 할 수 있습니다. 그러나 <code>@Aspect</code> 어노테이션은 classpath의 자동 감지에 충분하지 않습니다. 이를 위해서는 별도의 <code>@Component</code> 어노테이션(또는 Spring 컴포넌트 스캐너의 규칙에 따라 자격(qualifies)을 갖춘 사용자 정의 스테레오타입 어노테이션)을 추가해야 합니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
<em>다른 aspect와 함께 aspect를 advise합니까?</em><br>
Spring AOP에서는 aspect 자체가 다른 aspect의 advise 대상이 될 수 없습니다. 클래스의 <code>@Aspect</code> 어노테이션은 이를 aspect으로 표시하므로 자동 프록시(auto-proxying)에서 제외됩니다.</p>
</blockquote>