<h2 id="1-overview">1. Overview</h2>
<p>Java 8에는 람다 및 스트림과 같은 새롭고 멋진 기능이 많이 도입되었습니다. 그리고 당연히 Mockito는 두 번째 주요 버전에서 이러한 최근 혁신을 활용했습니다.</p>
<p>이 글에서는 이 강력한 조합이 제공하는 모든 것을 살펴보겠습니다.</p>
<h2 id="2-mocking-interface-with-a-default-method">2. Mocking Interface With a Default Method</h2>
<p>Java 8부터는 이제 인터페이스에서 메소드 구현을 작성할 수 있습니다. 이것은 훌륭한 새로운 기능일 수 있지만 언어에 대한 도입은 개념부터 Java의 일부였던 강력한 개념을 위반했습니다.</p>
<p>Mockito 버전 1은 이 변경 사항을 수용할 준비가 되어 있지 않았습니다. 기본적으로 인터페이스에서 실제 메서드를 호출하도록 요청할 수 없었기 때문입니다.</p>
<p>2개의 메서드 선언이 있는 인터페이스가 있다고 상상해 보세요. 첫 번째는 우리 모두에게 익숙한 구식 메서드 서명이고, 다른 하나는 완전히 새로운 <code>default</code> 메서드입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">JobService</span> <span class="token punctuation">{</span>
 
    <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">JobPosition</span><span class="token punctuation">&gt;</span></span> <span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token keyword">default</span> <span class="token keyword">boolean</span> <span class="token function">assignJobPosition</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">,</span> <span class="token class-name">JobPosition</span> jobPosition<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span><span class="token punctuation">(</span><span class="token operator">!</span><span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isPresent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            person<span class="token punctuation">.</span><span class="token function">setCurrentJobPosition</span><span class="token punctuation">(</span>jobPosition<span class="token punctuation">)</span><span class="token punctuation">;</span>
            
            <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>assignJobPosition()</code> 기본 메서드에는 구현되지 않은 <code>findCurrentJobPosition()</code> 메서드에 대한 호출이 있습니다.</p>
<p>이제 <code>findCurrentJobPosition()</code>의 실제 구현을 작성하지 않고 <code>assignJobPosition()</code> 구현을 테스트한다고 가정해 보겠습니다. <code>JobService</code>의 모의 버전을 만든 다음 Mockito에게 구현되지 않은 메서드에 대한 호출에서 알려진 값을 반환하고 <code>assignJobPosition()</code>이 호출될 때 실제 메서드를 호출하도록 지시할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">JobServiceUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">JobService</span> jobService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenDefaultMethod_whenCallRealMethod_thenNoExceptionIsRaised</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">)</span>
              <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">Optional</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">JobPosition</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">doCallRealMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">assignJobPosition</span><span class="token punctuation">(</span>
            <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">,</span> 
            <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">JobPosition</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
        <span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">assertFalse</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">assignJobPosition</span><span class="token punctuation">(</span>person<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">JobPosition</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이는 매우 합리적이며 인터페이스 대신 추상 클래스를 사용하고 있다면 제대로 작동할 것입니다.</p>
<p>그러나 Mockito 버전 1의 내부 작동은 이 구조에 적합하지 않았습니다. Mockito 이전 버전 2에서 이 코드를 실행하면 다음과 같은 잘 설명된 오류가 발생합니다.</p>
<pre><code class="language-shell">org.mockito.exceptions.base.MockitoException:
Cannot call a real method on java interface. The interface does not have any implementation<span class="token operator">!</span>
Calling real methods is only possible when mocking concrete classes.</code></pre>
<p>Mockito는 작업을 수행하고 있으며 이 작업은 Java 8 이전에는 생각할 수 없었기 때문에 인터페이스에서 실제 메서드를 호출할 수 없다고 말합니다.</p>
<p>좋은 소식은 우리가 사용하고 있는 Mockito 버전을 변경하는 것만으로도 이 오류를 없앨 수 있다는 것입니다. 예를 들어 Maven을 사용하면 버전 2.7.5를 사용할 수 있습니다(최신 Mockito 버전은 여기에서 찾을 수 있습니다).</p>
<pre><code class="language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.mockito<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>mockito-core<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.7.5<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>scope</span><span class="token punctuation">&gt;</span></span>test<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>scope</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span></code></pre>
<p>코드를 변경할 필요가 없습니다. 다음에 테스트를 실행하면 오류가 더 이상 발생하지 않습니다.</p>
<h2 id="3-return-default-values-for-optional-and-stream">3. Return Default Values for <code>Optional</code> and <code>Stream</code></h2>
<p><code>Optional</code> 및 <code>Stream</code>은 Java 8에 새롭게 추가된 기능입니다. 두 클래스 사이의 한 가지 유사점은 둘 다 빈 객체를 나타내는 특별한 유형의 값을 가지고 있다는 것입니다. 이 빈 객체를 사용하면 지금까지 널리 존재하는 <code>NullPointerException</code>을 더 쉽게 피할 수 있습니다.</p>
<h3 id="31-example-with-optional">3.1 Example With <code>Optional</code></h3>
<p>이전 섹션에서 설명한 <code>JobService</code>를 주입하고 <code>JobService#findCurrentJobPosition()</code>을 호출하는 메서드가 있는 서비스를 생각해 보세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImpl</span> <span class="token keyword">implements</span> <span class="token class-name">UnemploymentService</span> <span class="token punctuation">{</span>
 
    <span class="token keyword">private</span> <span class="token class-name">JobService</span> jobService<span class="token punctuation">;</span>
    
    <span class="token keyword">public</span> <span class="token class-name">UnemploymentServiceImpl</span><span class="token punctuation">(</span><span class="token class-name">JobService</span> jobService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>jobService <span class="token operator">=</span> jobService<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">personIsEntitledToUnemploymentSupport</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">JobPosition</span><span class="token punctuation">&gt;</span></span> optional <span class="token operator">=</span> jobService<span class="token punctuation">.</span><span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token keyword">return</span> <span class="token operator">!</span>optional<span class="token punctuation">.</span><span class="token function">isPresent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 개인에게 현재 직위가 없을 때 실업 지원을 받을 자격이 있는지 확인하는 테스트를 만들고 싶다고 가정합니다.</p>
<p>이 경우 <code>findCurrentJobPosition()</code>이 빈 <code>Optional</code>을 반환하도록 강제합니다. Mockito 버전 2 이전에는 해당 메서드에 대한 호출을 모의해야 했습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImplUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">JobService</span> jobService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@InjectMocks</span>
    <span class="token keyword">private</span> <span class="token class-name">UnemploymentServiceImpl</span> unemploymentService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenReturnIsOfTypeOptional_whenMocked_thenValueIsEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">Optional</span><span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token function">assertTrue</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">personIsEntitledToUnemploymentSupport</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>모의 객체에 대한 모든 메소드 호출에 대한 Mockito의 기본 반환 값이 <code>null</code>이기 때문에 13행의 <code>when(...).thenReturn(...)</code> 명령이 필요합니다. 버전 2에서는 해당 동작이 변경되었습니다.</p>
<p><code>Optional</code>을 처리할 때 null 값을 거의 처리하지 않기 때문에 Mockito는 이제 기본적으로 빈 <code>Optional</code>을 반환합니다. 이는 <code>Optional.empty()</code> 호출의 반환과 정확히 동일한 값입니다.</p>
<p>따라서 Mockito 버전 2를 사용할 때 라인 13을 제거해도 테스트는 여전히 성공할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImplUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenReturnIsOptional_whenDefaultValueIsReturned_thenValueIsEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
        <span class="token function">assertTrue</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">personIsEntitledToUnemploymentSupport</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="32-example-with-stream">3.2 Example With <code>Stream</code></h3>
<p><code>Stream</code>을 반환하는 메서드를 모의할 때도 동일한 동작이 발생합니다. 개인이 근무한 모든 직무를 나타내는 스트림을 반환하는 <code>JobService</code> 인터페이스에 새로운 메소드를 추가해 보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">JobService</span> <span class="token punctuation">{</span>
    <span class="token class-name">Stream</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">JobPosition</span><span class="token punctuation">&gt;</span></span> <span class="token function">listJobs</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 메소드는 특정 검색 문자열과 일치하는 직업에 대해 일한 적이 있는지 쿼리하는 또 다른 새로운 메소드에 사용됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImpl</span> <span class="token keyword">implements</span> <span class="token class-name">UnemploymentService</span> <span class="token punctuation">{</span>
   
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">Optional</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">JobPosition</span><span class="token punctuation">&gt;</span></span> <span class="token function">searchJob</span><span class="token punctuation">(</span><span class="token class-name">Person</span> person<span class="token punctuation">,</span> <span class="token class-name">String</span> searchString<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> jobService<span class="token punctuation">.</span><span class="token function">listJobs</span><span class="token punctuation">(</span>person<span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span><span class="token punctuation">(</span>j<span class="token punctuation">)</span> <span class="token operator">-&gt;</span> j<span class="token punctuation">.</span><span class="token function">getTitle</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>searchString<span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">findFirst</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>따라서 <code>listJobs()</code> 작성에 대해 걱정할 필요 없이 <code>searchJob()</code> 구현을 적절하게 테스트하고 그 사람이 아직 어떤 직업에서도 일하지 않았을 때 시나리오를 테스트한다고 가정합니다. 이 경우 <code>listJobs()</code>가 빈 <code>Stream</code>을 반환하기를 원할 것입니다.</p>
<p>Mockito 버전 2 이전에는 이러한 테스트를 작성하려면 <code>listJobs()</code> 호출을 모의해야 했습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImplUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenReturnIsOfTypeStream_whenMocked_thenValueIsEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">listJobs</span><span class="token punctuation">(</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">Stream</span><span class="token punctuation">.</span><span class="token function">empty</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token function">assertFalse</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">searchJob</span><span class="token punctuation">(</span>person<span class="token punctuation">,</span> <span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isPresent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>버전 2로 업그레이드하면 <code>when(…).thenReturn(…)</code> 호출을 삭제할 수 있습니다. 이제 Mockito는 기본적으로 모의 메서드에서 빈 <code>Stream</code>을 반환하기 때문입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UnemploymentServiceImplUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenReturnIsStream_whenDefaultValueIsReturned_thenValueIsEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> person <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token function">assertFalse</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">searchJob</span><span class="token punctuation">(</span>person<span class="token punctuation">,</span> <span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isPresent</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="4-leveraging-lambda-expressions">4. Leveraging Lambda Expressions</h2>
<p>Java 8의 람다 표현식을 사용하면 명령문을 훨씬 더 간결하고 읽기 쉽게 만들 수 있습니다. Mockito로 작업할 때 람다 표현식이 가져온 단순성의 두 가지 좋은 예는 <code>ArgumentMatchers</code>와 사용자 정의 <code>Answers</code>입니다.</p>
<h3 id="41-combination-of-lambda-and-argumentmatcher">4.1 Combination of Lambda and <code>ArgumentMatcher</code></h3>
<p>Java 8 이전에는 ArgumentMatcher를 구현하는 클래스를 만들고 match() 메서드에 사용자 정의 규칙을 작성해야 했습니다. Java 8에서는 내부 클래스를 간단한 람다 표현식으로 바꿀 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ArgumentMatcherWithLambdaUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenPersonWithJob_thenIsNotEntitled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Person</span> peter <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token string">"Peter"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Person</span> linda <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Person</span><span class="token punctuation">(</span><span class="token string">"Linda"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token class-name">JobPosition</span> teacher <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">JobPosition</span><span class="token punctuation">(</span><span class="token string">"Teacher"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">findCurrentJobPosition</span><span class="token punctuation">(</span>
          <span class="token class-name">ArgumentMatchers</span><span class="token punctuation">.</span><span class="token function">argThat</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> p<span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span><span class="token string">"Peter"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">Optional</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span>teacher<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
        <span class="token function">assertTrue</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">personIsEntitledToUnemploymentSupport</span><span class="token punctuation">(</span>linda<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertFalse</span><span class="token punctuation">(</span>unemploymentService<span class="token punctuation">.</span><span class="token function">personIsEntitledToUnemploymentSupport</span><span class="token punctuation">(</span>peter<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="combination-of-lambda-and-custom-answer">Combination of Lambda and Custom <code>Answer</code></h3>
<p>람다 표현식과 Mockito의 <code>Answer</code>를 결합하면 동일한 효과를 얻을 수 있습니다.</p>
<p>예를 들어, <code>Person</code>의 이름이 "Peter"인 경우 단일 <code>JobPosition</code>을 포함하는 <code>Stream</code>을 반환하고 그렇지 않은 경우 빈 <code>Stream</code>을 반환하도록 <code>listJobs()</code> 메서드에 대한 호출을 시뮬레이션하려면 다음을 생성해야 합니다. <code>Answer</code> 인터페이스를 구현한 클래스(익명 또는 내부)입니다.</p>
<p>다시 말하지만, 람다 표현식을 사용하면 모든 모의 동작을 인라인으로 작성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">CustomAnswerWithLambdaUnitTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Before</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">when</span><span class="token punctuation">(</span>jobService<span class="token punctuation">.</span><span class="token function">listJobs</span><span class="token punctuation">(</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span><span class="token punctuation">(</span>i<span class="token punctuation">)</span> <span class="token operator">-&gt;</span>
          <span class="token class-name">Stream</span><span class="token punctuation">.</span><span class="token function">of</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">JobPosition</span><span class="token punctuation">(</span><span class="token string">"Teacher"</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>p <span class="token operator">-&gt;</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">Person</span><span class="token punctuation">)</span> i<span class="token punctuation">.</span><span class="token function">getArgument</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getName</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span><span class="token string">"Peter"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>위의 구현에서는 <code>PersonAnswer</code> 내부 클래스가 필요하지 않습니다.</p>