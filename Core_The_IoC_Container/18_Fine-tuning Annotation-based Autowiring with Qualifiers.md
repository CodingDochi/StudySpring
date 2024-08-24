<p><code>@Primary</code>는 하나의 기본 후보를 결정할 수 있는 경우 여러 인스턴스에서 타입별 autowiring을 사용하는 효과적인 방법입니다. 선택 프로세스에 대한 더 많은 제어가 필요한 경우 Spring의 <code>@Qualifier</code> 어노테이션을 사용할 수 있습니다. 한정자(qualifier) 값을 특정 인수와 연관시켜 각 인수에 대해 특정 Bean이 선택되도록 type 일치 세트의 범위를 좁힐 수 있습니다. 가장 간단한 경우에는 다음 예와 같이 일반 설명 값일 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"main"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 개별 생성자 인수 또는 메서드 매개 변수에 <code>@Qualifier</code> 어노테이션을 지정할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">prepare</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"main"</span><span class="token punctuation">)</span> <span class="token class-name">MovieCatalog</span> movieCatalog<span class="token punctuation">,</span>
			<span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieCatalog <span class="token operator">=</span> movieCatalog<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>customerPreferenceDao <span class="token operator">=</span> customerPreferenceDao<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제는 해당 Bean 정의를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>main<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span> <span class="token comment">&lt;!-- (1) --&gt;</span>

		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>action<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span> <span class="token comment">&lt;!-- (2) --&gt;</span>

		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>movieRecommender<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.MovieRecommender<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>(1) <code>main</code> 한정자 값이 있는 Bean은 동일한 값으로 한정된 생성자 인수와 연결됩니다.<br>
(2) <code>action</code> 한정자 값이 있는 Bean은 동일한 값으로 한정된 생성자 인수와 연결됩니다.</p>
<p>대체(fallback) 매칭의 경우 Bean 이름은 기본 한정자 값으로 간주됩니다. 따라서 중첩된 한정자 요소 대신 <code>main</code> <code>id</code>를 사용하여 Bean을 정의하면 동일한 일치 결과를 얻을 수 있습니다. 그러나 이 규칙을 사용하여 특정 Bean을 이름으로 참조할 수 있지만 <code>@Autowired</code>는 기본적으로 선택적(optional) 의미(semantic) 한정자(qualifier)를 사용하는 type-driven 주입에 관한 것입니다. 이는 빈 이름 fallback을 사용하더라도 한정자 값이 항상 type 매칭 세트 내에서 좁아지는 의미를 갖는다는 것을 의미합니다. 고유한 Bean <code>id</code>에 대한 참조를 의미상(semantically)으로 표현하지 않습니다. 좋은 한정자 값은 <code>main</code>, <code>EMEA</code> 또는 <code>persistent</code>이며, 앞의 예와 같은 익명 빈 정의의 경우 자동 생성될 수 있는 빈 <code>id</code>와 독립적인 특정 구성 요소의 특성을 표현합니다.</p>
<p>한정자는 앞서 설명한 대로 typed 컬렉션에도 적용됩니다(예: <code>Set&lt;MovieCatalog&gt;</code>). 이 경우 선언된 한정자에 따라 일치하는 모든 Bean이 컬렉션으로 주입됩니다. 이는 한정자가 고유할 필요가 없음을 의미합니다. 오히려 필터링 기준을 구성합니다. 예를 들어 동일한 한정자 값 "action"을 사용하여 여러 <code>MovieCatalog</code> 빈을 정의할 수 있으며, 이 빈은 모두 <code>@Qualifier("action")</code> 어노테이션이 달린 <code>Set&lt;MovieCatalog&gt;</code>에 주입됩니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
type 매칭 후보 내에서 대상 Bean 이름에 대해 한정자 값을 선택하도록 하면 주입 지점에 <code>@Qualifier</code> 어노테이션이 필요하지 않습니다. 고유하지 않은 종속성 상황에서 다른 resolution 지시자(예: qualifier 또는 primary 마커)가 없는 경우 Spring은 주입 지점 이름(즉, 필드 이름 또는 매개변수 이름)을 대상 Bean 이름과 일치시키고 같은 이름의 후보자가 있으면 선택합니다.</p>
<p>버전 6.1부터는 <code>-parameters</code> Java 컴파일러 플래그가 있어야 합니다.</p>
</blockquote>
<p>즉, 이름으로 어노테이션 기반 주입을 표현하려는 경우, 타입 일치 후보 중에서 Bean 이름으로 선택할 수 있더라도 <code>@Autowired</code>를 기본적으로 사용하지 마십시오. 대신, 고유한 이름으로 특정 컴포넌트를 식별하도록 의미론적으로 정의된 JSR-250 <code>@Resource</code> 주석을 사용하세요. 선언된 타입은 매칭 프로세스와 관련이 없습니다. <code>@Autowired</code>는 다소 다른 의미를 가집니다. 타입에 따라 후보 Bean을 선택한 후 지정된 <code>String</code> 한정자(qualifier) 값은 해당 type-selected 후보 내에서만 고려됩니다(예: 동일한 한정자(qualifier) 레이블로 표시된 Bean과 <code>account</code> 한정자(qualifier)를 매칭시키는 것).</p>
<p>자체적으로 컬렉션, <code>Map</code> 또는 배열 유형으로 정의된 Bean의 경우 <code>@Resource</code>는 고유한 이름으로 특정 컬렉션 또는 배열 Bean을 참조하는 훌륭한 솔루션입니다. 즉, 4.3부터 요소 타입 정보가 <code>@Bean</code> 반환 타입 시그니처 또는 컬렉션 상속 계층에 보존되는 한 Spring의 <code>@Autowired</code> 타입 매칭 알고리즘을 통해 컬렉션, <code>Map</code> 및 배열 타입을 매치시킬 수 있습니다. 이 경우 이전 단락에 설명된 대로 한정자(qualifier) 값을 사용하여 동일한 타입의 컬렉션 중에서 선택할 수 있습니다.</p>
<p>4.3부터 <code>@Autowired</code>는 주입을 위한 self-reference(즉, 현재 주입된 Bean에 대한 참조)도 고려합니다. 자가 주입(self injection)은 대체 방법(fallback)입니다. 다른 구성 요소에 대한 일반적인 종속성이 항상 우선합니다. 그런 의미에서 self-reference은 정규 후보자 선정에 참여하지 않으므로 특히 기본(primary)이 되지 않습니다. 반대로, 항상 가장 낮은 우선순위로 종료됩니다. 실제로는 최후의 수단으로만 self reference를 사용해야 합니다(예: Bean의 트랜잭션 프록시를 통해 동일한 인스턴스에서 다른 메서드를 호출하는 경우). 이러한 시나리오에서는 영향을 받는 메서드를 별도의 위임 Bean으로 분류하는(factoring) 것을 고려하세요. 또는 고유한 이름으로 현재 Bean에 대한 프록시를 다시 얻을 수 있는 <code>@Resource</code>를 사용할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
동일한 구성 클래스에 <code>@Bean</code> 메소드의 결과를 주입하려고 시도하는 것도 사실상 self-reference 시나리오입니다. 실제로 필요한 메소드 시그니처에서 이러한 참조를 Lazily resove하거나(configuration 클래스의 autowired 필드와 반대) 영향을 받는 <code>@Bean</code> 메소드를 <code>static</code>으로 선언하여, 포함하는 구성 클래스 인스턴스 및 해당 라이프사이클에서 분리하십시오. 그렇지 않은 경우 이러한 Bean은 대체 단계(fallback phase)에서만 고려되며 대신 다른 구성 클래스의 일치하는 Bean이 기본(primary) 후보로 선택됩니다(사용 가능한 경우).</p>
</blockquote>
<p><code>@Autowired</code>는 필드, 생성자 및 다중 인수 메서드에 적용되므로 매개변수 수준에서 한정자(qualifier) 어노테이션을 통해 범위를 좁힐 수 있습니다. 대조적으로, <code>@Resource</code>는 단일 인수를 갖는 필드 및 Bean 속성(property) setter 메소드에 대해서만 지원됩니다. 결과적으로, 만약 당신의 주입 대상이 생성자 또는 다중 인수 메서드라면, qualifiers를 사용해야 합니다.<br>
자신만의 사용자 지정 한정자(qualifier) 어노테이션을 만들 수 있습니다. 이렇게 하려면 다음 예제와 같이 어노테이션을 정의하고 정의 내에 <code>@Qualifier</code> 어노테이션을 제공하세요.  </p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>FIELD<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>PARAMETER<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Qualifier</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Genre</span> <span class="token punctuation">{</span>

	<span class="token class-name">String</span> <span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 다음 예제와 같이 자동 연결 필드 및 매개변수에 사용자 정의 한정자(qualifiers)를 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@Genre</span><span class="token punctuation">(</span><span class="token string">"Action"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> actionCatalog<span class="token punctuation">;</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> comedyCatalog<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setComedyCatalog</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Genre</span><span class="token punctuation">(</span><span class="token string">"Comedy"</span><span class="token punctuation">)</span> <span class="token class-name">MovieCatalog</span> comedyCatalog<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>comedyCatalog <span class="token operator">=</span> comedyCatalog<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음으로 후보 Bean 정의에 대한 정보를 제공할 수 있습니다. <code>&lt;qualifier/&gt;</code> 태그를 <code>&lt;bean/&gt;</code> 태그의 하위 요소로 추가한 다음 사용자 정의 한정자(qualifer) 어노테이션과 일치하도록 <code>type</code>과 <code>value</code>을 지정할 수 있습니다. 타입은 어노테이션의 정규화된 클래스 이름과 일치됩니다. 또는 이름이 충돌할 위험이 없는 경우, 편의를 위해 짧은 클래스 이름을 사용할 수 있습니다. 다음 예에서는 두 가지 접근 방식을 모두 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Action<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.Genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Comedy<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>movieRecommender<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.MovieRecommender<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html">클래스패스 스캔 및 관리 컴포넌트</a>에서는 XML로 한정자(qualifer) 메타데이터를 제공하는 것에 대한 어노테이션 기반 대안을 볼 수 있습니다. 특히 <a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html#beans-scanning-qualifiers">어노테이션으로 한정자(qualifer) 메타데이터 제공</a>을 참조하세요.</p>
<p>어떤 경우에는 값 없이 어노테이션을 사용하는 것으로 충분할 수 있습니다. 이는 어노테이션이 보다 일반적인 목적으로 사용되며 여러 가지 다른 타입의 종속성에 적용될 수 있는 경우 유용할 수 있습니다. 예를 들어, 인터넷 연결을 사용할 수 없을 때 검색할 수 있는 오프라인 카탈로그를 제공할 수 있습니다. 먼저 다음 예와 같이 간단한 어노테이션을 정의합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>FIELD<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>PARAMETER<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Qualifier</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Offline</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 다음 예와 같이 autowired될 필드 또는 속성에 어노테이션을 추가합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@Offline</span> <span class="token comment">// (1)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> offlineCatalog<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 이 줄은 <code>@Offline</code> 어노테이션을 추가합니다.<br>
이제 빈 정의에는 다음 예제와 같이 한정자(qualifier) <code>type</code>만 필요합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Offline<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span> <span class="token comment">&lt;!--(1) --&gt;</span>
	<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>(1) 이 요소는 한정자를 지정합니다.</p>
<p>단순 <code>value</code> 속성에 추가하거나 대신 명명된 속성을 허용하는 사용자 정의 한정자 어노테이션을 정의할 수도 있습니다. 자동 연결될 필드나 매개변수에 여러 속성(attribute) 값이 지정되면, 빈 정의는 자동 연결 후보로 간주되는 모든 속성(attribute) 값과 일치해야 합니다. 예를 들어 다음 어노테이션 정의를 고려해 보세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>FIELD<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>PARAMETER<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Qualifier</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">MovieQualifier</span> <span class="token punctuation">{</span>

	<span class="token class-name">String</span> <span class="token function">genre</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

	<span class="token class-name">Format</span> <span class="token function">format</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 경우 <code>Format</code>은 다음과 같이 정의된 열거형(enum)입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">enum</span> <span class="token class-name">Format</span> <span class="token punctuation">{</span>
	VHS<span class="token punctuation">,</span> DVD<span class="token punctuation">,</span> BLURAY
<span class="token punctuation">}</span></code></pre>
<p>자동 연결될 필드에는 사용자 정의 한정자로 어노테이션이 추가되고 다음 예와 같이 <code>genre</code>와 <code>format</code>이라는 두 속성(attribute)에 대한 값이 포함됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@MovieQualifier</span><span class="token punctuation">(</span>format<span class="token operator">=</span><span class="token class-name">Format</span><span class="token punctuation">.</span>VHS<span class="token punctuation">,</span> genre<span class="token operator">=</span><span class="token string">"Action"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> actionVhsCatalog<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@MovieQualifier</span><span class="token punctuation">(</span>format<span class="token operator">=</span><span class="token class-name">Format</span><span class="token punctuation">.</span>VHS<span class="token punctuation">,</span> genre<span class="token operator">=</span><span class="token string">"Comedy"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> comedyVhsCatalog<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@MovieQualifier</span><span class="token punctuation">(</span>format<span class="token operator">=</span><span class="token class-name">Format</span><span class="token punctuation">.</span>DVD<span class="token punctuation">,</span> genre<span class="token operator">=</span><span class="token string">"Action"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> actionDvdCatalog<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Autowired</span>
	<span class="token annotation punctuation">@MovieQualifier</span><span class="token punctuation">(</span>format<span class="token operator">=</span><span class="token class-name">Format</span><span class="token punctuation">.</span>BLURAY<span class="token punctuation">,</span> genre<span class="token operator">=</span><span class="token string">"Comedy"</span><span class="token punctuation">)</span>
	<span class="token keyword">private</span> <span class="token class-name">MovieCatalog</span> comedyBluRayCatalog<span class="token punctuation">;</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 빈 정의는 일치하는(matching) 한정자(qualifier) 값을 포함해야 합니다. 이 예는 또한 <code>&lt;qualifier/&gt;</code> 요소 대신 Bean 메타 속성(attribute)을 사용할 수 있음을 보여줍니다. 사용 가능한 경우 <code>&lt;qualifier/&gt;</code> 요소와 그 속성(attribute)이 우선하지만 자동 연결 메커니즘은 다음 예제의 마지막 두 Bean 정의에서와 같이 해당 한정자가 없으면 <code>&lt;meta/&gt;</code> 태그 내에 제공된 값으로 대체됩니다. :</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>MovieQualifier<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>attribute</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>format<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>VHS<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>attribute</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Action<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>qualifier</span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>qualifier</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>MovieQualifier<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>attribute</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>format<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>VHS<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>attribute</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Comedy<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>qualifier</span><span class="token punctuation">&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>format<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>DVD<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Action<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.SimpleMovieCatalog<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>format<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>BLURAY<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>meta</span> <span class="token attr-name">key</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>genre<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Comedy<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token comment">&lt;!-- inject any dependencies required by this bean --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>