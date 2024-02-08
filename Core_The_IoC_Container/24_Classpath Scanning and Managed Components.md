<p>이 장의 대부분의 예제는 XML을 사용하여 Spring 컨테이너 내에서 각 <code>BeanDefinition</code>을 생성하는 구성 메타데이터를 지정합니다. 이전 섹션(<a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config.html">어노테이션 기반 컨테이너 구성</a>)에서는 소스 수준 어노테이션을 통해 많은 구성 메타데이터를 제공하는 방법을 보여줍니다. 그러나 이러한 예제에서도 "base" Bean 정의는 XML 파일에 명시적으로 정의되어 있는 반면 어노테이션은 종속성 주입만 구동합니다. 이 섹션에서는 클래스패스를 스캔하여 후보 컴포넌트를 암시적으로 탐지하는 옵션에 대해 설명합니다. 후보 컴포넌트는 필터 기준과 일치하고 컨테이너에 등록된 해당 Bean 정의를 갖는 클래스입니다. 이렇게 하면 Bean 등록을 수행하기 위해 XML을 사용할 필요가 없습니다. 대신에 어노테이션(예: <code>@Component</code>), AspectJ 타입 표현식 또는 사용자 정의 필터 기준을 사용하여 컨테이너에 등록된 Bean 정의가 있는 클래스를 선택할 수 있습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
XML 파일을 사용하는 대신 Java를 사용하여 Bean을 정의할 수 있습니다. 이러한 기능을 사용하는 방법에 대한 예를 보려면 <code>@Configuration</code>, <code>@Bean</code>, <code>@Import</code> 및 <code>@DependsOn</code> 주석을 살펴보세요.</p>
</blockquote>
<h1 id="component-and-further-stereotype-annotations">@Component and Further Stereotype Annotations</h1>
<p><code>@Repository</code> 어노테이션은 저장소(데이터 액세스 객체 또는 DAO라고도 함)의 역할이나 stereotype을 충족하는 모든 클래스에 대한 마커입니다. 이 마커의 용도 중에는 <a href="https://docs.spring.io/spring-framework/reference/data-access/orm/general.html#orm-exception-translation">예외 번역(translation)</a>에 설명된 대로 예외 자동 translation이 있습니다.</p>
<p>Spring은 <code>@Component</code>, <code>@Service</code> 및 <code>@Controller</code>와 같은 추가 스테레오타입 어노테이션을 제공합니다. <code>@Component</code>는 Spring 관리 컴포넌트에 대한 일반적인 스테레오타입입니다. <code>@Repository</code>, <code>@Service</code> 및 <code>@Controller</code>는 보다 구체적인 사용 사례(각각 persistence, 서비스 및 프레젠테이션 계층)를 위한 <code>@Component</code>의 특수화입니다. 따라서 <code>@Component</code>로 컴포넌트 클래스에 어노테이션을 달 수 있지만 대신 <code>@Repository</code>, <code>@Service</code> 또는 <code>@Controller</code>로 어노테이션을 달면 클래스가 도구로 처리하거나 측면과 연결하는 데 더 적합합니다. 예를 들어 이러한 스테레오타입 어노테이션은 포인트컷의 이상적인 대상이 됩니다. <code>@Repository</code>, <code>@Service</code> 및 <code>@Controller</code>는 Spring Framework의 향후 릴리스에서 추가 의미(semantics)를 전달할 수도 있습니다. 따라서 서비스 계층에 <code>@Component</code> 또는 <code>@Service</code> 중 하나를 선택하는 경우 <code>@Service</code>가 더 나은 선택입니다. 마찬가지로 앞서 설명한 대로 <code>@Repository</code>는 지속성 레이어에서 자동 예외 변환을 위한 마커로 이미 지원됩니다.</p>
<h1 id="using-meta-annotations-and-composed-annotations">Using Meta-annotations and Composed Annotations</h1>
<p>Spring이 제공하는 많은 어노테이션은 자신의 코드에서 메타 어노테이션으로 사용될 수 있습니다. 메타 어노테이션은 다른 어노테이션에 적용할 수 있는 어노테이션입니다. 예를 들어 앞서 언급한 <code>@Service</code> 주석은 다음 예제와 같이 <code>@Component</code>로 메타 어노테이션이 붙습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token annotation punctuation">@Component</span> <span class="token comment">// (1)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">Service</span> <span class="token punctuation">{</span>

	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>(1) <code>@Component</code>를 사용하면 <code>@Service</code>가 <code>@Component</code>와 동일한 방식으로 처리됩니다.</p>
<p>메타 주석을 결합하여 "composed 어노테이션"을 만들 수도 있습니다. 예를 들어 Spring MVC의 <code>@RestController</code> 주석은 <code>@Controller</code>와 <code>@ResponseBody</code>로 구성됩니다.</p>
<p>또한 composed 어노테이션은 선택적으로 메타 어노테이션의 속성(attribute)을 다시 선언하여 사용자 정의를 허용할 수 있습니다. 이는 메타 어노테이션 속성(attribute)의 하위 집합(subset)만 노출하려는 경우 특히 유용할 수 있습니다. 예를 들어 Spring의 <code>@SessionScope</code> 어노테이션은 범위(scope) 이름을 <code>session</code>에 하드 코딩하지만 여전히 <code>proxyMode</code>의 사용자 정의를 허용합니다. 다음 목록은 <code>SessionScope</code> 어노테이션의 정의를 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Target</span><span class="token punctuation">(</span><span class="token punctuation">{</span><span class="token class-name">ElementType</span><span class="token punctuation">.</span>TYPE<span class="token punctuation">,</span> <span class="token class-name">ElementType</span><span class="token punctuation">.</span>METHOD<span class="token punctuation">}</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Retention</span><span class="token punctuation">(</span><span class="token class-name">RetentionPolicy</span><span class="token punctuation">.</span>RUNTIME<span class="token punctuation">)</span>
<span class="token annotation punctuation">@Documented</span>
<span class="token annotation punctuation">@Scope</span><span class="token punctuation">(</span><span class="token class-name">WebApplicationContext</span><span class="token punctuation">.</span>SCOPE_SESSION<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token annotation punctuation">@interface</span> <span class="token class-name">SessionScope</span> <span class="token punctuation">{</span>

	<span class="token comment">/**
	 * Alias for {@link Scope#proxyMode}.
	 * &lt;p&gt;Defaults to {@link ScopedProxyMode#TARGET_CLASS}.
	 */</span>
	<span class="token annotation punctuation">@AliasFor</span><span class="token punctuation">(</span>annotation <span class="token operator">=</span> <span class="token class-name">Scope</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
	<span class="token class-name">ScopedProxyMode</span> <span class="token function">proxyMode</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">default</span> <span class="token class-name">ScopedProxyMode</span><span class="token punctuation">.</span>TARGET_CLASS<span class="token punctuation">;</span>

<span class="token punctuation">}</span></code></pre>
<p>그런 다음 다음과 같이 <code>proxyMode</code>를 선언하지 않고 <code>@SessionScope</code>를 사용할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token annotation punctuation">@SessionScope</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SessionScopedService</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 예제와 같이 <code>proxyMode</code> 값을 재정의할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token annotation punctuation">@SessionScope</span><span class="token punctuation">(</span>proxyMode <span class="token operator">=</span> <span class="token class-name">ScopedProxyMode</span><span class="token punctuation">.</span>INTERFACES<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SessionScopedUserService</span> <span class="token keyword">implements</span> <span class="token class-name">UserService</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>자세한 내용은 <a href="https://github.com/spring-projects/spring-framework/wiki/Spring-Annotation-Programming-Model">Spring 어노테이션 프로그래밍 모델</a> 위키 페이지를 참조하세요.</p>
<h1 id="automatically-detecting-classes-and-registering-bean-definitions">Automatically Detecting Classes and Registering Bean Definitions</h1>
<p>Spring은 자동으로 스테레오타입 클래스를 감지하고 해당 <code>BeanDefinition</code> 인스턴스를 <code>ApplicationContext</code>에 등록할 수 있습니다. 예를 들어 다음 두 클래스는 이러한 자동 감지에 적합(eligible)합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">;</span>

	<span class="token keyword">public</span> <span class="token class-name">SimpleMovieLister</span><span class="token punctuation">(</span><span class="token class-name">MovieFinder</span> movieFinder<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>movieFinder <span class="token operator">=</span> movieFinder<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Repository</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">JpaMovieFinder</span> <span class="token keyword">implements</span> <span class="token class-name">MovieFinder</span> <span class="token punctuation">{</span>
	<span class="token comment">// implementation elided for clarity</span>
<span class="token punctuation">}</span></code></pre>
<p>이러한 클래스를 자동 감지하고 해당 빈을 등록하려면 <code>@Configuration</code> 클래스에 <code>@ComponentScan</code>을 추가해야 합니다. 여기서 <code>basePackages</code> 속성은 두 클래스의 공통 상위 패키지입니다. (또는 각 클래스의 상위 패키지를 포함하는 쉼표, 세미콜론 또는 공백으로 구분된 목록을 지정할 수 있습니다.)</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span>  <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
간결하게 하기 위해 앞의 예에서는 어노테이션의 <code>value</code> 속성(attribute)(즉, <code>@ComponentScan("org.example")</code>)을 사용했을 수 있습니다. </p>
</blockquote>
<p>다음 대안은 XML을 사용합니다.</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xmlns:</span>context</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/context<span class="token punctuation">"</span></span>
	<span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
<code>&lt;context:component-scan&gt;</code>을 사용하면 <code>&lt;context:annotation-config&gt;</code> 기능이 암시적으로 활성화됩니다. 일반적으로 <code>&lt;context:component-scan&gt;</code>을 사용할 때는 <code>&lt;context:annotation-config&gt;</code> 요소를 포함할 필요가 없습니다.</p>
</blockquote>
<blockquote>
<p><strong>[Note]</strong><br>
클래스패스 패키지를 스캐닝하려면 클래스패스에 해당 디렉토리 항목(entry)이 있어야 합니다. Ant로 JAR을 빌드할 때, JAR 작업의 파일 전용(files-only) 스위치를 활성화하지 않도록 하십시오. 또한 일부 환경에서는 보안 정책에 따라 클래스패스 디렉토리가 노출되지 않을 수 있습니다. 예를 들어 JDK 1.7.0_45 이상의 독립 실행형 앱(매니페스트에 '신뢰할 수 있는 라이브러리' 설정이 필요함)—'<a href="https://stackoverflow.com/questions/19394570/java-jre-7u45-breaks-classloader-getresources">Stackoverflow.com/questions/19394570/ java-jre-7u45-breaks-classloader-getresources</a> 참조 ).</p>
<p>JDK 9의 모듈 경로(Jigsaw)에서 Spring의 클래스패스 검색은 일반적으로 예상대로 작동합니다. 그러나 컴포넌트 클래스가 <code>module-info</code> 설명자(descriptors)로 내보내졌는지 확인하세요. Spring이 클래스의 non-public 멤버를 호출할 것으로 예상한다면 해당 멤버가 '열려 있는지' 확인하세요(즉, <code>module-info</code> 설명자에서 <code>exports</code> 선언 대신 <code>opens</code> 선언을 사용하는지 확인하세요).</p>
</blockquote>
<p>또한, component-scan 요소를 사용할 때 <code>AutowiredAnnotationBeanPostProcessor</code> 및 <code>CommonAnnotationBeanPostProcessor</code>가 모두 암시적으로 포함됩니다. 이는 XML로 제공되는 Bean 구성 메타데이터 없이 두 컴포넌트가 자동 감지되어 함께 연결된다는 의미입니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>false</code> 값으로 <code>annotation-config</code> 속성(attribute)을 포함하여 <code>AutowiredAnnotationBeanPostProcessor</code> 및 <code>CommonAnnotationBeanPostProcessor</code>의 등록을 비활성화할 수 있습니다.</p>
</blockquote>
<h1 id="using-filters-to-customize-scanning">Using Filters to Customize Scanning</h1>
<p>기본적으로 <code>@Component</code>, <code>@Repository</code>, <code>@Service</code>, <code>@Controller</code>, <code>@Configuration</code>으로 어노테이션이 달린 클래스 또는 <code>@Component</code>로 어노테이션이 달린 사용자 지정 어노테이션은 검색된 유일한 후보 구성 요소입니다. 그러나 사용자 정의 필터를 적용하여 이 동작을 수정하고 확장할 수 있습니다. <code>@ComponentScan</code> 주석의 <code>includeFilters</code> 또는 <code>excludeFilters</code> 속성(attribute)으로 추가합니다(또는 XML 구성에서 <code>&lt;context:component-scan&gt;</code> 요소의 <code>&lt;context:include-filter /&gt;</code> 또는 <code>&lt;context:exclude-filter /&gt;</code> 하위 요소로). 각 필터 요소에는 <code>type</code> 및 <code>expression</code> 속성(attributes)이 필요합니다. 다음 표에는 필터링 옵션이 설명되어 있습니다.</p>
<table id="beans-scanning-filters-tbl" class="tableblock frame-all grid-all stretch">
<caption class="title">표 1. 필터 유형</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">필터 type</th>
<th class="tableblock halign-left valign-top">예시 expression</th>
<th class="tableblock halign-left valign-top">설명</th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">annotation (기본값)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.SomeAnnotation</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">대상 컴포넌트의 유형 레벨에서 <em>present</em> 또는 <em>meta-present</em> 되어야 하는 어노테이션.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">assignable</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.SomeClass</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">대상 컴포넌트가 할당 가능한 클래스(또는 인터페이스) (확장하거나 구현함).</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">aspectj</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example..*Service+</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">대상 컴포넌트와 일치하도록 하는 AspectJ 유형 표현식.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">regex</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org\.example\.Default.*</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">대상 컴포넌트의 클래스 이름과 일치하도록 하는 정규 표현식.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">custom</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.example.MyTypeFilter</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>org.springframework.core.type.TypeFilter</code> 인터페이스의 사용자 정의 구현.</p></td>
</tr>
</tbody>
</table>
<p>다음 예에서는 모든 <code>@Repository</code> 어노테이션을 무시하고 대신 "stub" 저장소를 사용하는 구성을 보여줍니다</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">,</span>
		includeFilters <span class="token operator">=</span> <span class="token annotation punctuation">@Filter</span><span class="token punctuation">(</span>type <span class="token operator">=</span> <span class="token class-name">FilterType</span><span class="token punctuation">.</span>REGEX<span class="token punctuation">,</span> pattern <span class="token operator">=</span> <span class="token string">".*Stub.*Repository"</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
		excludeFilters <span class="token operator">=</span> <span class="token annotation punctuation">@Filter</span><span class="token punctuation">(</span><span class="token class-name">Repository</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>다음 목록은 동등한 XML을 보여줍니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>include-filter</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>regex<span class="token punctuation">"</span></span>
				<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>.*Stub.*Repository<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
		<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>exclude-filter</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>annotation<span class="token punctuation">"</span></span>
				<span class="token attr-name">expression</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.springframework.stereotype.Repository<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">context:</span>component-scan</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
어노테이션에서 <code>useDefaultFilters=false</code>를 설정하거나 <code>&lt;component-scan/&gt;</code> 요소의 속성(attribute)으로 <code>use-default-filters="false"</code>를 제공하여 기본 필터를 비활성화할 수도 있습니다. 이는 <code>@Component</code>, <code>@Repository</code>, <code>@Service</code>, <code>@Controller</code>, <code>@RestController</code> 또는 <code>@Configuration</code>으로 어노테이션이 달렸거나 메타 어노테이션이 달린 클래스의 자동 감지를 효과적으로 비활성화합니다.</p>
</blockquote>
<h1 id="defining-bean-metadata-within-components">Defining Bean Metadata within Components</h1>
<p>Spring 컴포넌트는 Bean 정의 메타데이터를 컨테이너에 제공(contribute)할 수도 있습니다. <code>@Configuration</code> 어노테이션이 달린 클래스 내에서 Bean 메타데이터를 정의하는 데 사용된 것과 동일한 <code>@Bean</code> 어노테이션을 사용하여 이를 수행할 수 있습니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FactoryMethodComponent</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"public"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">publicInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"publicInstance"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doWork</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// Component method implementation omitted</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>앞의 클래스는 <code>doWork()</code> 메서드에 애플리케이션 특정 코드가 있는 Spring 컴포넌트입니다. 그러나 이는 <code>publicInstance()</code> 메소드를 참조(reffering)하는 팩토리 메소드가 있는 Bean 정의에도 기여합니다. <code>@Bean</code> 어노테이션은 <code>@Qualifier</code> 어노테이션을 통한 한정자(qualifier) 값과 같은 팩토리 메서드 및 기타 Bean 정의 속성을 식별합니다. 지정할 수 있는 다른 메서드 수준 어노테이션으로는 <code>@Scope</code>, <code>@Lazy</code> 및 사용자 지정 한정자 주석이 있습니다.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
구성 요소 초기화 역할 외에도 <code>@Autowired</code> 또는 <code>@Inject</code>로 표시된 주입 지점에 <code>@Lazy</code> 어노테이션을 배치할 수도 있습니다. 이러한 context에서 이는 lazy-resolution 프록시의 주입으로 이어집니다. 그러나 이러한 프록시 접근 방식은 다소 제한적입니다. 특히 선택적 종속성과 결합된 정교한 지연 상호작용의 경우 대신 <code>ObjectProvider&lt;MyTargetBean&gt;</code>을 권장합니다.</p>
</blockquote>
<p><code>@Bean</code> 메소드의 자동 연결에 대한 추가 지원과 함께 이전에 논의한 대로 자동 연결 필드 및 메소드가 지원됩니다. 다음 예에서는 그 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FactoryMethodComponent</span> <span class="token punctuation">{</span>

	<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token keyword">int</span> i<span class="token punctuation">;</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"public"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">publicInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"publicInstance"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token comment">// use of a custom qualifier and autowiring of method parameters</span>
	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">protected</span> <span class="token class-name">TestBean</span> <span class="token function">protectedInstance</span><span class="token punctuation">(</span>
			<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"public"</span><span class="token punctuation">)</span> <span class="token class-name">TestBean</span> spouse<span class="token punctuation">,</span>
			<span class="token annotation punctuation">@Value</span><span class="token punctuation">(</span><span class="token string">"#{privateInstance.age}"</span><span class="token punctuation">)</span> <span class="token class-name">String</span> country<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token class-name">TestBean</span> tb <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"protectedInstance"</span><span class="token punctuation">,</span> <span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		tb<span class="token punctuation">.</span><span class="token function">setSpouse</span><span class="token punctuation">(</span>spouse<span class="token punctuation">)</span><span class="token punctuation">;</span>
		tb<span class="token punctuation">.</span><span class="token function">setCountry</span><span class="token punctuation">(</span>country<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token keyword">return</span> tb<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token keyword">private</span> <span class="token class-name">TestBean</span> <span class="token function">privateInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"privateInstance"</span><span class="token punctuation">,</span> i<span class="token operator">++</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Bean</span>
	<span class="token annotation punctuation">@RequestScope</span>
	<span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">requestScopedInstance</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"requestScopedInstance"</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>예제에서는 <code>String</code> 메소드 매개변수 <code>country</code>를 <code>privateInstance</code>라는 다른 Bean의 <code>age</code> 속성(property) 값에 자동 연결합니다. Spring Expression Language 요소는 <code>#{ &lt;expression&gt; }</code> 표기법을 통해 속성(property) 값을 정의합니다. <code>@Value</code> 어노테이션의 경우 표현식(expression) 텍스트를 분석할 때 Bean 이름을 찾도록 표현식 resolver가 사전 구성(preconfigured)되어 있습니다.</p>
<p>Spring Framework 4.3부터는 현재 Bean 생성을 트리거하는 타입 주입 지점에 액세스하기 위해 <code>InjectionPoint</code> 타입(또는 더 구체적인 하위 클래스인 <code>DependencyDescriptor</code>)의 팩토리 메서드 매개 변수를 선언할 수도 있습니다. 이는 기존 인스턴스 주입에는 적용되지 않고 실제 Bean 인스턴스 생성에만 적용된다는 점에 유의하세요. 결과적으로 이 기능은 프로토타입 범위의 Bean에 가장 적합합니다. 다른 범위의 경우 팩토리 메소드는 지정된 범위에서 새 Bean 인스턴스 생성을 트리거한 주입 지점만 봅니다(예: lazy 싱글톤 Bean 생성을 트리거한 종속성). 이러한 시나리오에서는 의미론적으로 주의하여 제공된 주입 지점 메타데이터를 사용할 수 있습니다. 다음 예에서는 <code>InjectionPoint</code>를 사용하는 방법을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">FactoryMethodComponent</span> <span class="token punctuation">{</span>

	<span class="token annotation punctuation">@Bean</span> <span class="token annotation punctuation">@Scope</span><span class="token punctuation">(</span><span class="token string">"prototype"</span><span class="token punctuation">)</span>
	<span class="token keyword">public</span> <span class="token class-name">TestBean</span> <span class="token function">prototypeInstance</span><span class="token punctuation">(</span><span class="token class-name">InjectionPoint</span> injectionPoint<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">TestBean</span><span class="token punctuation">(</span><span class="token string">"prototypeInstance for "</span> <span class="token operator">+</span> injectionPoint<span class="token punctuation">.</span><span class="token function">getMember</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>일반(regular) Spring 컴포넌트의 <code>@Bean</code> 메소드는 Spring <code>@Configuration</code> 클래스 내부의 메소드와 다르게 처리됩니다. 차이점은 메소드와 필드의 호출을 가로채기 위해 <code>@Component</code> 클래스가 CGLIB로 향상(enhance)되지 않는다는 것입니다. CGLIB 프록시는 <code>@Configuration</code> 클래스의 <code>@Bean</code> 메소드 내에서 메소드나 필드를 호출하여 공동 객체(collaborating object)에 대한 Bean 메타데이터 참조를 생성하는 수단입니다. 이러한 메소드는 일반적인 Java semantics으로 호출되지 않고, <code>@Bean</code> 메소드에 대한 프로그래밍 방식 호출을 통해 다른 Bean을 참조하는 경우에도, 일반적인 수명주기 관리 및 Spring Bean의 프록시를 제공하기 위해 컨테이너를 통과(go through)합니다. 대조적으로, 일반 <code>@Component</code> 클래스 내의 <code>@Bean</code> 메소드에서 메소드나 필드를 호출하는 것은 특별한 CGLIB 처리나 다른 제약 조건이 적용되지 않는 표준 Java semantics를 갖습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Bean</code> 메소드를 <code>static</code>으로 선언하면 포함하는 구성(configuration) 클래스를 인스턴스로 생성하지 않고도 호출할 수 있습니다. 이는 사후 프로세서(post-processor) Bean(예: <code>BeanFactoryPostProcessor</code> 또는 <code>BeanPostProcessor</code> 타입)을 정의할 때 특히 의미가 있습니다. 이러한 Bean은 컨테이너 수명주기 초기에 초기화되고 해당 시점에서 구성의 다른 부분을 트리거하지 않아야 하기 때문입니다.</p>
<p>기술적인 제한으로 인해 <code>@Configuration</code> 클래스(이 섹션 앞부분에서 설명) 내에서도 정적 <code>@Bean</code> 메서드에 대한 호출은 컨테이너에 의해 차단되지 않습니다. CGLIB 서브클래싱은 non-static 메서드만 재정의(override)할 수 있습니다. 결과적으로 다른 @Bean 메소드에 대한 직접 호출은 표준 Java semantics를 가지며, 결과적으로 팩토리 메소드 자체에서 직접 반환되는 독립 인스턴스가 됩니다.</p>
<p>@Bean 메소드의 Java 언어 가시성은 Spring 컨테이너의 결과 Bean 정의에 즉각적인 영향을 미치지 않습니다. <code>@Configuration</code>이 아닌 클래스와 어디에서나 정적 메서드에 적합하다고 판단되는 대로 팩토리 메서드를 자유롭게 선언할 수 있습니다. 그러나 <code>@Configuration</code> 클래스의 일반(regular) <code>@Bean</code> 메서드는 재정의 가능해야 합니다. 즉, <code>private</code> 또는 <code>final</code>로 선언하면 안 됩니다.</p>
<p><code>@Bean</code> 메소드는 지정된 컴포넌트 또는 구성(configuration) 클래스의 base 클래스뿐만 아니라 컴포넌트 또는 구성(configuration) 클래스에 의해 구현된 인터페이스에 선언된 Java 8 default 메소드에서도 발견됩니다. 이는 복잡한 구성(configuration) arrangement을 구성하는 데 많은 유연성을 허용하며 Spring 4.2부터 Java 8 default 메소드를 통해 다중 상속도 가능합니다.</p>
<p>마지막으로, 단일 클래스는 런타임에 사용 가능한 종속성에 따라 사용할 여러 팩토리 메서드 arrangement로 동일한 Bean에 대해 여러 <code>@Bean</code> 메서드를 보유할 수 있습니다. 이는 다른 구성 시나리오에서 "가장 탐욕스러운" 생성자 또는 팩토리 메서드를 선택하는 것과 동일한 알고리즘입니다. 만족할 수 있는 종속성이 가장 많은 variant가 생성 시 선택됩니다. 이는 컨테이너가 여러 <code>@Autowired</code> 생성자 중에서 선택하는 방법과 유사합니다.</p>
</blockquote>
<h1 id="naming-autodetected-components">Naming Autodetected Components</h1>
<p>스캐닝 프로세스의 일부로 구성 요소가 자동 감지되면 해당 스캐너에 알려진 <code>BeanNameGenerator</code> 전략(strategy)에 의해 해당 Bean 이름이 생성됩니다.</p>
<p>기본적으로 <code>AnnotationBeanNameGenerator</code>가 사용됩니다. Spring <a href="https://docs.spring.io/spring-framework/reference/core/beans/classpath-scanning.html#beans-stereotype-annotations">스테레오타입 어노테이션</a>의 경우 어노테이션의 <code>value</code> 속성(attribute)을 통해 이름을 제공하면 해당 이름이 해당 Bean 정의에서 이름으로 사용됩니다. 이 규칙은 Spring 스테레오타입 어노테이션 대신 <code>@jakarta.annotation.ManagedBean</code>, <code>@javax.annotation.ManagedBean</code>, <code>@jakarta.inject.Named</code> 및 <code>@javax.inject.Named</code>와 같은 JSR-250 및 JSR-330 어노테이션이 사용되는 경우에도 적용됩니다. </p>
<p>Spring Framework 6.1부터는 Bean 이름을 지정하는 데 사용되는 어노테이션 속성(attribute)의 이름이 더 이상 <code>value</code>일 필요가 없습니다. 사용자 정의 스테레오타입 어노테이션은 다른 이름(예: <code>name</code>)으로 속성(attribute)을 선언하고 해당 속성(attribute)에 <code>@AliasFor(annotation = Component.class, attribute = "value")</code>로 어노테이션을 달 수 있습니다. 구체적인 예는 <code>ControllerAdvice#name()</code>의 소스 코드 선언을 참조하세요.</p>
<blockquote>
<p><strong>[Warning]</strong><br>
Spring Framework 6.1부터 convention-based 스테레오타입 이름에 대한 지원은 더 이상 사용되지 않으며 프레임워크의 향후 버전에서 제거될 예정입니다. 결과적으로 사용자 정의 스테레오타입 어노테이션은 <code>@AliasFor</code>를 사용하여 <code>@Component</code>의 <code>value</code> 속성(attribute)에 대한 명시적 별칭을 선언해야 합니다. 구체적인 예는 <code>Repository#value()</code> 및 <code>ControllerAdvice#name()</code>의 소스 코드 선언을 참조하세요.</p>
</blockquote>
<p>그러한 어노테이션이나 다른 감지된 컴포넌트(예: 사용자 정의 필터에 의해 발견된 컴포넌트)에서 명시적인 Bean 이름을 파생할 수 없는 경우 기본 Bean 이름 생성기는 대문자로 표시되지 않고 정규화되지 않은 클래스 이름을 반환합니다. 예를 들어, 다음 컴포넌트 클래스가 감지된 경우 이름은 <code>myMovieLister</code> 및 <code>movieFinderImpl</code>이 됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span><span class="token punctuation">(</span><span class="token string">"myMovieLister"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">SimpleMovieLister</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Repository</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieFinderImpl</span> <span class="token keyword">implements</span> <span class="token class-name">MovieFinder</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>기본 빈 이름 지정 전략을 사용하지 않으려면 사용자 정의 빈 이름 지정 전략을 제공할 수 있습니다. 먼저 <a href="https://docs.spring.io/spring-framework/docs/6.1.5/javadoc-api/org/springframework/beans/factory/support/BeanNameGenerator.html"><code>BeanNameGenerator</code></a> 인터페이스를 구현하고 인수가 없는 기본 생성자(no-arg constructor)를 포함해야 합니다. 그런 다음, 다음 예제 주석 및 Bean 정의에 표시된 대로 스캐너를 구성할 때 완전한 클래스 이름(fully qualifued name)을 제공하십시오.</p>
<blockquote>
<p><strong>[Tip]</strong><br>
동일한 정규화되지 않은 클래스 이름을 가진 여러 자동 감지된 컴포넌트로 인해 이름 지정 충돌이 발생하는 경우(즉, 이름은 동일하지만 다른 패키지에 있는 클래스), 생성된 빈 이름에 대한 완전히 정규화된 클래스 이름을 default 로 제공하는 <code>BeanNameGenerator</code>를 구성해야 할 수도 있습니다. Spring Framework 5.2.3부터는 <code>org.springframework.context.annotation</code> 패키지에 위치한 <code>FullyQualifiedAnnotationBeanNameGenerator</code>를 이러한 목적으로 사용할 수 있습니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">,</span> nameGenerator <span class="token operator">=</span> <span class="token class-name">MyNameGenerator</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example<span class="token punctuation">"</span></span>
		<span class="token attr-name">name-generator</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyNameGenerator<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>일반적으로 다른 구성요소가 이를 명시적으로 참조할 수 있을 때에는 어노테이션으로 이름을 지정하는 것을 고려하십시오. 반면, 컨테이너가 연결을 담당할 때에는 자동 생성된 이름이 적합합니다.</p>
<h1 id="providing-a-scope-for-autodetected-components">Providing a Scope for Autodetected Components</h1>
<p>일반적으로 Spring 관리 구성 요소와 마찬가지로 자동 감지 구성 요소의 기본이자 가장 일반적인 범위는 <code>singleton</code>입니다. 그러나 <code>@Scope</code> 어노테이션으로 지정할 수 있는 다른 범위가 필요한 경우도 있습니다. 다음 예시와 같이 어노테이션 내에 범위 이름을 제공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Scope</span><span class="token punctuation">(</span><span class="token string">"prototype"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Repository</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MovieFinderImpl</span> <span class="token keyword">implements</span> <span class="token class-name">MovieFinder</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>@Scope</code> 어노테이션은 구체적인 Bean 클래스(어노테이션이 달린 컴포넌트의 경우) 또는 팩토리 메서드(<code>@Bean</code> 메서드의 경우)에서만 검사(introspected)됩니다. XML bean 정의와 대조적으로 bean 정의 상속 개념이 없으며 클래스 수준의 상속 계층 구조는 메타데이터 목적과 관련이 없습니다.</p>
</blockquote>
<p>Spring 컨텍스트의 "request" 또는 "session"과 같은 웹 특정 범위에 대한 자세한 내용은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other">request, session, application 및 websocket 범위</a>를 참조하세요. 해당 범위에 대해 사전 빌드된 어노테이션과 마찬가지로 Spring의 메타 어노테이션 접근 방식을 사용하여 자신만의 범위 지정 어노테이션을 작성할 수도 있습니다. 예를 들어 <code>@Scope("prototype")</code>로 메타 어노테이션이 달린 사용자 정의 프록시 모드 범위 어노테이션도 선언 가능할 수 있습니다. </p>
<blockquote>
<p><strong>[Note]</strong><br>
어노테이션 기반 접근 방식에 의존하지 않고 범위 resolution을 위한 사용자 지정 전략을 제공하려면 <code>ScopeMetadataResolver</code> 인터페이스를 구현할 수 있습니다. 인수가 없는 기본 생성자(no-arg constructor)를 포함해야 합니다. 그런 다음 어노테이션과 Bean 정의에 대한 다음 예제와 같이 스캐너를 구성할 때 정규화된 클래스 이름을 제공할 수 있습니다.</p>
</blockquote>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">,</span> scopeResolver <span class="token operator">=</span> <span class="token class-name">MyScopeResolver</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">context:</span>component-scan</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example<span class="token punctuation">"</span></span> <span class="token attr-name">scope-resolver</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>org.example.MyScopeResolver<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>특정 non-singleton 범위를 사용하는 경우 범위가 지정된 개체에 대한 프록시를 생성해야 할 수도 있습니다. 추론은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html#beans-factory-scopes-other-injection">종속성으로서의 범위 지정 빈</a>에 설명되어 있습니다. 이를 위해 컴포넌트-스캔 요소에서 scoped-proxy 속성(attribute)을 사용할 수 있습니다. 가능한 세 가지 값은 <code>no</code>, <code>interfaces</code> 및 <code>targetClass</code>입니다. 예를 들어, 다음 구성은 표준 JDK 동적 프록시를 생성합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@ComponentScan</span><span class="token punctuation">(</span>basePackages <span class="token operator">=</span> <span class="token string">"org.example"</span><span class="token punctuation">,</span> scopedProxy <span class="token operator">=</span> <span class="token class-name">ScopedProxyMode</span><span class="token punctuation">.</span>INTERFACES<span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AppConfig</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token generics"><span class="token punctuation">&lt;</span>beans<span class="token punctuation">&gt;</span></span>
	<span class="token operator">&lt;</span>context<span class="token operator">:</span>component<span class="token operator">-</span>scan base<span class="token operator">-</span><span class="token keyword">package</span><span class="token operator">=</span><span class="token string">"org.example"</span> scoped<span class="token operator">-</span>proxy<span class="token operator">=</span><span class="token string">"interfaces"</span><span class="token operator">/</span><span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>beans<span class="token operator">&gt;</span></code></pre>
<h1 id="providing-qualifier-metadata-with-annotations">Providing Qualifier Metadata with Annotations</h1>
<p><code>@Qualifier</code> 어노테이션은 <a href="https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/autowired-qualifiers.html">한정자를 사용한 어노테이션 기반 autowiring 미세 조정</a>에서 논의됩니다. 해당 섹션의 예제에서는 자동 연결 후보를 resolve할 때 세부적인 제어를 제공하기 위해 <code>@Qualifier</code> 어노테이션과 사용자 정의 한정자 어노테이션을 사용하는 방법을 보여줍니다. 이러한 예제는 XML Bean 정의를 기반으로 했기 때문에, XML에 있는 <code>bean</code> 요소의 <code>qualifier</code> 또는 <code>meta</code> 하위 요소를 사용하여 후보 Bean 정의에 한정자 메타데이터가 제공되었습니다. 컴포넌트 자동 감지를 위해 클래스패스 검색을 사용하는 경우 후보 클래스에 대한 type-level 어노테이션이 포함된 한정자 메타데이터를 제공할 수 있습니다. 다음 세 가지 예에서는 이 기술을 보여줍니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Qualifier</span><span class="token punctuation">(</span><span class="token string">"Action"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ActionMovieCatalog</span> <span class="token keyword">implements</span> <span class="token class-name">MovieCatalog</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Genre</span><span class="token punctuation">(</span><span class="token string">"Action"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ActionMovieCatalog</span> <span class="token keyword">implements</span> <span class="token class-name">MovieCatalog</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Offline</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CachingMovieCatalog</span> <span class="token keyword">implements</span> <span class="token class-name">MovieCatalog</span> <span class="token punctuation">{</span>
	<span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
대부분의 어노테이션 기반 대안과 마찬가지로 어노테이션 메타데이터는 클래스 정의 자체에 바인딩되는 반면, XML을 사용하면 해당 메타데이터가 클래스별이 아닌 인스턴스별로 제공되기 때문에 한정자 메타데이터의 변형을 제공하기 위해 동일한 유형의 여러 Bean이 허용된다는 점을 명심하세요.</p>
</blockquote>