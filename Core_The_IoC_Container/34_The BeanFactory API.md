<p><code>BeanFactory</code> API는 Spring의 IoC 기능에 대한 기본 기반을 제공합니다. 특정 계약(contract)은 Spring의 다른 부분 및 관련 타사 프레임워크와 통합하는 데 주로 사용되며 <code>DefaultListableBeanFactory</code> 구현은 상위 수준 <code>GenericApplicationContext</code> 컨테이너 내의 주요 위임자(key delegate)입니다.</p>
<p><code>BeanFactory</code> 및 관련 인터페이스(예: <code>BeanFactoryAware</code>, <code>InitializingBean</code>, <code>DisposableBean</code>)는 다른 프레임워크 컴포넌트에 대한 중요한 통합 지점입니다. 어노테이션이나 리플렉션이 필요하지 않으므로 컨테이너와 해당 컴포넌트 간의 매우 효율적인 상호 작용이 가능합니다. 애플리케이션 수준 Bean은 동일한 콜백 인터페이스를 사용할 수 있지만 일반적으로 어노테이션이나 프로그래밍 방식 구성(configuration)을 통해 대신 선언적 종속성 주입을 선호합니다.</p>
<p>핵심 <code>BeanFactory</code> API 레벨과 <code>DefaultListableBeanFactory</code> 구현은 사용되는 구성(configuration) 형식이나 컴포넌트 어노테이션에 대해 가정하지 않는다는 점에 유의하세요. 이러한 모든 특징은 확장(예: <code>XmlBeanDefinitionReader</code> 및 <code>AutowiredAnnotationBeanPostProcessor</code>)을 통해 제공되며 공유 <code>BeanDefinition</code> 객체에서 핵심 메타데이터 표현으로 작동합니다. 이것이 Spring 컨테이너를 유연하고 확장 가능하게 만드는 핵심입니다.</p>
<h1 id="beanfactory-or-applicationcontext"><code>BeanFactory</code> or <code>ApplicationContext</code>?</h1>
<p>이 섹션에서는 <code>BeanFactory</code>와 <code>ApplicationContext</code> 컨테이너 레벨 간의 차이점과 부트스트래핑에 대한 의미를 설명합니다.</p>
<p>그렇게 하지 않을 타당한 이유가 없는 한 <code>ApplicationContext</code>를 사용해야 하며, <code>GenericApplicationContext</code> 및 해당 하위 클래스 <code>AnnotationConfigApplicationContext</code>를 사용자 정의 부트스트래핑에 대한 일반적인(common) 구현으로 사용해야 합니다. 이는 모든 일반적인 목적을 위한 Spring의 핵심 컨테이너에 대한 주요 진입점입니다: 구성(configuration) 파일 로딩, classspath 스캔 트리거, 프로그래밍 방식으로 빈 정의 및 어노테이션이 달린 클래스 등록, 함수적 빈 정의 등록(5.0 기준).</p>
<p><code>ApplicationContext</code>는 <code>BeanFactory</code>의 모든 기능을 포함하기 때문에 일반적으로 Bean 처리에 대한 전체 제어가 필요한 시나리오를 제외하고 일반 <code>BeanFactory</code>보다 권장됩니다. <code>ApplicationContext</code>(예: <code>GenericApplicationContext</code> 구현) 내에서 여러 종류의 Bean이 규칙(즉, bean name 또는 bean type, 특히 후처리자(post-processprs))으로 감지되는 반면 일반 <code>DefaultListableBeanFactory</code>는 특수 Bean에 대해 불가지론적입니다.</p>
<p>어노테이션 처리 및 AOP 프록시와 같은 많은 확장된 컨테이너 기능의 경우 <a href="https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-bpp"><code>BeanPostProcessor</code> 확장 지점</a>이 필수적입니다. 일반 <code>DefaultListableBeanFactory</code>만 사용하는 경우 이러한 사후 프로세서(post-processors)는 기본적으로 감지 및 활성화되지 않습니다. 실제로 Bean 구성(configuration)에는 잘못된 것이 없기 때문에 이러한 상황은 혼란스러울 수 있습니다. 오히려 이러한 시나리오에서는 추가 설정을 통해 컨테이너를 완전히 부트스트랩해야 합니다.</p>
<p>다음 표에는 <code>BeanFactory</code> 및 <code>ApplicationContext</code> 인터페이스와 구현이 제공하는 기능이 나열되어 있습니다.</p>
<table id="context-introduction-ctx-vs-beanfactory-feature-matrix" class="tableblock frame-all grid-all stripes-odd stretch">
<caption class="title">표 1. 기능 매트릭스</caption>





<thead>
<tr>
<th class="tableblock halign-left valign-top">기능</th>
<th class="tableblock halign-left valign-top"><code>BeanFactory</code></th>
<th class="tableblock halign-left valign-top"><code>ApplicationContext</code></th>
</tr>
</thead>
<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">빈 인스턴스화/와이어링</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">통합된 라이프사이클 관리</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">아니요</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">자동 <code>BeanPostProcessor</code> 등록</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">아니요</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">자동 <code>BeanFactoryPostProcessor</code> 등록</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">아니요</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">편리한 <code>MessageSource</code> 접근(국제화를 위해)</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">아니요</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock">내장된 <code>ApplicationEvent</code> 게시 메커니즘</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">아니요</p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock">가능</p></td>
</tr>
</tbody>
</table>
<p><code>DefaultListableBeanFactory</code>를 사용하여 Bean 사후 프로세서(post-processor)를 명시적으로 등록하려면 다음 예제와 같이 프로그래밍 방식으로 <code>addBeanPostProcessor</code>를 호출해야 합니다.</p>
<pre><code class="language-java"><span class="token class-name">DefaultListableBeanFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultListableBeanFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// populate the factory with bean definitions</span>

<span class="token comment">// now register any needed BeanPostProcessor instances</span>
factory<span class="token punctuation">.</span><span class="token function">addBeanPostProcessor</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">AutowiredAnnotationBeanPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
factory<span class="token punctuation">.</span><span class="token function">addBeanPostProcessor</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyBeanPostProcessor</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// now start using the factory</span></code></pre>
<p><code>BeanFactoryPostProcessor</code>를 일반 <code>DefaultListableBeanFactory</code>에 적용하려면 다음 예제와 같이 <code>postProcessBeanFactory</code> 메소드를 호출해야 합니다.</p>
<pre><code class="language-java"><span class="token class-name">DefaultListableBeanFactory</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">DefaultListableBeanFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">XmlBeanDefinitionReader</span> reader <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">XmlBeanDefinitionReader</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">;</span>
reader<span class="token punctuation">.</span><span class="token function">loadBeanDefinitions</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FileSystemResource</span><span class="token punctuation">(</span><span class="token string">"beans.xml"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// bring in some property values from a Properties file</span>
<span class="token class-name">PropertySourcesPlaceholderConfigurer</span> cfg <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">PropertySourcesPlaceholderConfigurer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
cfg<span class="token punctuation">.</span><span class="token function">setLocation</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">FileSystemResource</span><span class="token punctuation">(</span><span class="token string">"jdbc.properties"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">// now actually do the replacement</span>
cfg<span class="token punctuation">.</span><span class="token function">postProcessBeanFactory</span><span class="token punctuation">(</span>factory<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>두 경우 모두 명시적인 등록 단계가 불편하기 때문에 Spring 지원 애플리케이션에서 일반 <code>DefaultListableBeanFactory</code>보다 다양한 <code>ApplicationContext</code> 변형이 선호되는 이유입니다. 특히 일반적인 엔터프라이즈 설정에서 확장된 컨테이너 기능을 위해 <code>BeanFactoryPostProcessor</code>및 <code>BeanPostProcessor</code> 인스턴스에 의존할 때 더욱 그렇습니다.</p>
<blockquote>
<p><strong>[Note]</strong><br>
<code>AnnotationConfigApplicationContext</code>에는 등록된 모든 공통 어노테이션 사후 프로세서(post-processor)가 있으며 <code>@EnableTransactionManagement</code>와 같은 구성(configuration) 어노테이션을 통해 덮개 아래에 추가 프로세서를 가져올 수 있습니다. Spring의 어노테이션 기반 구성 모델의 추상화 수준에서 Bean 후처리기의 개념은 단순한 내부 컨테이너 세부사항이 됩니다.</p>
</blockquote>