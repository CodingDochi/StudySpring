<h2 id="1-overview">1. Overview</h2>
<p>이 튜토리얼에서는 Mockito 라이브러리의 어노테이션인 <code>@Mock</code>, <code>@Spy</code>, <code>@Captor</code> 및 <code>@InjectMocks</code>를 다룹니다.</p>
<h2 id="2-enable-mockito-annotations">2. Enable Mockito Annotations</h2>
<p>Mockito 테스트를 사용할 수 있는 두가지 어노테이션</p>
<h3 id="21-mockitojunitrunner">2.1 MockitoJUnitRunner</h3>
<p>JUnit 테스트에 MockitoJUnitRunner로 어노테이션 달기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">MockitoExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MockitoAnnotationUnitTest</span> <span class="token punctuation">{</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="22-mockitoannotationsopenmocks">2.2 MockitoAnnotations.OpenMocks()</h3>
<p>MockitoAnnotations.openMocks()를 호출하여 프로그래밍 방식으로 Mockito 어노테이션 활성화 하기</p>
<pre><code class="language-java"><span class="token annotation punctuation">@BeforeEach</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">MockitoAnnotations</span><span class="token punctuation">.</span><span class="token function">openMocks</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="23">2,3</h3>
<p>MockitoJUnit.rule() 사용하기</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MockitoAnnotationsInitWithMockitoJUnitRuleUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Rule</span>
    <span class="token keyword">public</span> <span class="token class-name">MockitoRule</span> initRule <span class="token operator">=</span> <span class="token class-name">MockitoJUnit</span><span class="token punctuation">.</span><span class="token function">rule</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p>이 경우 rule을 public으로 생성하는 것을 유념해둡시다.</p>
<h2 id="3-mock-어노테이션">3. <code>@Mock</code> 어노테이션</h2>
<p>Mockito에서 가장 널리 사용되는 어노테이션은 <code>@Mock</code>입니다. <code>Mockito.mock</code>을 수동으로 호출할 필요 없이 <code>@Mock</code>을 사용하여 모의 인스턴스를 생성하고 주입할 수 있습니다.</p>
<p>다음 예에서는 <code>@Mock</code> 어노테이션을 사용하지 않고 수동으로 모의 <code>ArrayList</code>를 생성합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenNotUseMockAnnotation_thenCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">List</span> mockList <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">ArrayList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    mockList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">,</span> mockList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">,</span> mockList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 동일한 작업을 수행하지만 <code>@Mock</code> 어노테이션을 사용하여 모의 객체를 주입하겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Mock</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> mockedList<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUseMockAnnotation_thenMockIsInjected</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    mockedList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockedList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">,</span> mockedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">,</span> mockedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>두 예 모두에서 모의 객체가 올바르게 작동하는지 확인하기 위해 모의 개체와 상호 작용하고 이러한 상호 작용 중 일부를 확인하는 방법에 유의하세요.</p>
<h2 id="4-donotmock-어노테이션">4. <code>@DoNotMock</code> 어노테이션</h2>
<p><code>@DoNotMock</code> 어노테이션은 테스트 중에 특정 유형을 모의해서는 안됨을 나타내는 데 사용됩니다. 일반적으로 테스트 프레임워크와 개발자를 실제 인스턴스나 표준 가짜 사용과 같은 대체 테스트 솔루션으로 안내하기 위해 클래스 또는 인터페이스 수준에서 적용됩니다.</p>
<p><code>@DoNotMock</code> 어노테이션을 사용하는 방법의 예는 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>exceptions<span class="token punctuation">.</span>misusing</span><span class="token punctuation">.</span><span class="token class-name">DoNotMock</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@DoNotMock</span><span class="token punctuation">(</span>reason <span class="token operator">=</span> <span class="token string">"Use a real instance instead"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">NotToMock</span> <span class="token punctuation">{</span>
    <span class="token comment">// Class implementation</span>
<span class="token punctuation">}</span></code></pre>
<p>예를 들어, <code>NotToMock</code> 클래스의 컨텍스트에서 <code>@DoNotMock</code> 어노테이션은 "대신 실제 인스턴스 사용"과 같은 지정된 이유 속성과 함께 적용됩니다. 이 어노테이션은 테스트 중에 <code>NotToMock</code> 클래스를 모의해서는 안 된다는 의도를 명시적으로 전달하고 대신 실제 인스턴스의 사용을 권장합니다.</p>
<p>이유 속성이 있는 <code>@DoNotMock</code> 어노테이션은 테스트 프레임워크와 개발자가 모의에 의존하기보다는 대체 테스트 접근 방식을 고려하도록 명확하게 지시합니다.</p>
<h2 id="5-spy-어노테이션">5. <code>@Spy</code> 어노테이션</h2>
<p>이제 <code>@Spy</code> 어노테이션을 사용하여 기존 인스턴스를 감시하는 방법을 살펴보겠습니다. 다음 예에서는 <code>@Spy</code> 어노테이션을 사용하지 않고 <code>List</code>의 스파이를 만듭니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenNotUseSpyAnnotation_thenCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> spyList <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">spy</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    spyList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    spyList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"two"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>spyList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>spyList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"two"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> spyList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">doReturn</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>spyList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">,</span> spyList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 동일한 작업을 수행하여 리스트를 감시하지만 @Spy 어노테이션을 사용합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Spy</span>
<span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> spiedList <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ArrayList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUseSpyAnnotation_thenSpyIsInjectedCorrectly</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    spiedList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    spiedList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"two"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>spiedList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>spiedList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"two"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">,</span> spiedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">doReturn</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>spiedList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token number">100</span><span class="token punctuation">,</span> spiedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이전과 마찬가지로 여기에서 스파이가 올바르게 작동하는지 확인하기 위해 스파이와 어떻게 상호 작용하는지 확인하세요. 이 예에서는 다음을 수행합니다.</p>
<ul>
<li><code>spiedList</code>에 요소를 추가하기 위해 <strong>실제</strong> 메소드 <code>spiedList.add()</code>를 사용했습니다.</li>
<li><code>Mockito.doReturn()</code>을 사용하여 <code>2</code> 대신 <code>100</code>을 반환하도록 <code>spiedList.size()</code> 메서드를 <strong>스텁</strong>했습니다.</li>
</ul>
<h2 id="6-captor-어노테이션">6. <code>@Captor</code> 어노테이션</h2>
<p>다음으로 <code>@Captor</code> 어노테이션을 사용하여 <code>ArgumentCaptor</code> 인스턴스를 생성하는 방법을 살펴보겠습니다. 다음 예에서는 <code>@Captor</code> 어노테이션을 사용하지 않고 <code>ArgumentCaptor</code>를 생성합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenNotUseCaptorAnnotation_thenCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">List</span> mockList <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">List</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">ArgumentCaptor</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> arg <span class="token operator">=</span> <span class="token class-name">ArgumentCaptor</span><span class="token punctuation">.</span><span class="token function">forClass</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    mockList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>arg<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">,</span> arg<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 동일한 목적으로 <code>@Captor</code>를 사용하여 <code>ArgumentCaptor</code> 인스턴스를 생성해 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Mock</span>
<span class="token class-name">List</span> mockedList<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Captor</span> 
<span class="token class-name">ArgumentCaptor</span> argCaptor<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUseCaptorAnnotation_thenTheSame</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    mockedList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>mockedList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>argCaptor<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">,</span> argCaptor<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>구성(configuration) 논리를 제거하면 테스트가 어떻게 더 간단해지고 읽기 쉬워지는지 확인하세요.</p>
<h2 id="7-injectmocks-어노테이션">7. @InjectMocks 어노테이션</h2>
<p>이제 <code>@InjectMocks</code> 어노테이션을 사용하여 테스트된 객체에 모의 필드를 자동으로 삽입하는 방법을 논의해 보겠습니다. 다음 예에서는 <code>@InjectMocks</code>를 사용하여 모의 <code>wordMap</code>을 <code>MyDictionary dic</code>에 삽입합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Mock</span>
<span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> wordMap<span class="token punctuation">;</span>

<span class="token annotation punctuation">@InjectMocks</span>
<span class="token class-name">MyDictionary</span> dic <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MyDictionary</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUseInjectMocksAnnotation_thenCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>wordMap<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"aWord"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"aMeaning"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"aMeaning"</span><span class="token punctuation">,</span> dic<span class="token punctuation">.</span><span class="token function">getMeaning</span><span class="token punctuation">(</span><span class="token string">"aWord"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>다음은 <code>MyDictionary</code> 클래스입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyDictionary</span> <span class="token punctuation">{</span>
    <span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> wordMap<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">MyDictionary</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        wordMap <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HashMap</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">add</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">String</span> word<span class="token punctuation">,</span> <span class="token keyword">final</span> <span class="token class-name">String</span> meaning<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        wordMap<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>word<span class="token punctuation">,</span> meaning<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getMeaning</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token class-name">String</span> word<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> wordMap<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>word<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="8-mock을-spy에-주입하기">8. Mock을 Spy에 주입하기</h2>
<p>위 테스트와 유사하게 스파이에 모의 객체를 주입할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Mock</span>
<span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> wordMap<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Spy</span>
<span class="token class-name">MyDictionary</span> spyDic <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MyDictionary</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>그러나 Mockito는 모의 객체를 스파이에 주입하는 것을 지원하지 않으며 다음 테스트 결과는 예외입니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span> 
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenUseInjectMocksAnnotation_thenCorrect</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> 
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>wordMap<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token string">"aWord"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"aMeaning"</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"aMeaning"</span><span class="token punctuation">,</span> spyDic<span class="token punctuation">.</span><span class="token function">getMeaning</span><span class="token punctuation">(</span><span class="token string">"aWord"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
<span class="token punctuation">}</span></code></pre>
<p>스파이와 함께 모의 개체를 사용하려면 생성자를 통해 모의 개체를 수동으로 주입할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">MyDictionary</span><span class="token punctuation">(</span><span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> wordMap<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">this</span><span class="token punctuation">.</span>wordMap <span class="token operator">=</span> wordMap<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>주석을 사용하는 대신 이제 수동으로 스파이를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Mock</span>
<span class="token class-name">Map</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">,</span> <span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> wordMap<span class="token punctuation">;</span> 

<span class="token class-name">MyDictionary</span> spyDic<span class="token punctuation">;</span>

@<span class="token annotation punctuation">@BeforeEach</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">MockitoAnnotations</span><span class="token punctuation">.</span><span class="token function">openMocks</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    spyDic <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">spy</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyDictionary</span><span class="token punctuation">(</span>wordMap<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 테스트가 통과됩니다.</p>
<h2 id="9-running-into-npe-while-using-annotation">9. Running Into NPE While Using Annotation</h2>
<p><code>@Mock</code> 또는 <code>@Spy</code> 어노테이션이 달린 인스턴스를 실제로 사용하려고 할 때 종종 <code>NullPointerException</code>이 발생할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MockitoAnnotationsUninitializedUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token class-name">List</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> mockedList<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span><span class="token punctuation">(</span>expected <span class="token operator">=</span> <span class="token class-name">NullPointerException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenMockitoAnnotationsUninitialized_thenNPEThrown</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockedList<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>대부분의 경우 이는 Mockito 어노테이션을 올바르게 활성화하는 것을 잊었기 때문에 발생합니다.</p>
<p>따라서 Mockito 어노테이션을 사용할 때마다 앞서 설명한 대로 추가 단계를 수행하고 초기화해야 한다는 점을 명심해야 합니다.</p>
<h2 id="10-notes">10. Notes</h2>
<p>마지막으로 Mockito 어노테이션에 대한 몇 가지 참고 사항은 다음과 같습니다.</p>
<ul>
<li>Mockito의 어노테이션은 반복적인 모의 생성 코드를 최소화합니다.</li>
<li>테스트를 더 읽기 쉽게 만듭니다.</li>
<li><code>@Spy</code> 및 <code>@Mock</code> 인스턴스를 모두 주입하려면 <code>@InjectMocks</code>가 필요합니다.</li>
</ul>