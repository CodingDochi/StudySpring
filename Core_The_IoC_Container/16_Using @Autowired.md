<blockquote>
<p><strong>[Note]</strong><br>
이 섹션에 포함된 예제에서는 JSR 330의 <code>@Inject</code> 어노테이션을 Spring의 <code>@Autowired</code> 어노테이션 대신 사용할 수 있습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/standard-annotations.html">여기</a>를 참조하세요. </p>
</blockquote>
<p>다음 예제와 같이 <code>@Autowired</code> 어노테이션을 생성자에 적용할 수 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customerPreferenceDao <span class="token operator">=</span> customerPreferenceDao<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Framework 4.3부터는 대상 Bean이 시작할 생성자를 하나만 정의하는 경우 이러한 생성자에 대한 <code>@Autowired</code> 어노테이션이 더 이상 필요하지 않습니다. 그러나 여러 생성자를 사용할 수 있고 기본/기본 생성자가 없는 경우 컨테이너에 어떤 것을 사용할지 지시하기 위해 적어도 하나의 생성자에 <code>@Autowired</code> 어노테이션을 달아야 합니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired.html#beans-autowired-annotation-constructor-resolution">생성자 resolution</a>에 대한 토론을 참조하세요.</p>
</blockquote>
<p>다음 예제와 같이 <code>@Autowired</code> 어노테이션을 기존 setter 메서드에 적용할 수도 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 임의의 이름과 여러 인수를 사용하여 메서드에 어노테이션을 적용할 수도 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">prepare</span><span class="token punctuation">(</span><span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">,</span>
			<span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieCatalog <span class="token operator">=</span> movieCatalog<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customerPreferenceDao <span class="token operator">=</span> customerPreferenceDao<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 필드에 <code>@Autowired</code>를 적용할 수도 있고 생성자와 혼합할 수도 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customerPreferenceDao <span class="token operator">=</span> customerPreferenceDao<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>@Autowired</code> 어노테이션이 달린 주입 지점에 사용하는 타입에 따라 대상 component(예: <code>MovieCatalog</code> 또는 <code>CustomerPreferenceDao</code>)가 일관되게 선언되었는지 확인하세요. 그렇지 않으면 런타임 시 "no type match found(유형 일치를 찾을 수 없음)" 오류로 인해 주입이 실패할 수 있습니다.</p>
<p>classpath 스캐닝을 통해 발견된 XML 정의 빈 또는 컴포넌트 클래스의 경우 컨테이너는 일반적으로 구체적인 타입을 미리(up front) 알고 있습니다. 그러나 <code>@Bean</code> 팩토리 메소드의 경우 선언된 반환 유형이 충분히 표현되는지 확인해야 합니다. 여러 인터페이스를 구현하는 components 또는 구현 type으로 잠재적으로 참조되는 components의 경우 팩토리 메서드에서 가장 구체적인 반환 type을 선언하는 것을 고려하십시오(적어도 Bean을 참조하는 주입 지점에서 요구하는 만큼 구체적으로).</p>
</blockquote>
<p>또한 다음 예제와 같이 해당 타입의 배열(array)이 필요한 필드나 메소드에 <code>@Autowired</code> 어노테이션을 추가하여 <code>ApplicationContext</code>에서 특정 유형의 모든 Bean을 제공하도록 Spring에 지시할 수도 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span><span class="token punctuation">[</span><span class="token punctuation">]</span> movieCatalogs<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 typed 컬렉션에도 동일하게 적용됩니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">Set</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieCatalog</span><span class="token punctuation">&gt;</span></span> movieCatalogs<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieCatalogs</span><span class="token punctuation">(</span><span class="token class-name">Set</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieCatalog</span><span class="token punctuation">&gt;</span></span> movieCatalogs<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieCatalogs <span class="token operator">=</span> movieCatalogs<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
대상 Bean은 <code>org.springframework.core.Ordered</code> 인터페이스를 구현하거나 array 또는 list의 항목을 특정 순서로 정렬하려는 경우 <code>@Order</code> 또는 표준 <code>@Priority</code> 어노테이션을 사용할 수 있습니다. 그렇지 않은 경우 해당 순서는 컨테이너에 해당 대상 Bean 정의의 등록 순서를 따릅니다.</p>
<p>잠재적으로 개별 Bean 정의에 대해(동일한 Bean 클래스를 사용하는 여러 정의의 경우) 대상 클래스 레벨 및 <code>@Bean</code> 메소드에서 <code>@Order</code> 어노테이션을 선언할 수 있습니다. <code>@Order</code> 값은 주입 지점의 우선 순위에 영향을 미칠 수 있지만 종속 관계 및 <code>@DependsOn</code> 선언에 의해 결정되는 직교 문제인 싱글톤 시작 순서에는 영향을 주지 않는다는 점에 유의하세요.</p>
</blockquote>
<hr>
<p><img src="https://www.tomitribe.com/wp-content/uploads/2019/06/Figure-2-768x429.png"> <img src="https://www.tomitribe.com/wp-content/uploads/2019/06/Sequence-Diagram-1024x812.jpg"></p>
<p>참고 : <a href="https://www.tomitribe.com/blog/singleton-session-beans-eager-initialization-and-ordering/">https://www.tomitribe.com/blog/singleton-session-beans-eager-initialization-and-ordering/</a></p>
<hr>
<blockquote>
<p>표준 <code>jakarta.annotation.Priority</code> 어노테이션은 메소드에서 선언할 수 없으므로 <code>@Bean</code> 수준에서는 사용할 수 없습니다. 그 의미(semantics)는 각 타입에 대한 단일 Bean의 <code>@Primary</code>와 결합된 <code>@Order</code> 값을 통해 모델링될 수 있습니다.</p>
</blockquote>
<hr>
<p>"<code>@Priority</code>" 주석이 제공하는 우선순위의 의미의미는 "<code>@Order</code>" 값과 "<code>@Primary</code>"이라는 다른 두 가지 주석을 조합하여 모델링될 수 있습니다.</p>
<ul>
<li>"<code>@Order</code>"는 빈들의 순서를 결정하는 데 사용됩니다. 작성된 순서대로 빈을 호출하거나 처리할 수 있도록 하는 데 도움이 됩니다. 이는 우선순위 개념과 유사합니다.</li>
<li>"<code>@Primary</code>"는 여러 개의 빈이 같은 타입을 가질 때, 기본적으로 선택되는 빈을 지정하는 데 사용됩니다. 이는 특정한 우선순위를 지정하는 데에 활용될 수 있습니다.</li>
</ul>
<hr>
<p>예상되는 키 타입이 <code>String</code>이면 type이 지정된 <code>Map</code> 인스턴스도 자동 연결될 수 있습니다. 다음 예제와 같이 맵 값에는 예상되는 유형의 모든 Bean이 포함되고 키에는 해당 Bean 이름이 포함됩니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">MovieCatalog</span><span class="token punctuation">&gt;</span></span> movieCatalogs<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieCatalogs</span><span class="token punctuation">(</span><span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">MovieCatalog</span><span class="token punctuation">&gt;</span></span> movieCatalogs<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieCatalogs <span class="token operator">=</span> movieCatalogs<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>기본적으로 주어진 주입 지점에 대해 일치하는 후보 Bean이 없으면 autowiring이 실패합니다. 선언된 배열, 컬렉션 또는 맵의 경우 일치하는 요소가 하나 이상 있어야 합니다.</p>
<p>기본 동작은 주석이 달린 메서드와 필드를 필수 종속성을 나타내는 것으로 처리하는 것입니다. 다음 예에서 설명한 대로 이 동작을 변경할 수 있습니다. 즉, <code>@Autowired</code>의 <code>required</code> 속성을 <code>false</code>로 설정하여 프레임워크가 충족되지 않는 주입 지점을 건너뛰도록 할 수 있습니다.</p>
<pre><code class="language-java">
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span><span class="token punctuation">(</span>required <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
필수가 아닌 메서드는 종속성(또는 인수가 여러 개인 경우 종속성 중 하나)을 사용할 수 없는 경우 전혀 호출되지 않습니다. 이러한 경우 필수가 아닌 필드는 전혀 채워지지 않으며 기본값은 그대로 유지됩니다.</p>
<p>즉,<code>required</code> 속성(attribute)을 <code>false</code>로 설정하면 해당 속성(propertu)이 autowiring 목적으로 선택 사항임(optional)을 나타내며, autowiring이 불가능한 경우 해당 속성은 무시됩니다. 이를 통해 종속성 주입을 통해 선택적으로 재정의할 수 있는 기본값을 속성에 할당할 수 있습니다.</p>
</blockquote>
<p>주입된 생성자 및 팩토리 메서드 인수는 <code>@Autowired</code>의 <code>required</code> 속성(attribute)이 잠재적으로 여러 생성자를 처리할 수 있는 Spring의 생성자 resolution 알고리즘으로 인해 다소 다른 의미를 갖기 때문에 특별한 경우입니다. 생성자 및 팩터리 메서드 인수는 기본적으로 효과적으로 필요하지만, 단일 생성자 시나리오에서 몇 가지 특수한 규칙, 이를 테면 매칭되는 빈을 사용할 수 없는 경우 비어 있는 인스턴스에 resolving 되는 다중 요소 주입 지점(배열, 컬렉션, 맵)과 같은 것들입니다. 이것은 모든 의존성이 고유한한 다중 인수 생성자로 선언될 수 있는 공통 구현 패턴을 허용합니다. 예를 들어 <code>@Autowired</code> 어노테이션 없이 단일 public 생성자로 선언되는 것입니다.  </p>
<blockquote>
<p><strong>[Note]</strong><br>
주어진 빈 클래스의 생성자 중 하나만 <code>required</code> 속성(attribute)이 <code>true</code>로 설정된 <code>@Autowired</code>를 선언할 수 있으며 이는 Spring 빈으로 사용될 때 생성자가 autowire됨을 나타냅니다. 결과적으로 <code>required</code> 속성(attribute)이 기본값인 <code>true</code>로 유지되면 단일 생성자에만 <code>@Autowired</code> 어노테이션이 추가될 수 있습니다. 여러 생성자가 어노테이션을 선언하는 경우 autowiring 후보로 간주되기 위해 모두 <code>require=false</code>로 선언해야 합니다(XML의 <code>autowire=constructor</code>와 유사). Spring 컨테이너에서 Bean을 일치시켜 충족할 수 있는 종속성이 가장 많은 생성자가 선택됩니다. 어떤 후보도 만족할 수 없으면 기본(primary)/기본(default) 생성자(있는 경우)가 사용됩니다. 마찬가지로, 클래스가 여러 생성자를 선언했지만 그 중 어느 것도 <code>@Autowired</code>로 어노테이션 처리되지 않은 경우 기본(primary)/기본(default) 생성자(있는 경우)가 사용됩니다. 클래스가 시작하기 위해 단일 생성자만 선언하는 경우 어노테이션이 추가되지 않은 경우에도 항상 사용됩니다. 어노테이션이 달린 생성자는 public일 필요는 없습니다.</p>
</blockquote>
<p>또는 다음 예제와 같이 Java 8의 <code>java.util.Optional</code>을 통해 특정 종속성의 비필수 특성을 표현할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">MovieFinder</span><span class="token punctuation">&gt;</span></span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Spring Framework 5.0부터 <code>@Nullable</code> 어노테이션(모든 패키지의 모든 종류—예: JSR-305의 <code>javax.annotation.Nullable</code>)을 사용하거나 Kotlin 내장 null 안전 지원을 활용할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Nullable</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>잘 알려진 resolve가능한 종속성인 <code>BeanFactory</code>, <code>ApplicationContext</code>, <code>Environment</code>, <code>ResourceLoader</code>, <code>ApplicationEventPublisher</code> 및 <code>MessageSource</code>에 <code>@Autowired</code>를 사용할 수도 있습니다. 이러한 인터페이스와 <code>ConfigurableApplicationContext</code> 또는 <code>ResourcePatternResolver</code>와 같은 확장 인터페이스는 특별한 설정이 필요 없이 자동으로 확인됩니다. 다음 예제에서는 <code>ApplicationContext</code> 객체를 자동 연결합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">private</span> <span class="token class-name">ApplicationContext</span> context<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span>
</code></pre>
<blockquote>
<p><code>@Autowired</code>, <code>@Inject</code>, <code>@Value</code> 및 <code>@Resource</code> 어노테이션은 Spring <code>BeanPostProcessor</code> 구현에 의해 처리됩니다. 이는 자신의 <code>BeanPostProcessor</code> 또는 <code>BeanFactoryPostProcessor</code> type(있는 경우) 내에서 이러한 어노테이션을 적용할 수 없음을 의미합니다. 이러한 유형은 XML 또는 Spring <code>@Bean</code> 메소드를 사용하여 명시적으로 '연결'되어야 합니다.</p>
</blockquote>