<blockquote>
<h3 id="spring을-구성하는데-xml보다-어노테이션이-더-나은가요">Spring을 구성하는데 XML보다 어노테이션이 더 나은가요?</h3>
<p>어노테이션 기반 구성의 도입으로 인해 이 접근 방식이 XML보다 "더 나은"지에 대한 의문이 제기되었습니다. 짧은 대답은 “상황에 따라 다르다”입니다. 긴 대답은 각 접근 방식에는 장단점이 있으며 일반적으로 어떤 전략이 더 적합한지 결정하는 것은 개발자의 몫이라는 것입니다. 정의된 방식으로 인해 어노테이션은 선언에 많은 컨텍스트를 제공하므로 더 짧고 간결한 구성이 가능합니다. 그러나 XML은 소스 코드를 건드리거나 다시 컴파일하지 않고도 구성 요소를 연결하는 데 탁월합니다. 일부 개발자는 소스에 가까운 wiring을 선호하는 반면 다른 개발자는 어노테이션이 달린 클래스가 더 이상 POJO가 아니며 더 나아가 구성이 분산화되고 제어하기가 더 어려워진다고 주장합니다.</p>
<p>어떤 선택을 하든 Spring은 두 가지 스타일을 모두 수용할 수 있으며 심지어 함께 혼합할 수도 있습니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/java.html">JavaConfig</a> 옵션을 통해 Spring은 대상 컴포넌트의 소스 코드를 건드리지 않고 비침습적인 방식으로 어노테이션을 사용할 수 있으며 도구 측면에서 모든 구성 스타일이 Visual Studio 코드 및 Theia, Eclipse용 <a href="https://spring.io/tools">Spring Tools</a>에서 지원된다는 점을 지적할 가치가 있습니다. </p>
</blockquote>
<p>XML 설정에 대한 대안은 XML 선언 대신 구성요소를 연결하기 위해 바이트코드 메타데이터에 의존하는 어노테이션 기반 구성으로 제공됩니다. 개발자는 XML을 사용하여 Bean wiring을 설명하는 대신 관련 클래스, 메서드 또는 필드 선언에 대한 어노테이션을 사용하여 구성을 컴포넌트 클래스 자체로 옮깁니다. <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-bpp-examples-aabpp">Example: <code>AutowiredAnnotationBeanPostProcessor</code></a>에서 언급했듯이 어노테이션과 함께 <code>BeanPostProcessor</code>를 사용하는 것은 Spring IoC 컨테이너를 확장하는 일반적인 방법입니다. 예를 들어 <code>@Autowired</code> 어노테이션은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html">Autowiring Collaborators</a>에 설명된 것과 동일한 기능을 제공하지만 더 세밀한 제어와 더 넓은 적용 가능성을 제공합니다. 또한 Spring은 <code>@PostConstruct</code> 및 <code>@PreDestroy</code>와 같은 JSR-250 주석에 대한 지원뿐만 아니라 <code>@Inject</code> 및 <code>@Named</code>와 같은 <code>jakarta.inject</code> 패키지에 포함된 JSR-330(Java용 종속성 주입) 어노테이션에 대한 지원도 제공합니다. 해당 어노테이션에 대한 세부정보는 <a href="https://docs.spring.io/spring-framework/reference/core/beans/standard-annotations.html">관련 섹션</a>에서 확인할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
XML 삽입 전에 어노테이션 삽입이 수행됩니다. 따라서 XML 구성은 두 접근 방식을 통해 연결된 속성에 대한 어노테이션을 재정의합니다.</p>
</blockquote>
<p>언제나 그렇듯, 후처리기(post-processors)를 개별 빈 정의로 등록할 수 있지만 XML 기반 Spring 구성에 다음 태그를 포함하여 암시적으로 등록할 수도 있습니다(<code>context</code> 네임스페이스 포함에 유의하세요).</p>
<pre><code class="language-xml">
<span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>annotation-config</span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>&lt;context:annotation-config/&gt;</code> 요소는 다음 post-processor를 암시적으로 등록합니다.</p>
<ul>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/ConfigurationClassPostProcessor.html"><code>ConfigurationClassPostProcessor</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/beans/factory/annotation/AutowiredAnnotationBeanPostProcessor.html"><code>AutowiredAnnotationBeanPostProcessor</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/annotation/CommonAnnotationBeanPostProcessor.html"><code>CommonAnnotationBeanPostProcessor</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/orm/jpa/support/PersistenceAnnotationBeanPostProcessor.html"><code>PersistenceAnnotationBeanPostProcessor</code></a></p>
</li>
<li>
<p><a href="https://docs.spring.io/spring-framework/docs/6.1.4/javadoc-api/org/springframework/context/event/EventListenerMethodProcessor.html"><code>EventListenerMethodProcessor</code></a></p>
</li>
</ul>
<blockquote>
<p><strong>[Note]</strong><br>
<code>&lt;context:annotation-config/&gt;</code>는 정의된 동일한 애플리케이션 컨텍스트의 Bean에 대한 어노테이션만 찾습니다. 이는 <code>DispatcherServlet</code>의 <code>WebApplicationContext</code>에 <code>&lt;context:annotation-config/&gt;</code>를 넣으면 서비스가 아닌 컨트롤러의 <code>@Autowired</code> 빈만 확인한다는 의미입니다. 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet.html"><code>DispatcherServlet</code></a>을 참조하세요.</p>
</blockquote>