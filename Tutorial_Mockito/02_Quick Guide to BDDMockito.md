<h2 id="1-overview">1. Overview</h2>
<p>BDD라는 용어는 2006년 Dan North에 의해 처음 만들어졌습니다.</p>
<p>BDD는 애플리케이션의 동작(behavior)에 초점을 맞춘 자연스럽고 사람이 읽을 수 있는 언어로 테스트를 작성하도록 권장합니다.</p>
<p>이는 세 가지 섹션(Arrange, Act, Assert)에 따라 명확하게 구조화된 테스트 작성 방법을 정의합니다.</p>
<ul>
<li>given : 몇 가지 전제 조건이 주어짐(Arrange)</li>
<li>when : 행동이 일어날 때 (Act)</li>
<li>then : 그런 다음 출력을 확인합니다(Assertion).</li>
</ul>
<p>Mockito 라이브러리는 BDD 친화적인 API를 도입하는 <code>BDDMockito</code> 클래스와 함께 제공됩니다. 이 API를 사용하면 <code>given()</code>을 사용하여 테스트를 어레인지하고 <code>then()</code>을 사용하여 어설션을 만드는 BDD 친화적인 접근 방식을 취할 수 있습니다.</p>
<p>이 기사에서는 BDD 기반 Mockito 테스트를 설정하는 방법을 설명하겠습니다. 또한 <code>Mockito</code>와 <code>BDDMockito</code> API의 차이점에 대해 이야기하고 결국 <code>BDDMockito</code> API에 중점을 둘 것입니다.</p>
<h2 id="2-setup">2. Setup</h2>
<h3 id="21-maven-dependencies">2.1 Maven Dependencies</h3>
<p>Mockito의 BDD 버전은 mockito-core 라이브러리의 일부입니다. 시작하려면 아티팩트만 포함하면 됩니다.</p>
<pre><code class="language-maven"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.mockito<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>mockito-core<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>version</span><span class="token punctuation">&gt;</span></span>2.21.0<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>version</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span></code></pre>
<h3 id="22-imports">2.2 Imports</h3>
<p>다음 정적 import를 포함하면 테스트를 더 쉽게 읽을 수 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">BDDMockito</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span></code></pre>
<p><code>BDDMockito</code>는 <code>Mockito</code>를 확장하므로 기존 <code>Mockito</code> API에서 제공하는 기능을 놓치지 않습니다.</p>
<h2 id="3-mockito-vs-bddmockito">3. Mockito vs BDDMockito</h2>
<p>Mockito의 전통적인 mocking은 어레인지 단계에서 <code>when(obj).then*()</code>을 사용하여 수행됩니다.</p>
<p>나중에 Assert 단계에서 <code>verify()</code>를 사용하여 모의 객체와의 상호 작용을 확인할 수 있습니다.</p>
<p><code>BDDMockito</code>는 다양한 <code>Mockito</code> 메소드에 대한 BDD 별칭(aliases)을 제공하므로 <code>given</code>(<code>when</code> 대신)을 사용하여 어레인지 단계를 작성할 수 있으며 마찬가지로 <code>verify</code> 대신 <code>then</code>을 사용하여 Assert 단계를 작성할 수 있습니다.</p>
<p>전통적인 Mockito를 사용하는 테스트 본문의 예를 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token function">when</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
phoneBookService<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> momPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span>
 
<span class="token function">verify</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> momPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p><code>BDDMockito</code>와 어떻게 비교되는지 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
phoneBookService<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> momPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span>
 
<span class="token function">then</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> momPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h2 id="4-mocking-with-bddmockito">4. Mocking With BDDMockito</h2>
<p><code>PhoneBookRepository</code>를 모의해야 하는 <code>PhoneBookService</code>를 테스트해 보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">PhoneBookService</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">PhoneBookRepository</span> phoneBookRepository<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">register</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">,</span> <span class="token class-name">String</span> phone<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span><span class="token punctuation">(</span><span class="token operator">!</span>name<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span>phone<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
          <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            phoneBookRepository<span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> phone<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">search</span><span class="token punctuation">(</span><span class="token class-name">String</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span><span class="token punctuation">(</span><span class="token operator">!</span>name<span class="token punctuation">.</span><span class="token function">isEmpty</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> phoneBookRepository<span class="token punctuation">.</span><span class="token function">getPhoneNumberByContactName</span><span class="token punctuation">(</span>name<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Mockito</code>로서의 <code>BDDMockito</code>를 사용하면 고정되거나 동적일 수 있는 값을 반환할 수 있습니다. 또한 예외를 던질 수도 있습니다:</p>
<h3 id="41-returning-a-fixed-value">4.1 Returning a Fixed Value</h3>
<p><code>BDDMockito</code>를 사용하면 모의 개체 대상 메서드가 호출될 때마다 고정된 결과를 반환하도록 Mockito를 쉽게 구성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
phoneBookService<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span>xContactName<span class="token punctuation">,</span> <span class="token string">""</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
 
<span class="token function">then</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token function">never</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> momPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h3 id="42-returning-a-dynamic-value">4.2 Returning a Dynamic Value</h3>
<p><code>BDDMockito</code>를 사용하면 값을 반환하는 보다 정교한 방법을 제공할 수 있습니다. 입력을 기반으로 동적 결과를 반환할 수 있습니다.</p>
<pre><code class="language-java"><span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">getPhoneNumberByContactName</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">will</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">InvocationOnMock</span> invocation<span class="token punctuation">)</span> <span class="token operator">-&gt;</span>
    invocation<span class="token punctuation">.</span><span class="token function">getArgument</span><span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span> 
      <span class="token operator">?</span> momPhoneNumber 
      <span class="token operator">:</span> <span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
phoneBookService<span class="token punctuation">.</span><span class="token function">search</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">then</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">getPhoneNumberByContactName</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h3 id="43-throwing-an-exception">4.3 Throwing an Exception</h3>
<p>Mockito에게 예외를 발생시키라고 지시하는 것은 매우 간단합니다:</p>
<pre><code class="language-java"><span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>xContactName<span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">willReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">willThrow</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">RuntimeException</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">given</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">eq</span><span class="token punctuation">(</span>tooLongPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">try</span> <span class="token punctuation">{</span>
    phoneBookService<span class="token punctuation">.</span><span class="token function">register</span><span class="token punctuation">(</span>xContactName<span class="token punctuation">,</span> tooLongPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">fail</span><span class="token punctuation">(</span><span class="token string">"Should throw exception"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">RuntimeException</span> ex<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token punctuation">}</span>

<span class="token function">then</span><span class="token punctuation">(</span>phoneBookRepository<span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">should</span><span class="token punctuation">(</span><span class="token function">never</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>momContactName<span class="token punctuation">,</span> tooLongPhoneNumber<span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>주어진 위치와 <code>will*</code>의 위치를 어떻게 교환했는지 주목하세요. 반환 값이 없는 메서드를 조롱하는 경우에는 필수입니다.</p>
<p>또한 (<code>any</code>, <code>eq</code>)와 같은 인수 일치자를 사용하여 고정 값에 의존하는 대신 기준에 따라 보다 일반적인 모의 방법을 제공했다는 점에 유의하세요.</p>