<p>Spring의 Java 구성 지원의 핵심 아티팩트는 <code>@Configuration</code> 어노테이션이 달린 클래스와 <code>@Bean</code> 어노테이션이 달린 메서드입니다.</p>
<p><code>@Bean</code> 어노테이션은 메서드가 Spring IoC 컨테이너에서 관리할 새 객체를 인스턴스화, 구성 및 초기화함을 나타내는 데 사용됩니다. Spring의 <code>&lt;beans/&gt;</code> XML 구성에 익숙한 사람들에게 <code>@Bean</code> 어노테이션은 <code>&lt;bean/&gt;</code> 요소와 동일한 역할을 합니다. Spring <code>@Component</code>와 함께 <code>@Bean</code> 어노테이션이 달린 메서드를 사용할 수 있습니다. 그러나 <code>@Configuration</code> Bean과 함께 가장 자주 사용됩니다.</p>
<p><code>@Configuration</code>으로 클래스에 어노테이션을 달면 해당 클래스의 주요 목적이 Bean 정의의 소스라는 것을 나타냅니다. 또한 <code>@Configuration</code> 클래스를 사용하면 동일한 클래스에서 다른 <code>@Bean</code> 메서드를 호출하여 bean 간 종속성(inter dependencies)을 정의할 수 있습니다. 가능한 가장 간단한 <code>@Configuration</code> 클래스는 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">MyServiceImpl</span> <span class="token function">myService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">MyServiceImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 <code>AppConfig</code> 클래스는 다음 Spring <code>&lt;beans/&gt;</code> XML과 동일합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>myService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme.services.MyServiceImpl<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<h3 id="전체-configuration과-lite-bean-모드">전체 @Configuration과 "lite" @Bean 모드?</h3>
<p><code>@Configuration</code>으로 어노테이션을 달지 않은 클래스 내에서 <code>@Bean</code> 메소드가 선언되면 해당 메소드는 "lite" 모드에서 처리된다고 합니다. <code>@Configuration</code>으로 어노테이션이 추가되지 않은 Bean에 선언된 Bean 메소드는 포함 클래스의 기본 목적이 다르고 @Bean 메소드가 일종의 보너스인 "lite"로 간주됩니다. 예를 들어 서비스 구성 요소는 적용 가능한 각 컴포넌트 클래스에 대한 추가 <code>@Bean</code> 메서드를 통해 컨테이너에 관리 뷰를 노출할 수 있습니다. 이러한 시나리오에서 <code>@Bean</code> 메소드는 범용 팩토리 메소드 메커니즘입니다.</p>
<p>전체 <code>@Configuration</code>과 달리 lite <code>@Bean</code> 메소드는 Bean 간 종속성을 선언할 수 없습니다. 대신, 포함된 컴포넌트의 내부 상태와 선택적으로 선언할 수 있는 인수에 대해 작동합니다. 따라서 이러한 <code>@Bean</code> 메소드는 다른 <code>@Bean</code> 메소드를 호출해서는 안 됩니다. 각각의 메소드는 문자 그대로 특별한 런타임 의미가 없는, 특정 Bean 참조에 대한 팩토리 메소드일 뿐입니다. 여기서 긍정적인 side-effect는 런타임에 CGLIB 서브클래싱을 적용할 필요가 없으므로 클래스 디자인 측면에서 제한이 없다는 것입니다(즉, 포함 클래스가 <code>final</code> 클래스일 수 있음 등).</p>
<p>일반적인 시나리오에서 <code>@Bean</code> 메소드는 <code>@Configuration</code> 클래스 내에서 선언되어 "전체" 모드가 항상 사용되며 따라서 교차 메소드 참조가 컨테이너의 수명주기 관리로 리디렉션됩니다. 이는 동일한 <code>@Bean</code> 메소드가 일반 Java 호출을 통해 실수로 호출되는 것을 방지하여 "lite" 모드에서 작동할 때 추적하기 어려울 수 있는 미묘한 버그를 줄이는 데 도움이 됩니다.</p>
</blockquote>
<p><code>@Bean</code> 및 <code>@Configuration</code> 어노테이션은 다음 섹션에서 자세히 설명합니다. 그러나 먼저 Java 기반 구성을 사용하여 스프링 컨테이너를 생성하는 다양한 방법을 다룹니다.</p>