<p><a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/core/env/Environment.html"><code>Environment</code></a> 인터페이스는 애플리케이션 환경의 두 가지 주요 측면인 <a href="https://docs.spring.io/spring-framework/reference/core/beans/environment.html#beans-definition-profiles">profile</a>과 <a href="https://docs.spring.io/spring-framework/reference/core/beans/environment.html#beans-property-source-abstraction">properties</a>을 모델링하는 컨테이너에 통합된 추상화입니다.</p>
<p>프로필은 주어진 프로필이 활성화된 경우에만 컨테이너에 등록되는 명명된(named) 논리적(logical) 빈 정의 그룹입니다. Bean은 XML 또는 어노테이션으로 정의된 프로필에 할당될 수 있습니다. 프로필과 관련된 <code>Environment</code> 객체의 역할은 현재 활성화된 프로필(있는 경우)과 기본적으로 활성화되어야 하는 프로필(있는 경우)을 결정하는 것입니다.</p>
<p>속성(Properties)은 거의 모든 애플리케이션에서 중요한 역할을 하며 properties 파일, JVM 시스템 속성(properties), 시스템 환경 변수, JNDI, 서블릿 컨텍스트 매개변수, 임시(ad-hoc) <code>Properties</code> 객체, <code>Map</code> 객체 등 다양한 소스에서 발생할 수 있습니다. 속성(properties)과 관련된 <code>Environment</code> 객체의 역할은 사용자에게 속성(property) 소스를 구성하고 속성(property)을 resolve하기 위한 편리한 서비스 인터페이스를 제공하는 것입니다.</p>
<h1 id="bean-definition-profiles">Bean Definition Profiles</h1>
<p>Bean 정의 프로파일은 다양한 환경(environment)에서 다양한 Bean을 등록할 수 있는 메커니즘을 핵심(core) 컨테이너에 제공합니다. "환경"이라는 단어는 사용자마다 다른 의미를 가질 수 있으며 이 기능은 다음을 포함한 다양한 사용 사례에 도움이 될 수 있습니다.</p>
<ul>
<li>
<p>개발 중에 인메모리 데이터 소스에 대해 작업하는 것 vs(versus) QA 또는 프로덕션 중에 JNDI에서 동일한 데이터 소스를 검색하는 것</p>
</li>
<li>
<p>성능 환경(performance envrionment)에 애플리케이션을 배포하는 경우에만 모니터링 인프라를 등록하기</p>
</li>
<li>
<p>customer A 대 customer B 배포에 대해 사용자 정의된 Bean 구현을 등록하기</p>
</li>
</ul>
<p><code>DataSource</code>가 필요한 실제 애플리케이션의 첫 번째 사용 사례를 고려해보세요. 테스트 환경에서 구성은 다음과 유사할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">EmbeddedDatabaseType</span><span class="token punctuation">.</span>HSQL<span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"my-schema.sql"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"my-test-data.sql"</span><span class="token punctuation">)</span>
		<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 애플리케이션의 데이터 소스가 프로덕션 애플리케이션 서버의 JNDI 디렉토리에 등록되어 있다고 가정하고 이 애플리케이션을 QA 또는 프로덕션 환경에 배포할 수 있는 방법을 살펴보겠습니다. 이제 <code>dataSource</code> 빈은 다음 목록과 같습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span>destroyMethod <span class="token operator">=</span> <span class="token string">""</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
	<span class="token class-name">Context</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">InitialContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token class-name">DataSource</span><span class="token punctuation">)</span> ctx<span class="token punctuation">.</span><span class="token function">lookup</span><span class="token punctuation">(</span><span class="token string">"java:comp/env/jdbc/datasource"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>문제는 현재 환경(environment)에 따라 이 두 가지 변형을 어떻게 전환(switch)할지입니다. 시간이 지남에 따라 Spring 사용자는 이 작업을 수행하기 위한 여러 가지 방법을 고안했습니다. 일반적으로 환경 변수(environment variable) 값에 따라 올바른 구성 파일 경로(configuration file path)를 resolve하는 <code>${placeholder}</code> 토큰이 포함된 XML <code>&lt;import/&gt;</code> 문과 시스템 환경 변수의 조합을 사용합니다. Bean 정의 프로파일은 이 문제에 대한 솔루션을 제공하는 핵심 컨테이너 기능입니다.</p>
<p>환경별 빈 정의의 이전 예제에 표시된 사용 사례를 일반화하면, 특정 컨텍스트에서는 특정 빈 정의를 등록해야 하지만 다른 컨텍스트에서는 등록하지 않아도 됩니다. 상황 A에서는 빈 정의의 특정 프로파일을 등록하고 상황 B에서는 다른 프로파일을 등록하고 싶다고 말할 수 있습니다. 우리는 이러한 요구를 반영(reflect)하기 위해 구성(configuration)을 업데이트하는 것부터 시작합니다.</p>
<h2 id="using-profile">Using @Profile</h2>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/annotation/Profile.html"><code>@Profile</code></a> 어노테이션을 사용하면 하나 이상의 지정된 프로필이 활성 상태일 때 컴포넌트를 등록할 수 있음을 나타낼 수 있습니다. 이전 예제를 사용하여 다음과 같이 <code>dataSource</code> 구성을 다시 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"development"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">StandaloneDataConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">EmbeddedDatabaseType</span><span class="token punctuation">.</span>HSQL<span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"classpath:com/bank/config/sql/schema.sql"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"classpath:com/bank/config/sql/test-data.sql"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"production"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">JndiDataConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span>destroyMethod <span class="token operator">=</span> <span class="token string">""</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
		<span class="token class-name">Context</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">InitialContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token class-name">DataSource</span><span class="token punctuation">)</span> ctx<span class="token punctuation">.</span><span class="token function">lookup</span><span class="token punctuation">(</span><span class="token string">"java:comp/env/jdbc/datasource"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@Bean(destroyMethod = "")</code>은 기본 destroy 메소드 추론을 비활성화합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
앞서 언급했듯이 <code>@Bean</code> 메소드를 사용하면 일반적으로 Spring의 <code>JndiTemplate</code>/<code>JndiLocatorDelegate</code> 헬퍼 또는 <code>JndiObjectFactoryBean</code> 변형이 아닌, 이전에 표시된 JNDI <code>InitialContext</code> 사용법을 사용하여, 프로그래밍 방식의 JNDI 조회를 사용하도록 선택합니다. 그러면 반환 유형을 다음과 같이 <code>FactoryBean</code> 타입으로 선언해야 합니다. </p>
</blockquote>
<p>프로필 문자열에는 간단한 프로필 이름(예: <code>production</code>) 또는 프로필 표현식(expression)이 포함될 수 있습니다. 프로필 표현식을 사용하면 보다 복잡한 프로필 논리를 표현할 수 있습니다(예: <code>production &amp; us-east</code>). 프로필 표현식에서는 다음 연산자가 지원됩니다.</p>
<ul>
<li><code>!</code>: 프로필의 논리적 <code>NOT</code></li>
<li><code>&amp;</code>: 프로필의 논리 <code>AND</code></li>
<li><code>|</code>: 프로필의 논리적 <code>OR</code></li>
</ul>
<blockquote>
<p><strong>[Note]</strong><br>
괄호를 사용하지 않고 <code>&amp;</code>와 <code>|</code>를 혼합할 수 없습니다. 예를 들어, <code>production &amp; us-east | eu-central</code>은 유효한 표현이 아닙니다. <code>production &amp; (us-east | eu-central)</code>로 표현해야 합니다.</p>
</blockquote>
<p>사용자 정의 구성(composed)된 어노테이션을 생성하기 위해 <code>@Profile</code>을 <a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html#beans-meta-annotations">메타 어노테이션</a>으로 사용할 수 있습니다. 다음 예에서는 <code>@Profile("production")</code>에 대한 드롭인 대체로 사용할 수 있는 사용자 정의 <code>@Production</code> 어노테이션을 정의합니다.</p>
<hr>
<p>"Drop-in"은 보통 컴퓨터 과학 분야에서 사용되며, 기존의 구현체나 시스템을 수정하지 않고도 새로운 것으로 바꿀 수 있는 것을 가리킵니다. 즉, <code>@Production</code>을 <code>@Profile("production")</code>으로 대체하여 코드를 변경하지 않고도 동일한 기능을 수행할 수 있다는 것을 의미합니다.</p>
<hr>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"production"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Production</span> <span class="token punctuation">{</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>@Configuration</code> 클래스가 <code>@Profile</code>로 표시되면 지정된 프로필 중 하나 이상이 활성화되지 않는 한 해당 클래스와 연결된 모든 <code>@Bean</code> 메서드 및 <code>@Import</code> 어노테이션이 무시(bypass)됩니다. <code>@Component</code> 또는 <code>@Configuration</code> 클래스가 <code>@Profile({"p1", "p2"})</code>로 표시된 경우 해당 클래스는 'p1' 또는 'p2' 프로필이 활성화되지 않으면 등록되거나 처리되지 않습니다. 지정된 프로필에 NOT 연산자(<code>!</code>)가 접두사(prefixed)로 붙은 경우 프로필이 활성화되지 않은 경우에만 어노테이션이 달린 요소가 등록됩니다. 예를 들어 <code>@Profile({"p1", "!p2"})</code>이 주어지면 'p1' 프로필이 활성 상태이거나 'p2' 프로필이 활성 상태가 아닌 경우 등록이 발생합니다.</p>
</blockquote>
<p><code>@Profile</code>은 다음 예제와 같이 구성(configuration) 클래스의 특정 Bean 하나만 포함하도록 메서드 수준에서 선언할 수도 있습니다(예: 특정 Bean의 대체 변형).</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span><span class="token string">"dataSource"</span><span class="token punctuation">)</span>
	<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"development"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">standaloneDataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">EmbeddedDatabaseType</span><span class="token punctuation">.</span>HSQL<span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"classpath:com/bank/config/sql/schema.sql"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"classpath:com/bank/config/sql/test-data.sql"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span><span class="token punctuation">(</span><span class="token string">"dataSource"</span><span class="token punctuation">)</span>
	<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"production"</span><span class="token punctuation">)</span> <span class="token comment">// (2)</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">jndiDataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
		<span class="token class-name">Context</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">InitialContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> <span class="token punctuation">(</span><span class="token class-name">DataSource</span><span class="token punctuation">)</span> ctx<span class="token punctuation">.</span><span class="token function">lookup</span><span class="token punctuation">(</span><span class="token string">"java:comp/env/jdbc/datasource"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>standaloneDataSource</code> 메서드는 <code>development</code> 프로필에서만 사용할 수 있습니다.<br>
(2) <code>jndiDataSource</code> 메소드는 <code>production</code> 프로필에서만 사용할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Bean</code> 메소드에 <code>@Profile</code>을 사용하면 특별한 시나리오가 적용될 수 있습니다. 동일한 Java 메소드 이름의 <code>@Bean</code> 메소드가 오버로드된 경우(생성자 오버로딩과 유사) <code>@Profile</code> 조건은 오버로드된 모든 메소드에서 일관되게 선언되어야 합니다. 조건이 일치하지 않는 경우 오버로드된 메서드 중 첫 번째 선언의 조건만 중요합니다. 따라서 <code>@Profile</code>을 사용하여 특정 인수 시그니처가 있는 오버로드된 메서드를 다른 메서드 대신 선택할 수 없습니다. 동일한 빈에 대한 모든 팩토리 메소드 간의 resolution은 생성 시 Spring의 생성자 resolution 알고리즘을 따릅니다.</p>
<p>다른 프로필 조건으로 대체 Bean을 정의하려면 이전 예제에 표시된 대로 <code>@Bean</code> 이름 속성(attribute)을 사용하여 동일한 Bean 이름을 가리키는 고유한(distinct) Java 메소드 이름을 사용하십시오. 인수 시그니처가 모두 동일한 경우(예를 들어 모든 변형에, 인수가 없는(no-arg) 팩토리 메서드가 있는 경우) 이는 처음에 유효한 Java 클래스에서 이러한 arrangement을 나타내는 유일한 방법입니다(특정 이름 및 인수 시그니처에는 하나의 메서드만 있을 수 있으므로).</p>
</blockquote>
<h2 id="xml-bean-definition-profiles">XML Bean Definition Profiles</h2>
<p>XML 대응물은 <code>&lt;beans&gt;</code> 요소의 <code>profile</code> 속성(attribute)입니다. 이전 샘플 구성(configuration)은 다음과 같이 두 개의 XML 파일로 다시 작성할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>development<span class="token punctuation">"</span></span>
	<span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jdbc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jdbc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>embedded-database</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/bank/config/sql/schema.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/bank/config/sql/test-data.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">jdbc:</span>embedded-database</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>production<span class="token punctuation">"</span></span>
	<span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jee</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jee<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jee:</span>jndi-lookup</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">jndi-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>java:comp/env/jdbc/datasource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>다음 예제에서 볼 수 있듯이 동일한 파일 내에서 <code>&lt;beans/&gt;</code> 요소를 중첩하여, 분할되는 것을 방지하는 것도 가능합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jdbc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jdbc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jee</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jee<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- other bean definitions --&gt;</span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>development<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>embedded-database</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/bank/config/sql/schema.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jdbc:</span>script</span> <span class="token attr-name">location</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>classpath:com/bank/config/sql/test-data.sql<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">jdbc:</span>embedded-database</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>production<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jee:</span>jndi-lookup</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">jndi-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>java:comp/env/jdbc/datasource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>spring-bean.xsd</code>는 파일의 마지막 요소만 허용하도록 제한되었습니다. 이는 XML 파일을 복잡하게 만들지 않고 유연성을 제공하는 데 도움이 됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
XML 대응 부분은 앞에서 설명한 프로필 표현식을 지원하지 않습니다. 그러나 <code>!</code> 연산자를 사용하여 프로필을 무효화하는 것이 가능합니다. 다음 예와 같이 프로필을 중첩하여 논리적 "and"를 적용하는 것도 가능합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jdbc</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jdbc<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>jee</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/jee<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- other bean definitions --&gt;</span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>production<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">profile</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>us-east<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jee:</span>jndi-lookup</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>dataSource<span class="token punctuation">"</span></span> <span class="token attr-name">jndi-name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>java:comp/env/jdbc/datasource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>앞의 예에서는 <code>production</code> 프로필과 <code>us-east</code> 프로필이 모두 활성화된 경우 <code>dataSource</code> Bean이 노출됩니다.</p>
</blockquote>
<h2 id="activating-a-profile">Activating a Profile</h2>
<p>이제 구성(configuration)을 업데이트했으므로 여전히 어떤 프로필이 활성화 되어 있는지 Spring에 지시해야 합니다. 지금 당장 샘플 애플리케이션을 시작했다면 컨테이너가 <code>dataSource</code>라는 Spring 빈을 찾을 수 없기 때문에 <code>NoSuchBeanDefinitionException</code>이 발생하는 것을 보게 될 것입니다.</p>
<p>프로필 활성화는 여러 가지 방법으로 수행할 수 있지만 가장 간단한 방법은 <code>ApplicationContext</code>를 통해 사용할 수 있는 <code>Environment</code> API에 대해 프로그래밍 방식으로 수행하는 것입니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">AnnotationConfigApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">AnnotationConfigApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
ctx<span class="token punctuation">.</span><span class="token function">getEnvironment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setActiveProfiles</span><span class="token punctuation">(</span><span class="token string">"development"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
ctx<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span><span class="token class-name">SomeConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">StandaloneDataConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">JndiDataConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
ctx<span class="token punctuation">.</span><span class="token function">refresh</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>또한 시스템 환경 변수, JVM 시스템 속성(property), <code>web.xml</code>의 서블릿 컨텍스트 매개변수 또는 JNDI의 항목(entry)을 통해(<a href="https://docs.spring.io/spring-framework/reference/core/beans/environment.html#beans-property-source-abstraction"><code>PropertySource</code> 추상화</a> 참조) 지정할 수 있는 <code>spring.profiles.active</code> 속성(property)을 통해 선언적으로 프로필을 활성화할 수도 있습니다. 통합 테스트에서는 <code>spring-test</code> 모듈의 <code>@ActiveProfiles</code> 어노테이션을 사용하여 활성 프로필을 선언할 수 있습니다(<a href="https://docs.spring.io/spring-framework/reference/testing/testcontext-framework/ctx-management/env-profiles.html">환경 프로필을 사용한 컨텍스트 구성</a> 참조).</p>
<p>프로필은 "둘 중 하나(either-or)"라는 제안이 아닙니다. 한 번에 여러 프로필을 활성화할 수 있습니다. 프로그래밍 방식으로 <code>String…</code>​  가변인수(varargs)를 허용하는 <code>setActiveProfiles()</code> 메서드에 여러 프로필 이름을 제공할 수 있습니다. 다음 예에서는 여러 프로필을 활성화합니다.</p>
<pre><code class="language-java">ctx<span class="token punctuation">.</span><span class="token function">getEnvironment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setActiveProfiles</span><span class="token punctuation">(</span><span class="token string">"profile1"</span><span class="token punctuation">,</span> <span class="token string">"profile2"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>선언적으로 <code>spring.profiles.active</code>는 다음 예제와 같이 쉼표로 구분된 프로필 이름 목록을 허용할 수 있습니다.</p>
<pre><code class="language-null">-Dspring.profiles.active="profile1,profile2"</code></pre>
<h2 id="default-profile">Default Profile</h2>
<p>기본 프로필은 활성화된 프로필이 없는 경우 활성화되는 프로필을 나타냅니다. 다음 예를 고려하십시오.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"default"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">DefaultDataConfig</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">EmbeddedDatabaseType</span><span class="token punctuation">.</span>HSQL<span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">addScript</span><span class="token punctuation">(</span><span class="token string">"classpath:com/bank/config/sql/schema.sql"</span><span class="token punctuation">)</span>
			<span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><a href="https://docs.spring.io/spring-framework/reference/core/beans/environment.html#beans-definition-profiles-enable">활성화된 프로필이 없으면</a> <code>dataSource</code>가 생성됩니다. 이는 하나 이상의 Bean에 대한 기본 정의를 제공하는 방법으로 볼 수 있습니다. 프로필이 활성화되어 있으면 기본 프로필이 적용되지 않습니다.</p>
<p>기본 프로필의 이름은 <code>default</code>입니다. <code>Environment</code>에서 <code>setDefaultProfiles()</code>를 사용하거나 선언적으로 <code>spring.profiles.default</code> 속성(property)을 사용하여 기본 프로필의 이름을 변경할 수 있습니다.</p>
<h1 id="propertysource-abstraction"><code>PropertySource</code> Abstraction</h1>
<p>Spring의 <code>Environment</code> 추상화는 구성 가능한 속성(property) 소스 계층에 대한 검색 작업을 제공합니다. 다음 목록을 고려하십시오.</p>
<pre><code class="language-java"><span class="token class-name">ApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GenericApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">Environment</span> env <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getEnvironment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">boolean</span> containsMyProperty <span class="token operator">=</span> env<span class="token punctuation">.</span><span class="token function">containsProperty</span><span class="token punctuation">(</span><span class="token string">"my-property"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"Does my environment contain the 'my-property' property? "</span> <span class="token operator">+</span> containsMyProperty<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>이전 코드 조각에서 우리는 <code>my-property</code> 속성(property)이 현재 환경에 대해 정의되어 있는지 Spring에 묻는 높은 수준의 방법을 볼 수 있습니다. 이 질문에 대답하기 위해 <code>Environment</code> 객체는 일련의 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/core/env/PropertySource.html"><code>PropertySource</code></a> 객체에 대해 검색을 수행합니다. <code>PropertySource</code>는 키-값 쌍의 모든 소스에 대한 간단한 추상화이며 Spring의 <code>StandardEnvironment</code>는 두 개의 PropertySource 객체로 구성됩니다. 하나는 JVM 시스템 속성(property) 집합(<code>System.getProperties()</code>)을 나타내고 다른 하나는 시스템 환경 변수 집합( <code>System.getenv()</code>)을 나타냅니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
이러한 기본 속성 소스는 독립 실행형 애플리케이션에서 사용하기 위해 <code>StandardEnvironment</code>에 제공됩니다. <code>StandardServletEnvironment</code>는 서블릿 구성, 서블릿 컨텍스트 매개변수 및 JNDI를 사용할 수 있는 경우 <code>JndiPropertySource</code>를 포함한 추가 기본 속성(property) 소스로 채워집니다.</p>
</blockquote>
<p>구체적으로(Concretely) <code>StandardEnvironment</code>를 사용할 때 런타임에 <code>my-property</code> 시스템 속성(property)이나 <code>my-property</code> 환경 변수가 있는 경우 <code>env.containsProperty("my-property")</code>에 대한 호출은 true를 반환합니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
수행되는 검색은 계층적입니다. 기본적으로, 시스템 속성(property)은 환경 변수보다 우선합니다. 따라서 <code>env.getProperty("my-property")</code>를 호출하는 동안 <code>my-property</code> 속성(property)이 두 위치 모두에 설정되면, 시스템 속성 값이 "승리"하여 반환됩니다. 속성 값은 병합되지 않고 오히려 이전 항목에 의해 완전히 재정의됩니다.</p>
<p>공통 <code>StandardServletEnvironment</code>의 경우 전체 계층 구조는 다음과 같으며 우선 순위가 가장 높은 항목이 맨 위에 표시됩니다.</p>
<ol>
<li>
<p><code>ServletConfig</code> 매개변수(해당되는 경우 — 예를 들어 <code>DispatcherServlet</code> 컨텍스트의 경우)</p>
</li>
<li>
<p><code>ServletContext</code> 매개변수(<code>web.xml</code> <code>context-param</code> 항목)</p>
</li>
<li>
<p>JNDI 환경 변수(<code>java:comp/env/</code> 항목)</p>
</li>
<li>
<p>JVM 시스템 특성(<code>-D</code> 명령줄 인수)</p>
</li>
<li>
<p>JVM 시스템 환경(운영 체제 환경 변수)</p>
</li>
</ol>
</blockquote>
<p>가장 중요한 점은 전체 메커니즘을 구성(configurable)할 수 있다는 것입니다. 아마도 이 검색에 통합하려는 사용자 정의 속성 소스가 있을 수 있습니다. 그렇게 하려면 자신만의 <code>PropertySource</code>를 구현(implement) 및 인스턴스화하고 이를 현재 <code>Environment</code>의 <code>PropertySource</code> 세트에 추가하세요. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token class-name">ConfigurableApplicationContext</span> ctx <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GenericApplicationContext</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">MutablePropertySources</span> sources <span class="token operator">=</span> ctx<span class="token punctuation">.</span><span class="token function">getEnvironment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getPropertySources</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
sources<span class="token punctuation">.</span><span class="token function">addFirst</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyPropertySource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>앞의 코드에서는 <code>MyPropertySource</code>가 검색에서 가장 높은 우선 순위로 추가되었습니다. <code>my-property</code> 속성(property)이 포함된 경우, 다른 <code>PropertySource</code>의 <code>my-property</code> 속성(property)을 위해 해당 속성(property)이 감지되고 반환됩니다. <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/core/env/MutablePropertySources.html"><code>MutablePropertySources</code></a> API는 속성 소스 세트를 정확하게 조작할 수 있는 다양한 메서드를 노출합니다.</p>
<h1 id="using-propertysource">Using <code>@PropertySource</code></h1>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/annotation/PropertySource.html">@PropertySource</a> 어노테이션은 Spring <code>Environment</code>에 <code>PropertySource</code>를 추가하기 위한 편리하고 선언적인 메커니즘을 제공합니다.</p>
<p>키-값 쌍 <code>testbean.name=myTestBean</code>을 포함하는 <code>app.properties</code>라는 파일이 있는 경우 다음 <code>@Configuration</code> 클래스는 <code>testBean.getName()</code> 호출이 <code>myTestBean</code>을 반환하는 방식으로 <code>@PropertySource</code>를 사용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@PropertySource</span><span class="token punctuation">(</span><span class="token string">"classpath:/com/myco/app.properties"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

 <span class="token annotation punctuation">@Autowired</span>
 <span class="token class-name">Environment</span> env<span class="token punctuation">;</span>

 <span class="token annotation punctuation">@Bean</span>
 <span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">testBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token class-name">TestBean</span> testBean <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  testBean<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>env<span class="token punctuation">.</span><span class="token function">getProperty</span><span class="token punctuation">(</span><span class="token string">"testbean.name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token keyword">return</span> testBean<span class="token punctuation">;</span>
 <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@PropertySource</code> 리소스 위치에 있는 모든 <code>${…​}</code> 자리 표시자는 다음 예제와 같이 환경에 대해 이미 등록된 속성 소스 집합에 대해 resolve됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@PropertySource</span><span class="token punctuation">(</span><span class="token string">"classpath:/com/${my.placeholder:default/path}/app.properties"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>

 <span class="token annotation punctuation">@Autowired</span>
 <span class="token class-name">Environment</span> env<span class="token punctuation">;</span>

 <span class="token annotation punctuation">@Bean</span>
 <span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">testBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token class-name">TestBean</span> testBean <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  testBean<span class="token punctuation">.</span><span class="token function">setName</span><span class="token punctuation">(</span>env<span class="token punctuation">.</span><span class="token function">getProperty</span><span class="token punctuation">(</span><span class="token string">"testbean.name"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token keyword">return</span> testBean<span class="token punctuation">;</span>
 <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>my.placeholder</code>가 이미 등록된 속성 소스(예: 시스템 속성 또는 환경 변수) 중 하나에 있다고 가정하면 자리 표시자는 해당 값으로 resolve됩니다. 그렇지 않은 경우 <code>default/path</code>가 기본값으로 사용됩니다. 기본값이 지정되지 않고 속성(property)을 resolve할 수 없는 경우 <code>IllegalArgumentException</code>이 발생합니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@PropertySource</code>는 반복 가능한 어노테이션으로 사용될 수 있습니다. <code>@PropertySource</code>는 속성 재정의로 사용자 정의 구성(composed)된 어노테이션을 생성하기 위한 메타 어노테이션으로 사용될 수도 있습니다.</p>
</blockquote>
<h1 id="placeholder-resolution-in-statements">Placeholder Resolution in Statements</h1>
<p>역사적으로 요소의 자리 표시자 값은 JVM 시스템 속성이나 환경 변수에 대해서만 resolve될 수 있었습니다. 더 이상 그렇지 않습니다. <code>Environment</code> 추상화는 컨테이너 전체에 통합되어 있으므로, 이를 통해 자리 표시자의 resolution 경로를 쉽게 지정할 수 있습니다. 즉, 원하는 방식으로 resolution 프로세스를 구성(configure)할 수 있습니다. 시스템 속성(property) 및 환경 변수를 통해 검색 우선 순위를 변경하거나 완전히 제거할 수 있습니다. 필요에 따라 자신만의 속성(property) 소스를 믹스에 추가할 수도 있습니다.</p>
<p>구체적으로(Concretely) 다음 명령문은 <code>customer</code> 속성(property)이 <code>Environment</code>에서 사용 가능한 한, 정의된 위치에 관계없이 작동합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>import</span> <span class="token attr-name">resource</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com/bank/service/${customer}-config.xml<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>