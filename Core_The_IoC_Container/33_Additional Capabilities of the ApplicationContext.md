<p><a href="https://docs.spring.io/spring-framework/reference/web/webmvc-view/mvc-xslt.html#mvc-view-xslt-beandefs">챕터 소개</a>에서 논의한 것처럼 <code>org.springframework.beans.factory</code> 패키지는 프로그래밍 방식을 포함하여 Bean을 관리하고 조작하기 위한 기본 기능을 제공합니다. <code>org.springframework.context</code> 패키지는 <code>BeanFactory</code> 인터페이스를 확장하는 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/ApplicationContext.html"><code>ApplicationContext</code></a> 인터페이스를 추가하고 다른 인터페이스를 확장하여 애플리케이션 프레임워크 지향 스타일로 추가 기능을 제공합니다. 많은 사람들이 완전히 선언적인 방식으로 <code>ApplicationContext</code>를 사용합니다. 프로그래밍 방식으로 생성하지도 않고 대신 <code>ContextLoader</code>와 같은 지원 클래스를 사용하여 Jakarta EE 웹 애플리케이션의 일반적인 시작 프로세스의 일부로 <code>ApplicationContext</code>를 자동으로 인스턴스화합니다.</p>
<p>보다 프레임워크 지향적인 스타일로 <code>BeanFactory</code> 기능을 향상시키기 위해 컨텍스트 패키지는 다음 기능도 제공합니다.</p>
<ul>
<li>
<p><code>MessageSource</code> 인터페이스를 통해 i18n 스타일의 메시지에 액세스합니다.</p>
</li>
<li>
<p><code>ResourceLoader</code> 인터페이스를 통해 URL, 파일 등의 리소스에 액세스합니다.</p>
</li>
<li>
<p><code>ApplicationEventPublisher</code> 인터페이스를 사용하여 <code>ApplicationListener</code> 인터페이스를 구현하는 <code>Bean</code>에 이벤트를 게시(publication)합니다.</p>
</li>
<li>
<p><code>HierarchicalBeanFactory</code> 인터페이스를 통해 애플리케이션의 웹 계층과 같은 하나의 특정 계층에 각각 집중할 수 있도록 여러(계층적) 컨텍스트를 로드합니다.</p>
</li>
</ul>
<h1 id="internationalization-using-messagesource">Internationalization using <code>MessageSource</code></h1>
<p><code>ApplicationContext</code> 인터페이스는 <code>MessageSource</code>라는 인터페이스를 확장하므로 국제화(internalization)("i18n") 기능을 제공합니다. Spring은 또한 계층적으로 메시지를 resolve할 수 있는 <code>HierarchicalMessageSource</code> 인터페이스를 제공합니다. 이러한 인터페이스는 함께 Spring이 메시지 resolution에 영향을 미치는 기반을 제공합니다. 이러한 인터페이스에 정의된 메서드는 다음과 같습니다.</p>
<ul>
<li>
<p><code>String getMessage(String code, Object[] args, String default, Locale loc)</code>: <code>MessageSource</code>에서 메시지를 검색하는 데 사용되는 기본 메서드입니다. 지정된 로케일(locale)에 대한 메시지가 없으면 기본 메시지가 사용됩니다. 전달된 모든 인수는 표준 라이브러리에서 제공하는 <code>MessageFormat</code> 기능을 사용하여 대체 값이 됩니다.</p>
</li>
<li>
<p><code>String getMessage(String code, Object[] args, Locale loc)</code>: 기본적으로 이전 방법과 동일하지만 한 가지 차이점이 있습니다. 기본 메시지를 지정할 수 없습니다. 메시지를 찾을 수 없으면 <code>NoSuchMessageException</code>이 발생합니다.</p>
</li>
<li>
<p><code>String getMessage(MessageSourceResolvable resolvable, Locale locale)</code>: 이전 메서드에 사용된 모든 속성은 이 메서드와 함께 사용할 수 있는 <code>MessageSourceResolvable</code>이라는 클래스에도 래핑됩니다.</p>
</li>
</ul>
<p><code>ApplicationContext</code>가 로드되면 컨텍스트에 정의된 <code>MessageSource</code> 빈을 자동으로 검색합니다. Bean의 이름은 <code>messageSource</code>여야 합니다. 해당 Bean이 발견되면 이전 메소드에 대한 모든 호출이 메시지 소스에 위임됩니다. 메시지 소스가 발견되지 않으면 <code>ApplicationContext</code>는 동일한 이름의 Bean을 포함하는 상위 항목을 찾으려고 시도합니다. 그런 항목을 찾는다면 해당 Bean을 <code>MessageSource</code>로 사용합니다. <code>ApplicationContext</code>가 메시지 소스를 찾을 수 없는 경우 위에 정의된 메서드에 대한 호출을 수락할 수 있도록 빈 <code>DelegatingMessageSource</code>가 인스턴스화됩니다.</p>
<p>Spring은 <code>ResourceBundleMessageSource</code>, <code>ReloadableResourceBundleMessageSource</code> 및 <code>StaticMessageSource</code>의 세 가지 <code>MessageSource</code> 구현을 제공합니다. 이들 모두는 중첩된 메시징을 수행하기 위해 <code>HierarchicalMessageSource</code>를 구현합니다. <code>StaticMessageSource</code>는 거의 사용되지 않지만 소스에 메시지를 추가하는 프로그래밍 방식을 제공합니다. 다음 예에서는 <code>ResourceBundleMessageSource</code>를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>messageSource<span class="token punctuation">"</span></span>
			<span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.support.ResourceBundleMessageSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>basenames<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>list</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>format<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>exceptions<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
				<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>windows<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>list</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 예제에서는 클래스패스에 정의된 <code>format</code>, <code>exceptions</code> 및 <code>windows</code>이라는 세 가지 리소스 번들이 있다고 가정합니다. 메시지 resolving을 위한 모든 요청은 <code>ResourceBundle</code> 객체를 통해 메시지를 resolve하는 JDK 표준 방식으로 처리됩니다. 예제의 목적을 위해 위 리소스 번들 파일 중 두 개의 내용이 다음과 같다고 가정합니다.</p>
<pre><code class="language-null"># in format.properties
message=Alligators rock!</code></pre>
<pre><code class="language-null"># in exceptions.properties
argument.required=The {0} argument is required.</code></pre>
<p>다음 예에서는 <code>MessageSource</code> 기능을 실행하는 프로그램을 보여줍니다. 모든 <code>ApplicationContext</code> 구현은 <code>MessageSource</code> 구현이기도 하므로 <code>MessageSource</code> 인터페이스로 캐스팅될 수 있다는 점을 기억하세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">MessageSource</span> resources <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">String</span> message <span class="token operator">=</span> resources<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token string">"message"</span><span class="token punctuation">,</span> <span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token string">"Default"</span><span class="token punctuation">,</span> <span class="token class-name">Locale</span><span class="token punctuation">.</span>ENGLISH<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>위 프로그램의 결과 출력은 다음과 같습니다.</p>
<pre><code class="language-null">Alligators rock!</code></pre>
<p>요약하자면, <code>MessageSource</code>는 클래스패스의 루트에 있는 <code>beans.xml</code>이라는 파일에 정의되어 있습니다. <code>messageSource</code> 빈 정의는 <code>basenames</code> 속성(property)을 통해 여러 리소스 번들을 참조합니다. 목록에서 <code>basenames</code> 속성(property)에 전달되는 세 개의 파일은 classpath의 루트에 파일로 존재하며 각각 <code>format.properties,Exceptions.properties</code> 및 <code>windows.properties</code>라고 합니다.</p>
<p>다음 예에서는 메시지 조회에 전달된 인수를 보여줍니다. 이러한 인수는 <code>String</code> 개체로 변환되어 조회 메시지의 자리 표시자에 삽입됩니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- this MessageSource is being used in a web application --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>messageSource<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.support.ResourceBundleMessageSource<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>basename<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>exceptions<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

	<span class="token comment">&lt;!-- lets inject the above MessageSource into this POJO --&gt;</span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.something.Example<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>messages<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>messageSource<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Example</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MessageSource</span> messages<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setMessages</span><span class="token punctuation">(</span><span class="token class-name">MessageSource</span> messages<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>messages <span class="token operator">=</span> messages<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">execute</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">String</span> message <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span>messages<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token string">"argument.required"</span><span class="token punctuation">,</span>
			<span class="token keyword">new</span> <span class="token class-name">Object</span> <span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span><span class="token string">"userDao"</span><span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token string">"Required"</span><span class="token punctuation">,</span> <span class="token class-name">Locale</span><span class="token punctuation">.</span>ENGLISH<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>execute()</code> 메소드 호출의 결과 출력은 다음과 같습니다.</p>
<pre><code class="language-null">The userDao argument is required.</code></pre>
<p>국제화("i18n")와 관련하여 Spring의 다양한 <code>MessageSource</code> 구현은 표준 JDK <code>ResourceBundle</code>과 동일한 로케일 resolution 및 대체(fallback) 규칙을 따릅니다. 간단히 말해서, 이전에 정의한 <code>messageSource</code> 예제를 계속 사용하여 영국(<code>en-GB</code>) 로케일에 대해 메시지를 확인하려면 각각 <code>format_en_GB.properties</code>,<code>exceptions_en_GB.properties</code> 및 <code>windows_en_GB.properties</code>라는 파일을 생성합니다.</p>
<p>일반적으로 로케일 resolution은 애플리케이션의 주변 환경에 의해 관리됩니다. 다음 예에서는 (영국) 메시지가 resolve되는 로캘을 수동으로 지정합니다.</p>
<pre><code class="language-null"># in exceptions_en_GB.properties
argument.required=Ebagum lad, the ''{0}'' argument is required, I say, required.</code></pre>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">MessageSource</span> resources <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ClassPathXmlApplicationContext</span><span class="token punctuation">(</span><span class="token string">"beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">String</span> message <span class="token operator">=</span> resources<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token string">"argument.required"</span><span class="token punctuation">,</span>
		<span class="token keyword">new</span> <span class="token class-name">Object</span> <span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">{</span><span class="token string">"userDao"</span><span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token string">"Required"</span><span class="token punctuation">,</span> <span class="token class-name">Locale</span><span class="token punctuation">.</span>UK<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token class-name">System</span><span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>위 프로그램을 실행한 결과 결과는 다음과 같습니다.</p>
<pre><code class="language-null">Ebagum lad, the 'userDao' argument is required, I say, required.</code></pre>
<p>또한 <code>MessageSourceAware</code> 인터페이스를 사용하여 정의된 <code>MessageSource</code>에 대한 참조(reference)를 얻을 수도 있습니다. <code>MessageSourceAware</code> 인터페이스를 구현하는 <code>ApplicationContext</code>에 정의된 모든 Bean은 Bean이 생성 및 구성(configure)될 때 애플리케이션 컨텍스트의 <code>MessageSource</code>와 함께 주입됩니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring의 <code>MessageSource</code>는 Java의 <code>ResourceBundle</code>을 기반으로 하기 때문에 동일한 기본 이름을 가진 번들을 병합하지 않고, 발견된 첫 번째 번들만 사용합니다. 동일한 기본 이름을 가진 후속 메시지 번들은 무시됩니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
<code>ResourceBundleMessageSource</code>의 대안으로 Spring은 <code>ReloadableResourceBundleMessageSource</code> 클래스를 제공합니다. 이 변형은 동일한 번들 파일 형식을 지원하지만 표준 JDK 기반 <code>ResourceBundleMessageSource</code> 구현보다 더 유연합니다. 특히, 모든 Spring 리소스 위치(classpath뿐만 아니라)에서 파일을 읽을 수 있고 번들 속성(property) 파일의 핫 리로드를 지원합니다(그 사이에 파일을 효율적으로 캐싱함). 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/support/ReloadableResourceBundleMessageSource.html"><code>ReloadableResourceBundleMessageSource</code></a> javadoc을 참조하세요.</p>
</blockquote>
<hr>
<p>"Hot reloading"은 소프트웨어 개발에서 사용되는 용어로, 애플리케이션이 실행 중일 때 변경된 내용을 반영하고 다시 빌드하지 않고도 즉시 적용할 수 있는 기능을 가리킵니다. 이는 특히 개발자가 코드를 수정하고 테스트할 때 유용합니다.</p>
<p>Spring의 <code>ReloadableResourceBundleMessageSource</code> 클래스에서 "hot reloading"이란 번들 속성 파일이 수정되면 애플리케이션이 자동으로 이를 감지하고 다시 불러와 적용하는 기능을 의미합니다. 이는 번들 파일을 다시 빌드하지 않고도 변경된 내용을 즉시 반영할 수 있게 해줍니다.</p>
<hr>
<h1 id="standard-and-custom-events">Standard and Custom Events</h1>
<p><code>ApplicationContext</code>의 이벤트 처리는 <code>ApplicationEvent</code> 클래스와 <code>ApplicationListener</code> 인터페이스를 통해 제공됩니다. <code>ApplicationListener</code> 인터페이스를 구현하는 Bean이 컨텍스트에 배포되면 <code>ApplicationEvent</code>가 <code>ApplicationContext</code>에 게시(publish)될 때마다 해당 Bean에 알림이 전달됩니다. 본질적으로 이것은 표준 Observer 디자인 패턴입니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
Spring 4.2부터 이벤트 인프라가 크게 개선되어 <a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-functionality-events-annotation">어노테이션 기반 모델</a>은 물론 임의의 이벤트(즉, <code>ApplicationEvent</code>에서 반드시 확장되지는 않는 객체)를 게시하는 기능도 제공합니다. 이러한 객체가 게시(publish)되면 이를 이벤트로 래핑(wrap)합니다.</p>
</blockquote>
<hr>
<p><strong>publish &amp; wrap</strong></p>
<p>"publish"는 Spring의 이벤트 시스템을 통해 이벤트를 발행하는 것을 의미합니다. Spring 애플리케이션 내에서 발생하는 이벤트는 다양한 컴포넌트 간의 통신에 사용될 수 있습니다. 예를 들어, 어떤 상황에서 데이터베이스에 새로운 항목이 추가되었을 때, 해당 이벤트를 발생시켜 이를 처리하거나 다른 부분에 알리는 등의 작업을 수행할 수 있습니다.</p>
<p>"Wrap"은 주어진 객체를 다른 형태로 감싸거나 변형하는 것을 의미합니다. 여기서는 이벤트로 발행될 객체가 ApplicationEvent 클래스를 확장하지 않아도 되며, 임의의 객체라도 이벤트로 발행할 수 있다는 것을 의미합니다. 따라서 Spring은 이러한 객체를 이벤트로 발행하기 위해 필요한 래핑(감싸기) 작업을 자동으로 처리합니다. </p>
<hr>
<p>다음 표에는 Spring이 제공하는 표준 이벤트가 설명되어 있습니다.</p>
<table id="beans-ctx-events-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. 내장 이벤트</caption>




<thead>
<tr>
<th class="tableblock halign-left valign-top">이벤트</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ContextRefreshedEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContext</code>가 초기화되거나 새로 고쳐질 때 발행됩니다(예: <code>ConfigurableApplicationContext</code> 인터페이스의 <code>refresh()</code> 메서드 사용). 여기서 "초기화"란 모든 빈이 로드되고, 후처리기 빈이 감지되어 활성화되고, 싱글톤이 미리 인스턴스화되고, <code>ApplicationContext</code> 객체가 사용할 준비가 된 상태를 의미합니다. 컨텍스트가 닫히지 않은 한 여러 번 리프레시를 트리거할 수 있습니다. 선택한 <code>ApplicationContext</code>가 이러한 "핫" 리프레시를 지원하는지 여부에 따라 다릅니다. 예를 들어, <code>XmlWebApplicationContext</code>은 핫 리프레시를 지원하지만 <code>GenericApplicationContext</code>은 지원하지 않습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ContextStartedEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContext</code>가 <code>ConfigurableApplicationContext</code> 인터페이스의 <code>start()</code> 메서드를 사용하여 시작될 때 발생됩니다. 여기서 "시작"이란 모든 <code>Lifecycle</code> 빈이 명시적인 시작 신호를 받는 것을 의미합니다. 일반적으로 이 신호는 명시적인 중지 후 빈을 다시 시작하는 데 사용되지만, 자동 시작이 구성되지 않은 구성 요소(예: 초기화 시 이미 시작되지 않은 구성 요소)를 시작하는 데도 사용될 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ContextStoppedEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContext</code>가 <code>ConfigurableApplicationContext</code> 인터페이스의 <code>stop()</code> 메서드를 사용하여 중지될 때 발생됩니다. 여기서 "중지"란 모든 <code>Lifecycle</code> 빈이 명시적인 중지 신호를 받는 것을 의미합니다. 중지된 컨텍스트는 <code>start()</code> 호출을 통해 다시 시작될 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ContextClosedEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ApplicationContext</code>가 <code>ConfigurableApplicationContext</code> 인터페이스의 <code>close()</code> 메서드 또는 JVM 종료 후크를 통해 닫힐 때 발생됩니다. 여기서 "닫힘"이란 모든 싱글톤 빈이 소멸될 것이라는 것을 의미합니다. 컨텍스트가 닫히면 생명주기가 끝나고 리프레시하거나 다시 시작할 수 없습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>RequestHandledEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">HTTP 요청이 서비스되었음을 모든 빈에게 알리는 웹 한정 이벤트입니다. 이 이벤트는 요청이 완료된 후에 발행됩니다. 이 이벤트는 Spring의 <code>DispatcherServlet</code>을 사용하는 웹 애플리케이션에만 적용됩니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>ServletRequestHandledEvent</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>RequestHandledEvent</code>의 하위 클래스로서 서블릿별 컨텍스트 정보를 추가합니다.</p></td>
</tr>
</tbody>
</table>
<p>자신만의 맞춤 이벤트를 만들고 게시(publish)할 수도 있습니다. 다음 예제는 Spring의 <code>ApplicationEvent</code> 기본 클래스를 확장하는 간단한 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BlockedListEvent</span> <span class="token keyword">extends</span> <span class="token class-name">ApplicationEvent</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> address<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token keyword">final</span> <span class="token class-name">String</span> content<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">BlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">Object</span> source<span class="token punctuation">,</span> <span class="token class-name">String</span> address<span class="token punctuation">,</span> <span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>source<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>address <span class="token operator">=</span> address<span class="token punctuation">;</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>content <span class="token operator">=</span> content<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// accessor and other methods...</span>
<span class="token punctuation">}</span></code></pre>
<p>사용자 정의 <code>ApplicationEvent</code>를 게시하려면 <code>ApplicationEventPublisher</code>에서 <code>publishEvent()</code> 메서드를 호출합니다. 일반적으로 이는 <code>ApplicationEventPublisherAware</code>를 구현하는 클래스를 생성하고 이를 Spring Bean으로 등록하여 수행됩니다. 다음 예제에서는 이러한 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EmailService</span> <span class="token keyword">implements</span> <span class="token class-name">ApplicationEventPublisherAware</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> blockedList<span class="token punctuation">;</span>
	<span class="token keyword">private</span> <span class="token class-name">ApplicationEventPublisher</span> publisher<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setBlockedList</span><span class="token punctuation">(</span><span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> blockedList<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>blockedList <span class="token operator">=</span> blockedList<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setApplicationEventPublisher</span><span class="token punctuation">(</span><span class="token class-name">ApplicationEventPublisher</span> publisher<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>publisher <span class="token operator">=</span> publisher<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">sendEmail</span><span class="token punctuation">(</span><span class="token class-name">String</span> address<span class="token punctuation">,</span> <span class="token class-name">String</span> content<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span>blockedList<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>address<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			publisher<span class="token punctuation">.</span><span class="token function">publishEvent</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">BlockedListEvent</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">,</span> address<span class="token punctuation">,</span> content<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
			<span class="token keyword">return</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>
		<span class="token comment">// send email...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>구성(configuration) 시 Spring 컨테이너는 <code>EmailService</code>가 <code>ApplicationEventPublisherAware</code>를 구현하고 자동으로 <code>setApplicationEventPublisher()</code>를 호출하는 것을 감지합니다. 실제로 전달된 매개변수는 Spring 컨테이너 자체입니다. <code>ApplicationEventPublisher</code> 인터페이스를 통해 애플리케이션 컨텍스트와 상호작용하고 있습니다.</p>
<p>사용자 정의 <code>ApplicationEvent</code>를 수신하려면 <code>ApplicationListener</code>를 구현하는 클래스를 생성하고 이를 Spring 빈으로 등록할 수 있습니다. 다음 예제에서는 이러한 클래스를 보여줍니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BlockedListNotifier</span> <span class="token keyword">implements</span> <span class="token class-name">ApplicationListener</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">BlockedListEvent</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> notificationAddress<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setNotificationAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> notificationAddress<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>notificationAddress <span class="token operator">=</span> notificationAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onApplicationEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// notify appropriate parties via notificationAddress...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>ApplicationListener</code>는 사용자 정의 이벤트 타입(이전 예제의 <code>BlockedListEvent</code>)으로 제네릭하게 매개변수화(generically parameterized)됩니다. 이는 <code>onApplicationEvent()</code> 메서드가 type-safe을 유지하여 다운캐스트가 필요하지 않음을 의미합니다. 이벤트 리스너는 원하는 만큼 등록할 수 있지만 기본적으로 이벤트 리스너는 이벤트를 동기식으로 수신한다는 것을 주의하십시오. 이는 모든 리스너가 이벤트 처리를 완료할 때까지 <code>publishEvent()</code> 메서드가 차단(block)됨을 의미합니다. 이 동기식(synchronous) 및 단일 스레드(single-threaded) 접근 방식의 한 가지 장점은 수신기(receiver)가 이벤트를 수신할 때, 트랜잭션 컨텍스트를 사용할 수 있는 경우, 게시자(publisher)의 트랜잭션 컨텍스트 내에서 작동한다는 것입니다. 이벤트 게시를 위한 다른 전략이 필요한 경우(예: 기본적으로 비동기 이벤트 처리에 대해서는 Spring의 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/event/ApplicationEventMulticaster.html"><code>ApplicationEventMulticaster</code></a> 인터페이스에 대한 javadoc과, 사용자 정의 "applicationEventMulticaster" 빈 정의에 적용할 수 있는 구성 옵션에 대한 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/event/SimpleApplicationEventMulticaster.html"><code>SimpleApplicationEventMulticaster</code></a> 구현을 참조하세요. 이러한 경우 이벤트 처리를 위해 ThreadLocal 및 로깅 컨텍스트가 전파되지 않습니다. Observability 문제에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/integration/observability.html#observability.application-events"><code>@EventListener</code> Observability 섹션</a>을 참조하세요.</p>
<p>다음 예제는 위의 각 클래스를 등록하고 구성하는 데 사용되는 Bean 정의를 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>emailService<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.EmailService<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blockedList<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>list</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>known.spammer@example.org<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>known.hacker@example.org<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
			<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>john.doe@example.org<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>list</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blockedListNotifier<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>example.BlockedListNotifier<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>notificationAddress<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>blockedlist@example.org<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span>

   <span class="token comment">&lt;!-- optional: a custom ApplicationEventMulticaster definition --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>bean</span> <span class="token attr-name">id</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>applicationEventMulticaster<span class="token punctuation">"</span></span> <span class="token attr-name">class</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.context.event.SimpleApplicationEventMulticaster<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>taskExecutor<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>errorHandler<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>...<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>bean</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>종합하면 <code>emailService</code> 빈의 <code>sendEmail()</code>메소드가 호출될 때 차단해야 하는 이메일 메시지가 있는 경우 <code>BlockedListEvent</code> 유형의 사용자 정의 이벤트가 게시됩니다. <code>blockedListNotifier</code> 빈은 <code>ApplicationListener</code>로 등록되고 <code>BlockedListEvent</code>를 수신하며, 이때 적절한 당사자에게 알릴 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
Spring의 이벤트 메커니즘은 동일한 애플리케이션 컨텍스트 내에서 Spring 빈 간의 간단한 통신을 위해 설계되었습니다. 그러나 보다 정교한 엔터프라이즈 통합 요구 사항을 위해 별도로 유지 관리되는 <a href="https://spring.io/projects/spring-integration/">Spring Integration</a> 프로젝트는 잘 알려진 Spring 프로그래밍 모델을 기반으로 하는 경량의 <a href="https://www.enterpriseintegrationpatterns.com/">패턴 지향</a> 이벤트 중심 아키텍처 구축을 위한 완벽한 지원을 제공합니다.</p>
</blockquote>
<h2 id="annotation-based-event-listeners">Annotation-based Event Listeners</h2>
<p><code>@EventListener</code> 어노테이션을 사용하여 관리되는 Bean의 모든 메서드에 이벤트 리스너를 등록할 수 있습니다. <code>BlockedListNotifier</code>는 다음과 같이 다시 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">BlockedListNotifier</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">String</span> notificationAddress<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setNotificationAddress</span><span class="token punctuation">(</span><span class="token class-name">String</span> notificationAddress<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>notificationAddress <span class="token operator">=</span> notificationAddress<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@EventListener</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">processBlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// notify appropriate parties via notificationAddress...</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>메서드 시그니처는 수신하는 이벤트 유형을 다시 한 번 선언하지만, 이번에는 유연한 이름을 사용하고 특정 리스너 인터페이스를 구현하지 않습니다. 실제 이벤트 타입이 구현 계층에서 제네릭 매개변수를 resolve하는 한, 제네릭을 통해 이벤트 타입의 범위를 좁힐 수도 있습니다.</p>
<p>메소드가 여러 이벤트를 수신해야 하거나 매개변수 없이 정의하려는 경우 이벤트 타입을 어노테이션 자체에 지정할 수도 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EventListener</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ContextStartedEvent</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token class-name">ContextRefreshedEvent</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">handleContextStart</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>특정 이벤트에 대한 메소드를 실제로 호출하기 위해 일치해야 하는 SpEL 표현식을 정의하는 어노테이션의 <code>condition</code> 속성(attribute)을 사용하여 추가적인 런타임 필터링을 추가하는 것도 가능합니다.</p>
<p>다음 예에서는 이벤트의 <code>content</code> 속성(attribute)이 <code>my-event</code>와 동일한 경우에만 호출되도록 notifier를 다시 작성할 수 있는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EventListener</span><span class="token punctuation">(</span>condition <span class="token operator">=</span> <span class="token string">"#blEvent.content == 'my-event'"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">processBlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> blEvent<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// notify appropriate parties via notificationAddress...</span>
<span class="token punctuation">}</span></code></pre>
<p>각 <code>SpEL</code> 표현식은 전용 컨텍스트에 대해 평가(evaluate)됩니다. 다음 표에는 조건부 이벤트 처리에 사용할 수 있도록 컨텍스트에 제공되는 항목이 나열되어 있습니다.</p>
<table id="context-functionality-events-annotation-tbl" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 2. 이벤트 SpEL에서 사용 가능한 메타데이터</caption>






<thead>
<tr>
<th class="tableblock halign-left valign-top">이름</th>
<th class="tableblock halign-left valign-top">위치</th>
<th class="tableblock halign-left valign-top">설명</th>
<th class="tableblock halign-left valign-top">예제</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">이벤트</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">루트 객체</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">실제 <code>ApplicationEvent</code>입니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>#root.event</code> 또는 <code>event</code></p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">인수 배열</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">루트 객체</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">메서드를 호출하는 데 사용된 인수(객체 배열)입니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>#root.args</code> 또는 <code>args</code>; <code>args[0]</code>를 통해 첫 번째 인수에 액세스할 수 있습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><em>인수 이름</em></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">평가 컨텍스트</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">메서드 인수 중 하나의 이름입니다. 일부 이유로 인해 이름을 사용할 수 없는 경우(예: 컴파일된 바이트 코드에 디버그 정보가 없는 경우) 개별 인수는 <code>#a&lt;#arg&gt;</code> 구문을 사용하여 사용할 수 있습니다. 여기서 <code>&lt;#arg&gt;</code>는 인수 인덱스(0부터 시작)를 나타냅니다.</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>#blEvent</code> 또는 <code>#a0</code> (별칭으로 <code>#p0</code> 또는 <code>#p&lt;#arg&gt;</code> 매개변수 표기법을 사용할 수 있습니다)</p></td>
</tr>
</tbody>
</table>
<p><code>#root.event</code>는 메소드 시그니처가 실제로 게시된 임의의 객체를 참조(refer to)하는 경우에도, 기본(underlying) 이벤트에 대한 액세스를 제공합니다.</p>
<p>다른 이벤트를 처리한 결과로 이벤트를 게시해야 하는 경우 다음 예제와 같이 게시되어야 하는 이벤트를 반환하도록 메서드 시그니처를 변경할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EventListener</span>
<span class="token keyword">public</span> <span class="token class-name">ListUpdateEvent</span> <span class="token function">handleBlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// notify appropriate parties via notificationAddress and</span>
	<span class="token comment">// then publish a ListUpdateEvent...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<a href="https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-functionality-events-async">비동기(asynchronous) 리스너</a>에는 이 기능이 지원되지 않습니다.</p>
</blockquote>
<p><code>handlerBlockedListEvent()</code> 메서드는 처리하는 모든 <code>BlockedListEvent</code>에 대해 새로운  <code>ListUpdateEvent</code>를 게시합니다. 여러 이벤트를 게시해야 하는 경우 <code>Collection</code>이나 이벤트 배열을 대신 반환할 수 있습니다.</p>
<h2 id="asynchronous-listeners">Asynchronous Listeners</h2>
<p>특정 리스너가 이벤트를 비동기적으로 처리하도록 하려면 <a href="https://docs.spring.io/spring-framework/reference/integration/scheduling.html#scheduling-annotation-support-async">일반(regular) @Async 지원</a>을 재사용할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EventListener</span>
<span class="token annotation punctuation">@Async</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">processBlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// BlockedListEvent is processed in a separate thread</span>
<span class="token punctuation">}</span></code></pre>
<p>비동기 이벤트를 사용할 때는 다음 제한 사항에 유의하세요.</p>
<ul>
<li>
<p>비동기 이벤트 리스너가 <code>Exception</code>를 발생시키는 경우 호출자에게 전파되지 않습니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/aop/interceptor/AsyncUncaughtExceptionHandler.html"><code>AsyncUncaughtExceptionHandler</code></a>를 참조하세요.</p>
</li>
<li>
<p>비동기 이벤트 리스너 메서드는 값을 반환하여 후속 이벤트를 게시할 수 없습니다. 처리 결과로 다른 이벤트를 게시해야 하는 경우 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/context/ApplicationEventPublisher.html"><code>ApplicationEventPublisher</code></a>를 주입하여 이벤트를 수동으로 게시하세요.</p>
</li>
<li>
<p>ThreadLocal 및 로깅 컨텍스트는 이벤트 처리를 위해 기본적으로 전파되지 않습니다. Observability에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/integration/observability.html#observability.application-events">@EventListener Observability</a> 섹션을 참조하세요.</p>
</li>
</ul>
<hr>
<p><strong>ThreadLocal과 멀티쓰레드</strong></p>
<p> 멀티스레드는 하나의 프로세스 내에서 실행되며, 일반적으로 서로 정보를 공유합니다. 그러나 스레드 간의 정보 공유는 때로 문제를 발생시킬 수 있습니다. 이는 여러 스레드가 동시에 공유된 자원에 접근하고 수정하려고 할 때 발생할 수 있는 경쟁 상태 등의 문제가 있기 때문입니다.</p>
<p>ThreadLocal은 이러한 문제를 해결하거나 회피하기 위한 메커니즘 중 하나입니다. ThreadLocal을 사용하면 각 스레드에게 독립적인 변수를 제공하여, 각 스레드는 해당 변수를 수정하고 읽을 수 있지만, 다른 스레드에서는 같은 변수에 접근할 수 없습니다. 따라서 스레드 간의 정보 공유를 최소화하고 스레드 간의 독립성을 유지할 수 있습니다.</p>
<p>ThreadLocal 변수는 각 스레드마다 독립적으로 초기화되며, 해당 스레드에서만 유효합니다. 보통 이는 각 스레드에서 유지해야 하는 상태(예: 사용자 인증 정보, 로깅 콘텍스트 등)를 관리하는 데 유용하게 사용됩니다. 예를 들어, 웹 애플리케이션에서는 각 요청을 처리하는 스레드마다 사용자 인증 정보를 유지하는 데 ThreadLocal을 사용할 수 있습니다.</p>
<p>ThreadLocal은 선택적으로 사용됩니다. 즉, 모든 상황에 적합한 해결책은 아닙니다. </p>
<p><strong>Observability와 Visibility</strong></p>
<ul>
<li>
<p>Observability : 시스템 외부에서 시스템 내부 작업 및 상태를 관찰하고 추적할 수 있는 능력. 이를 위해 로그, 메트릭, 이벤트 추적 등 다양한 도구와 기술을 사용할 수 있음.</p>
</li>
<li>
<p>Visibility : 시스템의 상태나 작업이 여러 구성 요소 간에 공유되는 정도를 나타냅니다. 이는 시스템 내의 여러 구성 요소가 상호 작용할 때, 이러한 상호 작용이 발생하고 있는지 여부를 의미합니다. 예를 들어, 다중 스레드 환경에서 변수의 변경이 다른 스레드에 즉시 반영되는지 여부가 이에 해당합니다.</p>
</li>
</ul>
<hr>
<h2 id="ordering-listeners">Ordering Listeners</h2>
<p>하나의 리스너를 다른 리스너보다 먼저 호출해야 하는 경우 다음 예제와 같이 <code>@Order</code> 어노테이션을 메서드 선언에 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@EventListener</span>
<span class="token annotation punctuation">@Order</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">processBlockedListEvent</span><span class="token punctuation">(</span><span class="token class-name">BlockedListEvent</span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// notify appropriate parties via notificationAddress...</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="generic-events">Generic Events</h2>
<p>또한 제네릭을 사용하여 이벤트 구조를 추가로 정의할 수도 있습니다. <code>EntityCreatedEvent&lt;T&gt;</code>를 사용해 보세요. 여기서 <code>T</code>는 생성된 실제 엔터티의 타입입니다. 예를 들어 다음 리스너 정의를 생성하여 <code>Person</code>에 대해 <code>EntityCreatedEvent</code>만 수신할 수 있습니다.</p>
<pre><code class="language-java">  <span class="token annotation punctuation">@EventListener</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onPersonCreated</span><span class="token punctuation">(</span><span class="token class-name">EntityCreatedEvent</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Person</span><span class="token punctuation">&gt;</span></span> event<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>타입 삭제로 인해 이는 발생된 이벤트가 이벤트 리스너가 필터링하는 제네릭 매개변수를 resolve하는 경우에만 작동합니다(즉, <code>class PersonCreatedEvent extends EntityCreatedEvent&lt;Person&gt; { …​ }</code>과 같은 것).</p>
<p>특정 상황에서는 모든 이벤트가 동일한 구조를 따르는 경우(이전 예의 이벤트의 경우처럼) 매우 지루해질 수 있습니다. 이러한 경우 <code>ResolvableTypeProvider</code>를 구현하여 런타임 환경이 제공하는 것 이상으로 프레임워크를 안내할 수 있습니다. 다음 이벤트는 이를 수행하는 방법을 보여줍니다.  </p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EntityCreatedEvent</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token keyword">extends</span> <span class="token class-name">ApplicationEvent</span> <span class="token keyword">implements</span> <span class="token class-name">ResolvableTypeProvider</span> <span class="token punctuation">{</span>

	<span class="token keyword">public</span> <span class="token class-name">EntityCreatedEvent</span><span class="token punctuation">(</span><span class="token class-name">T</span> entity<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">super</span><span class="token punctuation">(</span>entity<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token class-name">ResolvableType</span> <span class="token function">getResolvableType</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token class-name">ResolvableType</span><span class="token punctuation">.</span><span class="token function">forClassWithGenerics</span><span class="token punctuation">(</span><span class="token function">getClass</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token class-name">ResolvableType</span><span class="token punctuation">.</span><span class="token function">forInstance</span><span class="token punctuation">(</span><span class="token function">getSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
이는 <code>ApplicationEvent</code>뿐만 아니라 이벤트로 보내는 임의의 개체에도 적용됩니다.</p>
</blockquote>
<hr>
<p><strong>multicasting</strong></p>
<p>멀티캐스팅(multicasting)은 하나의 발신자가 여러 수신자에게 메시지를 동시에 전달하는 통신 패턴입니다. 이는 네트워크 통신에서도 사용되지만, 여기서는 애플리케이션 이벤트를 다루는 컨텍스트에서의 멀티캐스팅을 설명하고 있습니다.</p>
<p>Spring Framework에서의 멀티캐스팅은 <code>ApplicationEvent</code>를 발행하는 <code>ApplicationEventMulticaster</code>를 통해 이루어집니다. 이 멀티캐스터는 발행된 이벤트를 여러 개의 리스너에게 전달합니다. 따라서 한 이벤트를 여러 리스너가 동시에 처리할 수 있습니다.</p>
<p>이 멀티캐스터는 기본적으로 <code>SimpleApplicationEventMulticaster</code>라는 클래스로 구현되어 있으며, 기본적으로 동기적인 방식으로 이벤트를 처리합니다. 이는 이벤트를 발생시킨 스레드에서 각 리스너가 순차적으로 호출됩니다.</p>
<p>그러나 Spring은 이를 사용자가 필요에 따라 대체하거나 사용자 정의할 수 있도록 허용합니다. 예를 들어, 모든 이벤트를 비동기적으로 처리하거나, 리스너에서 발생한 예외를 처리하는 데 특정 전략을 적용하기 위해 사용자는 <code>applicationEventMulticaster</code> 빈을 정의하고 설정할 수 있습니다. 이렇게 함으로써 멀티캐스터의 동작을 변경하고 커스터마이징할 수 있습니다.</p>
<hr>
<p>마지막으로, 클래식 <code>ApplicationListener</code> 구현과 마찬가지로 실제 멀티캐스팅은 런타임 시 컨텍스트 전체(context-wide)의 <code>ApplicationEventMulticaster</code>를 통해 발생합니다. 기본적으로 이는 caller 스레드에서 동기(synchronous) 이벤트 게시가 포함된 <code>SimpleApplicationEventMulticaster</code>입니다. 이는 "applicationEventMulticaster" 빈 정의를 통해 대체/맞춤화될 수 있습니다. 모든 이벤트를 비동기적으로 처리하거나 리스너 예외를 처리하기 위해:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token class-name">ApplicationEventMulticaster</span> <span class="token function">applicationEventMulticaster</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">SimpleApplicationEventMulticaster</span> multicaster <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">SimpleApplicationEventMulticaster</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	multicaster<span class="token punctuation">.</span><span class="token function">setTaskExecutor</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	multicaster<span class="token punctuation">.</span><span class="token function">setErrorHandler</span><span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> multicaster<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h1 id="convenient-access-to-low-level-resources">Convenient Access to Low-level Resources</h1>
<p>애플리케이션 컨텍스트를 최적으로 사용하고 이해하려면 <a href="https://docs.spring.io/spring-framework/reference/web/webflux-webclient/client-builder.html#webflux-client-builder-reactor-resources">리소스</a>에 설명된 대로 Spring의 <code>Resouce</code> 추상화에 익숙해져야 합니다.</p>
<p>애플리케이션 컨텍스트는 <code>Resource</code> 객체를 로드하는 데 사용할 수 있는 <code>ResourceLoader</code>입니다. <code>Resource</code>는 본질적으로 JDK <code>java.net.URL</code> 클래스의 기능이 더 풍부한 버전입니다. 실제로 <code>Resource</code> 구현은 적절한 경우 <code>java.net.URL</code> 인스턴스를 래핑합니다. <code>Resource</code>는 classpath, 파일 시스템 위치, 표준 URL로 설명할 수 있는 모든 위치 및 기타 변형을 포함하여 거의 모든 위치에서 투명한 방식으로 하위 수준(low-level) 리소스를 얻을 수 있습니다. 리소스 위치 문자열이 특별한 접두사(prefix)가 없는 단순 경로(path)인 경우 해당 리소스의 출처는 구체적이고 실제 애플리케이션 컨텍스트 타입에 적합합니다.</p>
<p>특수 콜백 인터페이스인 <code>ResourceLoaderAware</code>를 구현하기 위해 애플리케이션 컨텍스트에 배포된 Bean을 구성(configure)하여 초기화 시 <code>ResourceLoader</code>로 전달된 애플리케이션 컨텍스트 자체를 사용하여 자동으로 콜백되도록 할 수 있습니다. 정적(static) 리소스에 액세스하는 데 사용되는 <code>Resource</code> 타입의 속성(property)을 노출할 수도 있습니다. 다른 속성(property)과 마찬가지로 여기에 주입됩니다. 이러한 <code>Resource</code> 속성(property)을 간단한 <code>String</code> 경로(paths)로 지정하고 Bean이 배포될 때 해당 텍스트 문자열에서 실제 <code>Resource</code> 객체로 자동 변환을 사용할 수 있습니다.</p>
<p><code>ApplicationContext</code> 생성자에 제공된 위치 경로(path or paths)는 실제로 리소스 문자열이며 간단한 형식으로 특정 컨텍스트 구현에 따라 적절하게 처리됩니다. 예를 들어 <code>ClassPathXmlApplicationContext</code>는 간단한 위치 경로를 classpath 위치로 처리합니다. 또한 실제 컨텍스트 타입에 관계없이 classpath나 URL에서 정의를 강제로 로드하기 위해 특수 접두사가 있는 위치 경로(리소스 문자열)를 사용할 수도 있습니다.</p>
<h1 id="application-startup-tracking">Application Startup Tracking</h1>
<p><code>ApplicationContext</code>는 Spring 애플리케이션의 라이프사이클을 관리하고 컴포넌트에 대한 풍부한 프로그래밍 모델을 제공합니다. 결과적으로 복잡한 애플리케이션에는 구성 요소 그래프와 시작 단계가 똑같이 복잡할 수 있습니다.</p>
<p>특정 지표를 사용하여 애플리케이션 시작 단계를 추적하면 시작 단계 중 어디에 시간이 소비되는지 이해하는 데 도움이 될 수 있지만, 컨텍스트 수명주기를 전체적으로 더 잘 이해하는 방법으로도 사용할 수 있습니다.</p>
<p><code>AbstractApplicationContext</code>(및 해당 하위 클래스)는 다양한 시작 단계에 대한 <code>StartupStep</code> 데이터를 수집하는 <code>ApplicationStartup</code>으로 계측(instrument)됩니다.</p>
<ul>
<li>
<p>애플리케이션 컨텍스트 수명주기(기본 패키지 검색(scanning), 구성(config) 클래스 관리)</p>
</li>
<li>
<p>Bean 수명주기(인스턴스화, 스마트 초기화(smart initialization), 사후 처리(post processing))</p>
</li>
<li>
<p>애플리케이션 이벤트 처리</p>
</li>
</ul>
<p>다음은 <code>AnnotationConfigApplicationContext</code>의 계측 예입니다.</p>
<pre><code class="language-java"><span class="token comment">// create a startup step and start recording</span>
<span class="token class-name">StartupStep</span> scanPackages <span class="token operator">=</span> <span class="token function">getApplicationStartup</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">start</span><span class="token punctuation">(</span><span class="token string">"spring.context.base-packages.scan"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// add tagging information to the current step</span>
scanPackages<span class="token punctuation">.</span><span class="token function">tag</span><span class="token punctuation">(</span><span class="token string">"packages"</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token class-name">Arrays</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>basePackages<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// perform the actual phase we're instrumenting</span>
<span class="token keyword">this</span><span class="token punctuation">.</span>scanner<span class="token punctuation">.</span><span class="token function">scan</span><span class="token punctuation">(</span>basePackages<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// end the current step</span>
scanPackages<span class="token punctuation">.</span><span class="token function">end</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>애플리케이션 컨텍스트는 이미 여러 단계로 계측되었습니다. 일단 기록되면, 특정 도구를 사용하여 이러한 시작 단계를 수집, 표시 및 분석할 수 있습니다. 기존 시작 단계의 전체 목록을 보려면 <a href="https://docs.spring.io/spring-framework/reference/core/appendix/application-startup-steps.html">전용 부록 섹션</a>을 확인하세요.</p>
<p>기본 <code>ApplicationStartup</code> 구현은 오버헤드를 최소화하기 위한 무작동(no-op) 변형입니다. 이는 기본적으로 애플리케이션 시작 중에 측정항목(metric)이 수집되지 않음을 의미합니다. Spring Framework에는 Java Flight Recorder: <code>FlightRecorderApplicationStartup</code>을 사용하여 시작 단계를 추적하기 위한 구현이 포함되어 있습니다. 이 변형을 사용하려면 생성되자마자 해당 인스턴스를 <code>ApplicationContext</code>에 구성(configure)해야 합니다.</p>
<p>개발자는 자체 <code>AbstractApplicationContext</code> 하위 클래스를 제공하거나 보다 정확한 데이터를 수집하려는 경우 <code>ApplicationStartup</code> 인프라를 사용할 수도 있습니다.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
<code>ApplicationStartup</code>은 애플리케이션 시작 중에 그리고 핵심 컨테이너에만 사용됩니다. 이는 결코 Java 프로파일러나 <a href="https://micrometer.io/">Micrometer</a>와 같은 메트릭 라이브러리를 대체하는 것이 아닙니다.</p>
</blockquote>
<p>사용자 정의 <code>StartupStep</code> 수집을 시작하기 위해 컴포넌트는 애플리케이션 컨텍스트에서 직접 <code>ApplicationStartup</code> 인스턴스를 가져오거나, 해당 컴포넌트가 <code>ApplicationStartupAware</code>를 구현하도록 하거나, 주입 지점에서 <code>ApplicationStartup</code> 타입을 요청(ask for)할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
개발자는 사용자 정의 시작 단계를 생성할 때 <code>"spring.*"</code> 네임스페이스를 사용해서는 안 됩니다. 이 네임스페이스는 Spring 내부 사용을 위해 예약되어 있으며 변경될 수 있습니다.</p>
</blockquote>
<h1 id="convenient-applicationcontext-instantiation-for-web-applications">Convenient ApplicationContext Instantiation for Web Applications</h1>
<p>예를 들어 <code>ContextLoader</code>를 사용하여 선언적으로 <code>ApplicationContext</code> 인스턴스를 만들 수 있습니다. 물론 <code>ApplicationContext</code> 구현 중 하나를 사용하여 프로그래밍 방식으로 <code>ApplicationContext</code> 인스턴스를 생성할 수도 있습니다.</p>
<p>다음 예제와 같이 <code>ContextLoaderListener</code>를 사용하여 <code>ApplicationContext</code>를 등록할 수 있습니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>context-param</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-name</span><span class="token punctuation">&gt;</span></span>contextConfigLocation<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param-value</span><span class="token punctuation">&gt;</span></span>/WEB-INF/daoContext.xml /WEB-INF/applicationContext.xml<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param-value</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>context-param</span><span class="token punctuation">&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>listener-class</span><span class="token punctuation">&gt;</span></span>org.springframework.web.context.ContextLoaderListener<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener-class</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>listener</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>리스너는 <code>contextConfigLocation</code> 매개변수를 검사합니다. 매개변수가 없으면 리스너는 <code>/WEB-INF/applicationContext.xml</code>을 기본값으로 사용합니다. 매개변수가 존재하는 경우 리스너는 미리 정의된 구분 기호(쉼표, 세미콜론 및 공백)를 사용하여 <code>String</code>을 구분하고 해당 값을 애플리케이션 컨텍스트가 검색되는 위치로 사용합니다. Ant 스타일 경로 패턴도 지원됩니다. 예를 들면 <code>/WEB-INF/*Context.xml</code>(이름이 <code>Context.xml</code>로 끝나고 <code>WEB-INF</code> 디렉터리에 있는 모든 파일의 경우) 및 <code>/WEB-INF/**/*Context.xml</code>(WEB-INF의 하위 디렉터리에 있는 모든 파일의 경우)입니다.</p>
<h1 id="deploying-a-spring-applicationcontext-as-a-jakarta-ee-rar-file">Deploying a Spring ApplicationContext as a Jakarta EE RAR File</h1>
<p>Spring <code>ApplicationContext</code>를 RAR 파일로 배포하여, Jakarta EE RAR 배포 unit에 컨텍스트와 필요한 모든 Bean 클래스 및 라이브러리 JAR을 캡슐화하는 것이 가능합니다. 이는 Jakarta EE 서버 기능에 액세스할 수 있는 독립형 <code>ApplicationContext</code>(Jakarta EE 환경에서만 호스팅됨)를 부트스트랩하는 것과 동일합니다. RAR 배포는 헤드리스 WAR 파일(사실상 Jakarta EE 환경에서 Spring <code>ApplicationContext</code>를 부트스트래핑하는 데만 사용되는 HTTP 진입점이 없는 WAR 파일)을 배포하는 시나리오에 대한 보다 자연스러운 대안입니다.</p>
<p>RAR 배포는 HTTP 진입점이 필요하지 않고 메시지 endpoint과 예약된 작업(scheduled job)으로만 구성된 애플리케이션 컨텍스트에 이상적입니다. 이러한 컨텍스트의 Bean은 JTA 트랜잭션 관리자, JNDI 바인딩 JDBC <code>DataSource</code> 인스턴스 및 JMS <code>ConnectionFactory</code> 인스턴스와 같은 애플리케이션 서버 리소스를 사용할 수 있으며 Spring의 표준 트랜잭션 관리 및 JNDI 및 JMX 지원 기능을 통해 플랫폼의 JMX 서버에 등록할 수도 있습니다. 애플리케이션 구성 요소는 Spring의 <code>TaskExecutor</code> 추상화를 통해 애플리케이션 서버의 JCA <code>WorkManager</code>와 상호 작용할 수도 있습니다.</p>
<p>RAR 배포와 관련된 구성 세부 정보는 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/jca/context/SpringContextResourceAdapter.html"><code>SpringContextResourceAdapter</code></a> 클래스의 javadoc를 참조하세요.</p>
<p>Spring ApplicationContext를 Jakarta EE RAR 파일로 간단하게 배포하려면 다음을 수행하세요.</p>
<ol>
<li>
<p>모든 애플리케이션 클래스를 RAR 파일(다른 파일 확장자를 가진 표준 JAR 파일)로 패키징합니다.</p>
</li>
<li>
<p>필요한 모든 라이브러리 JAR을 RAR 아카이브의 루트에 추가합니다.</p>
</li>
<li>
<p><code>META-INF/ra.xml</code> 배포 디스크립터(<a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/jca/context/SpringContextResourceAdapter.html"><code>SpringContextResourceAdapter</code>에 대한 javadoc</a>에 표시된 대로) 및 해당 Spring XML Bean 정의 파일(일반적으로 <code>META-INF/applicationContext.xml</code>)을 추가하십시오.</p>
</li>
<li>
<p>결과 RAR 파일을 애플리케이션 서버의 배포 디렉터리에 놓습니다.</p>
</li>
</ol>
<blockquote>
<p><strong>[Note]</strong><br>
이러한 RAR 배포 unit는 일반적으로 자체 포함(self-contained)되어 있습니다. 구성 요소를 외부 세계에 노출하지 않으며 동일한 애플리케이션의 다른 모듈에도 노출하지 않습니다. RAR 기반 <code>ApplicationContext</code>와의 상호 작용은 일반적으로 다른 모듈과 공유하는 JMS 대상을 통해 발생합니다. RAR 기반 <code>ApplicationContext</code>는 예를 들어 일부 작업을 예약하거나 파일 시스템 등의 새 파일에 반응할 수도 있습니다. 외부로부터의 동기 액세스를 허용해야 하는 경우 동일한 시스템의 다른 애플리케이션 모듈에서 사용할 수 있는 RMI 엔드포인트를 내보낼 수 있습니다.</p>
</blockquote>
<hr>
<p><strong>RMI</strong></p>
<p>RMI는 Java Remote Method Invocation의 약자로, 자바에서 원격 객체 간의 통신을 지원하는 기술입니다. RMI를 사용하면 다른 JVM(Java Virtual Machine)에서 실행되는 객체들 간에 메소드 호출을 할 수 있습니다.</p>
<p>RMI를 사용하여 원격 객체를 호출하려면 먼저 원격 인터페이스를 정의해야 합니다. 이 인터페이스는 원격에서 호출될 수 있는 메소드를 포함하고 있습니다. 그런 다음, 이 인터페이스를 구현한 클래스를 작성하고, 이를 원격 객체로 등록합니다. 클라이언트는 이 원격 객체를 사용하여 원격 메소드를 호출할 수 있습니다.</p>
<p>RMI는 분산 시스템에서의 객체 간 통신을 쉽게 구현할 수 있도록 지원하며, Java 플랫폼에서 제공하는 기능 중 하나입니다. RMI는 분산 객체 지향 시스템을 구축하고 클라이언트와 서버 간의 통신을 단순화하는 데 사용됩니다. 이를 통해 여러 JVM에서 실행되는 애플리케이션 간의 통신을 용이하게 할 수 있습니다.</p>