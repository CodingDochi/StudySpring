<h2 id="1-overview">1. Overview</h2>
<p>이 빠른 튜토리얼에서는 Mockito와 Spring 모의 지원을 사용하여 모의 객체를 생성하는 세 가지 방법을 살펴보겠습니다. 또한 서로 어떻게 다른지 논의하겠습니다.</p>
<h2 id="2-mockitomock">2. Mockito.mock()</h2>
<p><code>Mockito.mock()</code> 메소드를 사용하면 클래스나 인터페이스의 모의 객체를 생성할 수 있습니다. 그런 다음 모의 객체를 사용하여 메서드에 대한 반환 값을 스텁하고 호출되었는지 확인할 수 있습니다. 예를 살펴보겠습니다:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenCountMethodMocked_WhenCountInvoked_ThenMockedValueReturned</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserRepository</span> localMockRepository <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">UserRepository</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>localMockRepository<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">111L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">long</span> userCount <span class="token operator">=</span> localMockRepository<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">111L</span><span class="token punctuation">,</span> userCount<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>localMockRepository<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메서드를 사용하기 전에 다른 작업을 수행할 필요는 없습니다. 이를 사용하여 모의 클래스 필드와 메서드의 로컬 모의를 생성할 수 있습니다.</p>
<h2 id="3-mockitos-mock-annotation">3. Mockito's @Mock Annotation</h2>
<p>이 어노테이션은 <code>Mockito.mock()</code> 메서드의 약어입니다. 테스트 클래스에서만 사용해야 한다는 점을 기억하는 것이 중요합니다. <code>mock()</code> 메소드와 달리 이 어노테이션을 사용하려면 Mockito 어노테이션을 활성화해야 합니다.</p>
<p><code>MockitoJUnitRunner</code>를 사용하여 테스트를 실행하거나 <code>MockitoAnnotations.initMocks()</code> 메서드를 명시적으로 호출하여 이를 수행할 수 있습니다.</p>
<p><code>MockitoJUnitRunner</code>를 사용하는 예를 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">MockitoJUnitRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MockAnnotationUnitTest</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Mock</span>
    <span class="token class-name">UserRepository</span> mockRepository<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenCountMethodMocked_WhenCountInvoked_ThenMockValueReturned</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockRepository<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">123L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token keyword">long</span> userCount <span class="token operator">=</span> mockRepository<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">123L</span><span class="token punctuation">,</span> userCount<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockRepository<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>코드를 더 읽기 쉽게 만드는 것 외에도 <code>@Mock</code>을 사용하면 실패 메시지에 필드 이름이 표시되므로 실패 시 모의 문제를 더 쉽게 찾을 수 있습니다.</p>
<pre><code class="language-shell">Wanted but not invoked:
mockRepository.count<span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
-<span class="token operator">&gt;</span> at org.baeldung.MockAnnotationTest.testMockAnnotation<span class="token punctuation">(</span>MockAnnotationTest.java:22<span class="token punctuation">)</span>
Actually, there were zero interactions with this mock.

  at org.baeldung.MockAnnotationTest.testMockAnnotation<span class="token punctuation">(</span>MockAnnotationTest.java:22<span class="token punctuation">)</span></code></pre>
<p>또한 <code>@InjectMocks</code>와 함께 사용하면 설정 코드의 양을 크게 줄일 수 있습니다.</p>
<h2 id="4-spring-boots-mockbean-annotation">4. Spring Boot’s <code>@MockBean</code> Annotation</h2>
<p><code>@MockBean</code>을 사용하여 Spring 애플리케이션 컨텍스트에 모의 객체를 추가할 수 있습니다. 모의 객체는 애플리케이션 컨텍스트에서 동일한 유형의 기존 Bean을 대체합니다.</p>
<p>동일한 유형의 Bean이 정의되지 않은 경우 새 Bean이 추가됩니다. 이 어노테이션은 외부 서비스와 같은 특정 Bean을 조롱해야 하는 통합 테스트에 유용합니다.</p>
<p>이 어노테이션을 사용하려면 <code>SpringRunner</code>를 사용하여 테스트를 실행해야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">SpringRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MockBeanAnnotationIntegrationTest</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@MockBean</span>
    <span class="token class-name">UserRepository</span> mockRepository<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token class-name">ApplicationContext</span> context<span class="token punctuation">;</span>
    
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenCountMethodMocked_WhenCountInvoked_ThenMockValueReturned</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockRepository<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">123L</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">UserRepository</span> userRepoFromContext <span class="token operator">=</span> context<span class="token punctuation">.</span><span class="token function">getBean</span><span class="token punctuation">(</span><span class="token class-name">UserRepository</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">long</span> userCount <span class="token operator">=</span> userRepoFromContext<span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">123L</span><span class="token punctuation">,</span> userCount<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockRepository<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">count</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>필드에 주석을 사용하면 모의 객체가 필드에 주입되고 애플리케이션 컨텍스트에 등록됩니다.</p>
<p>이는 위의 코드에서 분명하게 드러납니다. 여기서 우리는 주입된 <code>UserRepository</code> 모의를 사용하여 <code>count</code> 메소드를 스텁했습니다. 그런 다음 애플리케이션 컨텍스트의 Bean을 사용하여 실제로 mock된 Bean인지 확인했습니다.</p>