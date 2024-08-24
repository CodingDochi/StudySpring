<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann.html">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>Servlet의 <code>WebApplicationContext</code>에서 표준 Spring Bean 정의를 사용하여 컨트롤러 Bean을 정의할 수 있습니다. <code>@Controller</code> 스테레오타입은 클래스 경로에서 <code>@Component</code> 클래스를 감지하고 이에 대한 Bean 정의를 자동 등록하기 위한 Spring의 일반 지원에 맞춰 자동 감지를 허용합니다. 또한 웹 구성 요소로서의 역할을 나타내는 주석이 달린 클래스에 대한 스테레오타입 역할도 합니다.</p>
<p>이러한 <code>@Controller</code> Bean의 자동 감지를 활성화하려면 다음 예제와 같이 Java 구성에 구성 요소 검색을 추가할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span><span class="token string">"org.example.web"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">WebConfig</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예에서는 앞의 예와 동일한 XML 구성을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>p</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/p<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>
		http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.web<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

	<span class="token comment">&lt;!-- ... --&gt;</span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p><code>@RestController</code>는 모든 메서드가 유형 수준 <code>@ResponseBody</code> annotation을 상속하는 컨트롤러를 나타내기 위해 <code>@Controller</code> 및 <code>@ResponseBody</code>로 자체적으로 메타 annotation이 붙은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html#beans-meta-annotations">composed annotation</a>입니다. 따라서 응답 본문과 뷰 resolution 및 HTML 템플릿 렌더링에 직접 씁니다. </p>
<h2 id="aop-proxies">AOP Proxies</h2>
<p><a href="https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann.html#webflux-ann-requestmapping-proxying">반응형 스택에서 이에 상응하는 내용 보기</a></p>
<p>어떤 경우에는 런타임에 AOP 프록시로 컨트롤러를 장식해야 할 수도 있습니다. 한 가지 예는 컨트롤러에 <code>@Transactional</code> annotation을 직접 포함하도록 선택한 경우입니다. 이 경우 특히 컨트롤러의 경우 클래스 기반 프록시를 사용하는 것이 좋습니다. 이는 컨트롤러에 직접 annotation이 있는 경우 자동으로 발생합니다.</p>
<p>컨트롤러가 인터페이스를 구현하고 AOP 프록시가 필요한 경우 클래스 기반 프록시를 명시적으로 구성해야 할 수도 있습니다. 예를 들어 <code>@EnableTransactionManagement를</code> 사용하면 <code>@EnableTransactionManagement(proxyTargetClass = true)</code>로 변경할 수 있고, <code>&lt;tx:annotation-driven/&gt;</code>를 사용하면 <code>&lt;tx:annotation-driven Proxy-target-class="true"/&gt;</code>로 변경할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
6.0부터 인터페이스 프록시를 사용하면 Spring MVC는 더 이상 인터페이스의 유형 수준 <code>@RequestMapping</code> annotation에만 기반한 컨트롤러를 감지하지 않습니다. 클래스 기반 프록시를 활성화하세요. 그렇지 않으면 인터페이스에도 <code>@Controller</code> annotation이 있어야 합니다.</p>
</blockquote>