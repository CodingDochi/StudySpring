<p>Spring은 또한 필드 또는 Bean 속성(property) setter 메소드에서 JSR-250 <code>@Resource</code> 어노테이션(<code>jakarta.annotation.Resource</code>)을 사용하여 주입을 지원합니다. 이는 Jakarta EE(예: JSF 관리 Bean 및 JAX-WS 엔드포인트)의 일반적인 패턴입니다. Spring은 Spring 관리 객체에 대해서도 이 패턴을 지원합니다.</p>
<p><code>@Resource</code>는 이름(name) 속성(attribute)을 사용합니다. 기본적으로 Spring은 해당 값을 주입할 Bean 이름으로 해석합니다. 즉, 다음 예에 표시된 대로 by-name 의미(semantics) 체계를 따릅니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Resource</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token string">"myMovieFinder"</span><span class="token punctuation">)</span> <span class="token comment">// (1)</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) 이 줄에서 <code>@Resource</code>를 주입합니다.</p>
<p>이름을 명시적으로 지정하지 않으면, 기본(default) 이름은 필드 이름이나 setter 메서드에서 파생됩니다. 필드의 경우 필드 이름을 사용합니다. setter 메소드의 경우 Bean 속성(property) 이름을 사용합니다. 다음 예제에서는 setter 메소드에 <code>movieFinder</code>라는 빈을 삽입합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Resource</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMovieFinder</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
어노테이션과 함께 제공된 이름은 <code>CommonAnnotationBeanPostProcessor</code>가 인식(aware)하는 <code>ApplicationContext</code>에 의해 Bean 이름으로 해석됩니다. Spring의 <a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/jndi/support/SimpleJndiBeanFactory.html"><code>SimpleJndiBeanFactory</code></a>를 명시적으로 구성하면 JNDI를 통해 이름을 확인할 수 있습니다. 그러나 기본 동작을 사용하고 Spring의 JNDI 조회 기능을 사용하여 간접 수준을 유지하는 것이 좋습니다.</p>
</blockquote>
<hr>
<p><strong>JNDI와 Classpath</strong></p>
<p>JNDI(Java Naming and Directory Interface): 자바 어플리케이션이 서버 자원에 접근하기 위한 API입니다. 주로 데이터베이스 연결, 메시지 큐, 이메일 서버 등의 외부 리소스에 접근할 때 사용됩니다.</p>
<p>클래스패스(Classpath): 자바 프로그램이 실행될 때 JVM(Java Virtual Machine)이 클래스 파일을 찾는 경로를 지정하는데 사용됩니다. 클래스패스는 클래스 파일이 위치한 디렉토리나 JAR 파일을 가리킵니다. 클래스패스를 설정함으로써 JVM은 해당 위치에서 클래스를 로드하여 실행할 수 있습니다.</p>
<p><strong>스프링 프레임워크와 JNDI</strong><br>
Spring 프레임워크는 JNDI를 통해 외부 리소스에 접근하는 기능을 제공합니다. Spring은 JNDI를 사용하여 데이터베이스 연결, JMS(Java Message Service) 큐, EJB(Enterprise JavaBeans) 등과 같은 리소스에 접근합니다. Spring은 JNDI를 통해 설정된 리소스를 얻어와서 어플리케이션에서 사용할 수 있도록 지원합니다. 이는 Spring의 IoC(Inversion of Control) 컨테이너가 JNDI를 통해 리소스를 관리하고 필요한 곳에 주입(injection)하여 의존성을 해결하는 데 사용됩니다. 따라서 Spring은 JNDI와 밀접하게 상호작용하며, 외부 리소스를 효율적으로 관리하고 사용할 수 있도록 도와줍니다.</p>
<hr>
<p>명시적인 이름이 지정되지 않은 <code>@Resource</code> 사용의 독점적인(exclusive) 경우에서, <code>@Autowired</code>와 유사하게 <code>@Resource</code>는 특정 명명된 Bean 대신 primary 타입 match를 찾고 잘 알려진 해결(resolve) 가능한 종속성(<code>BeanFactory</code>, <code>ApplicationContext</code>, <code>ResourceLoader</code>, <code>ApplicationEventPublisher</code> 및 <code>MessageSource</code> 인터페이스)를 resolve 합니다.</p>
<p>따라서 다음 예에서 <code>customerPreferenceDao</code> 필드는 먼저 "customerPreferenceDao"라는 Bean을 찾은 다음 <code>CustomerPreferenceDao</code> 타입에 대한 primary 타입 매치로 대체(fall back)됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieRecommender</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Resource</span>
	<span class="token keyword">private</span> <span class="token class-name">CustomerPreferenceDao</span> customerPreferenceDao<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Resource</span>
	<span class="token keyword">private</span> <span class="token class-name">ApplicationContext</span> context<span class="token punctuation">;</span> <span class="token comment">// (1)</span>

	<span class="token keyword">public</span> <span class="token class-name">MovieRecommender</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>context</code> 필드는 알려진 해결 가능한 종속성 유형인 <code>ApplicationContext</code>를 기반으로 주입됩니다.</p>