<p>이 섹션에서는 다음 중 하나를 통해 Spring Data JPA를 구성하는 방법을 설명합니다. </p>
<ul>
<li><a href="https://docs.spring.io/spring-data/jpa/reference/repositories/create-instances.html#jpa.java-config">"annotation 기반 구성"</a>(Java 구성) </li>
<li><a href="https://docs.spring.io/spring-data/jpa/reference/repositories/create-instances.html#repositories.create-instances.xml">“Spring 네임스페이스”</a>(XML 구성)</li>
</ul>
<h1 id="annotation-based-configuration">Annotation-based Configuration</h1>
<p>Spring Data JPA 리포지토리 지원은 다음 예제와 같이 JavaConfig와 사용자 정의 XML 네임스페이스를 통해 활성화될 수 있습니다.</p>
<p>Example 1. Spring Data JPA repositories using JavaConfig</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Configuration</span>
<span class="token annotation punctuation">@EnableJpaRepositories</span>
<span class="token annotation punctuation">@EnableTransactionManagement</span>
<span class="token keyword">class</span> <span class="token class-name">ApplicationConfig</span> <span class="token punctuation">{</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token keyword">public</span> <span class="token class-name">DataSource</span> <span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">EmbeddedDatabaseBuilder</span> builder <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">EmbeddedDatabaseBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> builder<span class="token punctuation">.</span><span class="token function">setType</span><span class="token punctuation">(</span><span class="token class-name">EmbeddedDatabaseType</span><span class="token punctuation">.</span>HSQL<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token keyword">public</span> <span class="token class-name">LocalContainerEntityManagerFactoryBean</span> <span class="token function">entityManagerFactory</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">HibernateJpaVendorAdapter</span> vendorAdapter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HibernateJpaVendorAdapter</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    vendorAdapter<span class="token punctuation">.</span><span class="token function">setGenerateDdl</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">LocalContainerEntityManagerFactoryBean</span> factory <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">LocalContainerEntityManagerFactoryBean</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    factory<span class="token punctuation">.</span><span class="token function">setJpaVendorAdapter</span><span class="token punctuation">(</span>vendorAdapter<span class="token punctuation">)</span><span class="token punctuation">;</span>
    factory<span class="token punctuation">.</span><span class="token function">setPackagesToScan</span><span class="token punctuation">(</span><span class="token string">"com.acme.domain"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    factory<span class="token punctuation">.</span><span class="token function">setDataSource</span><span class="token punctuation">(</span><span class="token function">dataSource</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> factory<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>

  <span class="token annotation punctuation">@Bean</span>
  <span class="token keyword">public</span> <span class="token class-name">PlatformTransactionManager</span> <span class="token function">transactionManager</span><span class="token punctuation">(</span><span class="token class-name">EntityManagerFactory</span> entityManagerFactory<span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">JpaTransactionManager</span> txManager <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JpaTransactionManager</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    txManager<span class="token punctuation">.</span><span class="token function">setEntityManagerFactory</span><span class="token punctuation">(</span>entityManagerFactory<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> txManager<span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<blockquote>
<p><strong>[Note]</strong><br>
<code>EntityManagerFactory</code>를 생성하는 것 외에도 예외 변환 메커니즘에도 참여하므로 <code>EntityManagerFactory</code>가 아닌 <code>LocalContainerEntityManagerFactoryBean</code>을 직접 생성해야 합니다.</p>
</blockquote>
<p>앞의 구성 클래스는 <code>spring-jdbc</code>의 <code>EmbeddedDatabaseBuilder</code> API를 사용하여 임베디드 HSQL 데이터베이스를 설정합니다. 그런 다음 Spring Data는 <code>EntityManagerFactory</code>를 설정하고 Hibernate를 샘플 persistence provider로 사용합니다. 여기에 선언된 마지막 인프라 구성 요소는 <code>JpaTransactionManager</code>입니다. 마지막으로 이 예제에서는 본질적으로 XML 네임스페이스와 동일한 속성을 전달하는 <code>@EnableJpaRepositories</code> annotation을 사용하여 Spring Data JPA 저장소를 활성화합니다. base package가 구성되어 있지 않으면 configuration 클래스가 있는 패키지를 사용합니다.</p>
<h1 id="spring-namespace">Spring Namespace</h1>
<p>Spring Data의 JPA 모듈에는 저장소 Bean 정의를 허용하는 사용자 정의 네임스페이스가 포함되어 있습니다. 또한 JPA에 특별한 특정 기능과 요소 속성도 포함되어 있습니다. 일반적으로 JPA 저장소는 다음 예제와 같이 <code>repositories</code> 요소를 사용하여 설정할 수 있습니다.</p>
<p>Example 2. Setting up JPA repositories by using the namespace</p>
<pre><code class="language-xml"><span class="token prolog">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>beans</span> <span class="token attr-name">xmlns</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans<span class="token punctuation">"</span></span>
  <span class="token attr-name"><span class="token namespace">xmlns:</span>xsi</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.w3.org/2001/XMLSchema-instance<span class="token punctuation">"</span></span>
  <span class="token attr-name"><span class="token namespace">xmlns:</span>jpa</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/data/jpa<span class="token punctuation">"</span></span>
  <span class="token attr-name"><span class="token namespace">xsi:</span>schemaLocation</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/jpa
    https://www.springframework.org/schema/data/jpa/spring-jpa.xsd<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">jpa:</span>repositories</span> <span class="token attr-name">base-package</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>com.acme.repositories<span class="token punctuation">"</span></span> <span class="token punctuation">/&gt;</span></span>

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>beans</span><span class="token punctuation">&gt;</span></span></code></pre>
<blockquote>
<p><strong>[Tip]</strong><br>
JavaConfig와 XML 중 어느 것이 더 낫습니까? XML은 오래 전에 Spring이 구성된 방식입니다. 오늘날 빠르게 성장하는 Java, 레코드 유형, 주석 등의 시대에 새로운 프로젝트는 일반적으로 가능한 한 많은 순수 Java를 사용합니다. XML 지원을 즉시 제거할 계획은 없지만 최신 기능 중 일부는 XML을 통해 사용하지 못할 수도 있습니다.</p>
</blockquote>
<p><code>repositories</code> 요소를 사용하면 <code>@Repository</code>로 annotation이 달린 모든 빈에 대한 persistence 예외 변환이 활성화되어 JPA persistence provider가 던진 예외가 Spring의 <code>DataAccessException</code> 계층으로 변환될 수 있습니다.</p>
<h2 id="custom-namespace-attributes">Custom Namespace Attributes</h2>
<p><code>repositories</code> 요소의 기본 속성 외에도 JPA 네임스페이스는 저장소 설정을 보다 세부적으로 제어할 수 있는 추가 속성을 제공합니다.</p>
<p>Table 1. Custom JPA-specific attributes of the repositories element</p>
<table class="tableblock frame-all grid-all fit-content">
<caption class="title">표 1. <code>repositories</code> 요소의 사용자 정의 JPA 특정 속성</caption>




<tbody>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>entity-manager-factory-ref</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>repositories</code> 요소에서 감지된 리포지토리에 사용할 <code>EntityManagerFactory</code>를 명시적으로 연결합니다. 일반적으로 응용 프로그램 내에서 여러 개의 <code>EntityManagerFactory</code> 빈을 사용하는 경우에 사용됩니다. 구성되지 않은 경우 Spring Data는 자동으로 <code>ApplicationContext</code>에서 이름이 <code>entityManagerFactory</code>인 <code>EntityManagerFactory</code> 빈을 찾습니다.</p></td>
</tr>
<tr>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>transaction-manager-ref</code></p></td>
<td class="tableblock halign-left valign-top"><p class="tableblock"><code>repositories</code> 요소에서 감지된 리포지토리에 사용할 <code>PlatformTransactionManager</code>를 명시적으로 연결합니다. 일반적으로 여러 개의 트랜잭션 관리자 또는 <code>EntityManagerFactory</code> 빈이 구성된 경우에만 필요합니다. 현재 <code>ApplicationContext</code> 내에서 정의된 단일 지정된 <code>PlatformTransactionManager</code>로 기본 설정됩니다.</p></td>
</tr>
</tbody>
</table>
<blockquote>
<p><strong>[Note]</strong><br>
Spring Data JPA에서는 명시적인 <code>transaction-manager-ref</code>가 정의되지 않은 경우 <code>transactionManager</code>라는 <code>PlatformTransactionManager</code> 빈이 있어야 합니다.</p>
</blockquote>
<h1 id="bootstrap-mode">Bootstrap Mode</h1>
<p>기본적으로 Spring Data JPA 리포지토리는 기본 Spring Bean입니다. 싱글톤 범위이며 열심히 초기화됩니다. 시작하는 동안 검증 및 메타데이터 분석 목적으로 이미 JPA <code>EntityManager</code>와 상호 작용합니다. Spring Framework는 백그라운드 스레드에서 JPA <code>EntityManagerFactory</code>의 초기화를 지원합니다. 그 이유는 해당 프로세스가 일반적으로 Spring 애플리케이션에서 상당한 시작 시간을 차지하기 때문입니다. 백그라운드 초기화를 효과적으로 사용하려면 JPA 저장소가 가능한 한 늦게 초기화되도록 해야 합니다.</p>
<p>Spring Data JPA 2.1부터 이제 다음 값을 사용하는 <code>BootstrapMode</code>(<code>@EnableJpaRepositories</code> annotation 또는 XML 네임스페이스를 통해)를 구성할 수 있습니다.</p>
<ul>
<li>
<p><code>DEFAULT</code>(기본값)  -  <code>@Lazy</code>로 명시적으로 주석을 달지 않는 한 리포지토리는 즉시 인스턴스화됩니다. 지연화는 리포지토리 빈의 초기화가 필요한 클라이언트 빈에 리포지토리 인스턴스가 필요하지 않은 경우에만 효과가 있습니다.</p>
</li>
<li>
<p><code>LAZY</code>  -  모든 저장소 Bean을 암시적으로 지연 선언하고 지연 초기화 프록시가 생성되어 클라이언트 Bean에 삽입되도록 합니다. 즉, 클라이언트 Bean이 초기화 중에 저장소를 사용하지 않고 단순히 필드에 인스턴스를 저장하는 경우 저장소가 인스턴스화되지 않습니다. 리포지토리 인스턴스는 리포지토리와 처음 상호 작용할 때 초기화되고 확인됩니다.</p>
</li>
<li>
<p><code>DEFERRED</code> — 기본적으로 <code>LAZY</code>와 동일한 작업 모드이지만 애플리케이션이 완전히 시작되기 전에 리포지토리가 확인되도록 <code>ContextRefreshedEvent</code>에 대한 응답으로 리포지토리 초기화를 트리거합니다.</p>
</li>
</ul>
<h2 id="recommendations">Recommendations</h2>
<p>기본 부트스트랩 모드로 비동기 JPA 부트스트랩 스틱을 사용하지 않는 경우.</p>
<p>JPA를 비동기적으로 부트스트랩하는 경우 <code>DEFERRED</code>는 합리적인 기본값입니다. 이는 Spring Data JPA 부트스트랩이 <code>EntityManagerFactory</code> 설정 자체가 다른 모든 애플리케이션 component를 초기화하는 것보다 오래 걸리는 경우에만 <code>EntityManagerFactory</code> 설정을 기다리도록 하기 때문입니다. 그럼에도 불구하고 애플리케이션이 신호를 보내기 전에 리포지토리가 적절하게 초기화되고 검증되었는지 확인합니다.</p>
<p><code>LAZY</code>는 시나리오 및 로컬 개발 테스트에 적합한 선택입니다. 리포지토리가 제대로 부트스트랩할 수 있다고 확신하거나 애플리케이션의 다른 부분을 테스트하는 경우 모든 리포지토리에 대해 확인을 실행하면 시작 시간이 불필요하게 늘어날 수 있습니다. 단일 리포지토리를 초기화해야 할 수 있는 애플리케이션 부분에만 액세스하는 로컬 개발에도 동일하게 적용됩니다.</p>