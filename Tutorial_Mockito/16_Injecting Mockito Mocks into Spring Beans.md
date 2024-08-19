<h2 id="1-overview">1. Overview</h2>
<p>이 튜토리얼에서는 단위 테스트를 위해 Spring Bean에 Mockito 모의 객체를 삽입하기 위해 종속성 주입을 사용하는 방법에 대해 설명합니다.</p>
<p>구성 요소가 외부 시스템 액세스에 의존하는 경우가 많은 실제 애플리케이션에서는 적절한 테스트 격리를 제공하여 각 테스트에 전체 클래스 계층 구조를 포함하지 않고도 특정 장치의 기능을 테스트하는 데 집중할 수 있도록 하는 것이 중요합니다.</p>
<p>모의 객체를 주입하는 것은 이러한 격리를 도입하는 깔끔한 방법입니다.</p>
<h2 id="2-maven-dependencies">2. Maven Dependencies</h2>
<p>단위 테스트와 모의 객체에는 다음과 같은 Maven 종속성이 필요합니다.</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.boot<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-boot-starter<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.boot<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-boot-starter-test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.mockito<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>mockito-core<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.21.0<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>이 예에서는 Spring Boot를 사용하기로 결정했지만 클래식 Spring도 잘 작동합니다.</p>
<h2 id="3-writing-the-test">3. Writing the Test</h2>
<h3 id="31-the-business-logic">3.1 The Business Logic</h3>
<p>먼저 테스트할 간단한 서비스를 만들어 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameService</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getUserName</span><span class="token punctuation">(</span><span class="token class-name">String</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"Real user name"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그런 다음 이를 <code>UserService</code> 클래스에 삽입합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserService</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">NameService</span> nameService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">public</span> <span class="token class-name">UserService</span><span class="token punctuation">(</span><span class="token class-name">NameService</span> nameService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>nameService <span class="token operator">=</span> nameService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getUserName</span><span class="token punctuation">(</span><span class="token class-name">String</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> nameService<span class="token punctuation">.</span><span class="token function">getUserName</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 문서의 경우 제공된 클래스는 제공된 ID에 관계없이 단일 이름을 반환합니다. 이는 복잡한 논리를 테스트하는 데 주의가 산만해지지 않도록 하기 위한 것입니다.</p>
<p>또한 빈을 스캔하고 애플리케이션을 초기화하려면 표준 Spring Boot 메인 클래스가 필요합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@SpringBootApplication</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MocksApplication</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">SpringApplication</span><span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span><span class="token class-name">MocksApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> args<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="32-the-tests">3.2 The Tests</h3>
<p>이제는 노력해야 합니다. 우선, 테스트를 위해 필요한 사항을 구성해야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Profile</span><span class="token punctuation">(</span><span class="token string">"test"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@Configuration</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">NameServiceTestConfiguration</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Bean</span>
    <span class="token annotation punctuation">@Primary</span>
    <span class="token keyword">public</span> <span class="token class-name">NameService</span> <span class="token function">nameService</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">NameService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@Profile</code> 어노테이션은 "test" 프로필이 활성화된 경우에만 이 구성을 적용하도록 Spring에 지시합니다. <code>@Primary</code> 어노테이션은 autowiring을 위해 실제 인스턴스 대신 이 인스턴스가 사용되는지 확인하기 위해 존재합니다. 메소드 자체는 <code>NameService</code> 클래스의 Mockito 모형을 생성하고 반환합니다.</p>
<p>이제 단위 테스트를 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ActiveProfiles</span><span class="token punctuation">(</span><span class="token string">"test"</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">SpringJUnit4ClassRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@SpringApplicationConfiguration</span><span class="token punctuation">(</span>classes <span class="token operator">=</span> <span class="token class-name">MocksApplication</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserServiceUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">UserService</span> userService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">NameService</span> nameService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUserIdIsProvided_thenRetrievedNameIsCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>nameService<span class="token punctuation">.</span><span class="token function">getUserName</span><span class="token punctuation">(</span><span class="token string">"SomeId"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"Mock user name"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">String</span> testName <span class="token operator">=</span> userService<span class="token punctuation">.</span><span class="token function">getUserName</span><span class="token punctuation">(</span><span class="token string">"SomeId"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"Mock user name"</span><span class="token punctuation">,</span> testName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>@ActiveProfiles</code> 어노테이션을 사용하여 "test" 프로필을 활성화하고 이전에 작성한 모의 구성을 활성화합니다. 결과적으로 Spring은 <code>UserService</code> 클래스의 실제 인스턴스를 자동 연결하지만 <code>NameService</code> 클래스의 모의 인스턴스를 자동 연결합니다. 테스트 자체는 상당히 일반적인 JUnit+Mockito 테스트입니다. 우리는 원하는 모의 동작을 구성한 다음 테스트하려는 메서드를 호출하고 우리가 기대하는 값을 반환한다고 주장합니다.</p>
<p>이러한 테스트에서 환경 프로필을 사용하지 않는 것도 가능합니다(권장되지는 않지만). 이를 위해 <code>@Profile</code> 및 <code>@ActiveProfiles</code> 어노테이션을 제거하고 <code>UserServiceTest</code> 클래스에 <code>@ContextConfiguration(classes = NameServiceTestConfiguration.class)</code> 어노테이션을 추가합니다.</p>