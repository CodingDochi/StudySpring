<h2 id="1-overview">1. Overview</h2>
<p>이 튜토리얼에서는 비추상 메소드를 사용하여 추상 클래스의 단위 테스트에 대한 다양한 사용 사례와 가능한 대체 솔루션을 분석합니다.</p>
<p>추상 클래스 테스트는 거의 항상 구체적인 구현의 공개 API를 거쳐야 하므로 수행 중인 작업이 확실하지 않은 경우 아래 기술을 적용하지 마십시오.</p>
<h2 id="2-maven-dependencies">2. Maven Dependencies</h2>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.junit.jupiter<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>junit-jupiter-engine<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>5.9.2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.mockito<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>mockito-core<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.8.9<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.powermock<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>powermock-module-junit4<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>1.7.4<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>exclusions</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>exclusion</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>junit<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
            <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>junit<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>exclusion</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>exclusions</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.powermock<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>powermock-api-mockito2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.0.9<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span></code></pre>
<h2 id="3-independent-non-abstract-method">3. Independent Non-Abstract Method</h2>
<p>public non-abstract 메소드가 있는 추상 클래스가 있는 경우를 고려해 보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractIndependent</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">int</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token string">"DEFAULT-1"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>우리는 <code>defaultImpl()</code> 메소드를 테스트하고 싶고 구체적인 클래스를 사용하거나 Mockito를 사용하는 두 가지 가능한 솔루션이 있습니다.</p>
<h3 id="31-using-a-concrete-class">3.1 Using a Concrete Class</h3>
<p><code>AbstractIndependent</code> 클래스를 확장하는 구체적인 클래스를 만들고 이를 사용하여 메서드를 테스트합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ConcreteImpl</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractIndependent</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token number">4</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenNonAbstractMethod_whenConcreteImpl_testCorrectBehaviour</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">ConcreteImpl</span> conClass <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ConcreteImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">String</span> actual <span class="token operator">=</span> conClass<span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"DEFAULT-1"</span><span class="token punctuation">,</span> actual<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 솔루션의 단점은 모든 추상 메서드의 더미 구현을 사용하여 구체적인 클래스를 만들어야 한다는 것입니다.</p>
<h3 id="32-using-mockito">3.2 Using Mockito</h3>
<p><code>Mockito</code>를 사용하여 모의 모형을 만들 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenNonAbstractMethod_whenMockitoMock_testCorrectBehaviour</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">AbstractIndependent</span> absCls <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span>
      <span class="token class-name">AbstractIndependent</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> 
      <span class="token class-name">Mockito</span><span class="token punctuation">.</span>CALLS_REAL_METHODS<span class="token punctuation">)</span><span class="token punctuation">;</span>
 
    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"DEFAULT-1"</span><span class="token punctuation">,</span> absCls<span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 가장 중요한 부분은 <code>Mockito.CALLS_REAL_METHODS</code>를 사용하여 메서드가 호출될 때 실제 코드를 사용하기 위한 모의 코드를 준비하는 것입니다.</p>
<h2 id="4-abstract-method-called-from-non-abstract-method">4. Abstract Method Called From Non-Abstract Method</h2>
<p>이 경우 비추상 메서드는 전역 실행 흐름을 정의하는 반면 추상 메서드는 사용 사례에 따라 다른 방식으로 작성될 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractMethodCalling</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token class-name">String</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">String</span> res <span class="token operator">=</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token punctuation">(</span>res <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span> <span class="token operator">?</span> <span class="token string">"Default"</span> <span class="token operator">:</span> <span class="token punctuation">(</span>res <span class="token operator">+</span> <span class="token string">" Default"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 코드를 테스트하기 위해 이전과 동일한 두 가지 접근 방식을 사용할 수 있습니다. 즉, 구체적인 클래스를 생성하거나 Mockito를 사용하여 모의 클래스를 생성하는 것입니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenDefaultImpl_whenMockAbstractFunc_thenExpectedBehaviour</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">AbstractMethodCalling</span> cls <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">AbstractMethodCalling</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>cls<span class="token punctuation">.</span><span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"Abstract"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">doCallRealMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>cls<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"Abstract Default"</span><span class="token punctuation">,</span> cls<span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 <code>abstractFunc()</code>는 테스트에서 선호하는 반환 값으로 스텁됩니다. 이는 비추상 메소드인 <code>defaultImpl()</code>을 호출할 때 이 스텁을 사용한다는 것을 의미합니다.</p>
<h2 id="5-non-abstract-method-with-test-obstruction">5. Non-Abstract Method With Test Obstruction</h2>
<p>일부 시나리오에서는 테스트하려는 메서드가 테스트 방해물이 포함된 전용 메서드를 호출합니다. 타겟 메서드를 테스트하기 전에 방해하는 테스트 메서드를 우회해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractPrivateMethods</span> <span class="token punctuation">{</span>

    <span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">int</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token function">getCurrentDateTime</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token string">"DEFAULT-1"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">private</span> <span class="token class-name">String</span> <span class="token function">getCurrentDateTime</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token class-name">LocalDateTime</span><span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예에서 <code>defaultImpl()</code> 메소드는 개인 메소드 <code>getCurrentDateTime()</code>을 호출합니다. 이 비공개 메서드는 런타임 시 현재 시간을 가져오며, 이는 단위 테스트에서 피해야 합니다.</p>
<p>이제 이 프라이빗 메서드의 표준 동작을 조롱하기 위해 <code>Mockito</code>를 사용할 수도 없습니다. 프라이빗 메서드를 제어할 수 없기 때문입니다.</p>
<p>대신 PowerMock을 사용해야 합니다(이 종속성에 대한 지원은 JUnit 5에서는 지원되지 않으므로 이 예제는 JUnit 4에서만 작동합니다).</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">PowerMockRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@PrepareForTest</span><span class="token punctuation">(</span><span class="token class-name">AbstractPrivateMethods</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">AbstractPrivateMethodsUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenMockPrivateMethod_thenVerifyBehaviour</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">AbstractPrivateMethods</span> mockClass <span class="token operator">=</span> <span class="token class-name">PowerMockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">AbstractPrivateMethods</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">PowerMockito</span><span class="token punctuation">.</span><span class="token function">doCallRealMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockClass<span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">String</span> dateTime <span class="token operator">=</span> <span class="token class-name">LocalDateTime</span><span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">PowerMockito</span><span class="token punctuation">.</span><span class="token function">doReturn</span><span class="token punctuation">(</span>dateTime<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>mockClass<span class="token punctuation">,</span> <span class="token string">"getCurrentDateTime"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">String</span> actual <span class="token operator">=</span> mockClass<span class="token punctuation">.</span><span class="token function">defaultImpl</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">assertEquals</span><span class="token punctuation">(</span>dateTime <span class="token operator">+</span> <span class="token string">"DEFAULT-1"</span><span class="token punctuation">,</span> actual<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예에서 중요한 부분은 다음과 같습니다.</p>
<ul>
<li><code>@RunWith</code>는 PowerMock을 테스트 실행자로 정의합니다.</li>
<li><code>@PrepareForTest(class)</code>는 PowerMock에게 나중에 처리할 클래스를 준비하라고 지시합니다.</li>
</ul>
<p>흥미롭게도 우리는 <code>PowerMock</code>에게 private 메소드 <code>getCurrentDateTime()</code>을 스텁하도록 요청하고 있습니다. PowerMock은 외부에서 접근할 수 없기 때문에 리플렉션을 사용하여 이를 찾습니다.</p>
<p>따라서 <code>defaultImpl()</code>을 호출하면 실제 메소드 대신 private 메소드용으로 생성된 스텁이 호출됩니다.</p>
<h2 id="6-non-abstract-method-which-accesses-instance-fields">6. Non-Abstract Method Which Accesses Instance Fields</h2>
<p>추상 클래스는 클래스 필드로 구현된 내부 상태를 가질 수 있습니다. 필드의 값은 테스트되는 메서드에 중요한 영향을 미칠 수 있습니다. 필드가 public이거나 protected인 경우 테스트 메서드에서 쉽게 액세스할 수 있습니다. 하지만 private라면 <code>PowerMockit</code>o를 사용해야 합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">class</span> <span class="token class-name">AbstractInstanceFields</span> <span class="token punctuation">{</span>
    <span class="token keyword">protected</span> <span class="token keyword">int</span> count<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token keyword">boolean</span> active <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">abstract</span> <span class="token keyword">int</span> <span class="token function">abstractFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">testFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>count <span class="token operator">&gt;</span> <span class="token number">5</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token string">"Overflow"</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> 
        <span class="token keyword">return</span> active <span class="token operator">?</span> <span class="token string">"Added"</span> <span class="token operator">:</span> <span class="token string">"Blocked"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>여기서 <code>testFunc()</code> 메서드는 반환되기 전에 인스턴스 수준 필드 <code>count</code> 및 <code>active</code>를 사용합니다.</p>
<p><code>testFunc()</code>를 테스트할 때 <code>Mockito</code>를 사용하여 생성된 인스턴스에 액세스하여 <code>count</code> 필드의 값을 변경할 수 있습니다.</p>
<p>반면에 private <code>active</code> 필드의 동작을 테스트하려면 <code>PowerMockito</code>와 해당 Whitebox 클래스를 다시 사용해야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenPowerMockitoAndActiveFieldTrue_thenCorrectBehaviour</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">AbstractInstanceFields</span> instClass <span class="token operator">=</span> <span class="token class-name">PowerMockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">AbstractInstanceFields</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">PowerMockito</span><span class="token punctuation">.</span><span class="token function">doCallRealMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>instClass<span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">testFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Whitebox</span><span class="token punctuation">.</span><span class="token function">setInternalState</span><span class="token punctuation">(</span>instClass<span class="token punctuation">,</span> <span class="token string">"active"</span><span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"Added"</span><span class="token punctuation">,</span> instClass<span class="token punctuation">.</span><span class="token function">testFunc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>PowerMockito.mock()</code>을 사용하여 스텁 클래스를 생성하고 있으며 <code>Whitebox</code> 클래스를 사용하여 객체의 내부 상태를 제어하고 있습니다. 활성 필드의 값이 <code>true</code>로 변경됩니다.</p>