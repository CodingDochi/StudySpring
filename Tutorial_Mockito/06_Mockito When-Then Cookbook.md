<h2 id="1-overview">1. Overview</h2>
<p>이 요리책은 Mockito를 사용하여 다양한 예제와 사용 사례에서 동작을 구성하는 방법을 보여줍니다.</p>
<p>요리책의 형식은 예제에 중점을 두고 실용적이므로 불필요한 세부 정보나 설명이 필요하지 않습니다.</p>
<p>물론 Mockito로 테스트하는 방법에 대해 자세히 알아보려면 여기에서 다른 Mockito 기사를 살펴보세요.</p>
<p>우리는 이전 요리책에서 사용한 것과 동일한 구현인 간단한 목록 구현을 흉내낼 것입니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyList</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">get</span><span class="token punctuation">(</span><span class="token keyword">final</span> <span class="token keyword">int</span> index<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token number">1</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="2-cookbook">2. Cookbook</h2>
<p>모의에 대한 간단한 반환 동작을 구성합니다.</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> listMock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">when</span><span class="token punctuation">(</span>listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">anyString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">boolean</span> added <span class="token operator">=</span> listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">randomAlphabetic</span><span class="token punctuation">(</span><span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span>added<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isFalse</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>다른 방법으로 모의에 대한 반환 동작을 구성합니다.</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> listMock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">doReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>listMock<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">anyString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token keyword">boolean</span> added <span class="token operator">=</span> listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">randomAlphabetic</span><span class="token punctuation">(</span><span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span>added<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isFalse</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>void 반환 유형으로 메서드의 동작을 구성하여 예외를 발생시킵니다.</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> listMock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">doThrow</span><span class="token punctuation">(</span><span class="token class-name">NullPointerException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>listMock<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">clear</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThrows</span><span class="token punctuation">(</span><span class="token class-name">NullPointerException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> listMock<span class="token punctuation">.</span><span class="token function">clear</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>여러 통화의 동작을 구성합니다.</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> listMock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">when</span><span class="token punctuation">(</span>listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">anyString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span>
  <span class="token punctuation">.</span><span class="token function">thenThrow</span><span class="token punctuation">(</span><span class="token class-name">IllegalStateException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThrows</span><span class="token punctuation">(</span><span class="token class-name">IllegalStateException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
    listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">randomAlphabetic</span><span class="token punctuation">(</span><span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    listMock<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token function">randomAlphabetic</span><span class="token punctuation">(</span><span class="token number">6</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>스파이의 행동을 구성합니다:</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> instance <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MyList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token class-name">MyList</span> spy <span class="token operator">=</span> <span class="token function">spy</span><span class="token punctuation">(</span>instance<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">doThrow</span><span class="token punctuation">(</span><span class="token class-name">NullPointerException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>spy<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertThrows</span><span class="token punctuation">(</span><span class="token class-name">NullPointerException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">-&gt;</span> spy<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>모의 객체에서 실제 기본 메서드를 호출하도록 메서드를 구성합니다.</p>
<pre><code class="language-java">모의 객체에서 실제 기본 메서드를 호출하도록 메서드를 구성합니다<span class="token punctuation">.</span></code></pre>
<p>사용자 정의 답변으로 모의 메서드 호출을 구성합니다.</p>
<pre><code class="language-java"><span class="token class-name">MyList</span> listMock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">doAnswer</span><span class="token punctuation">(</span>invocation <span class="token operator">-&gt;</span> <span class="token string">"Always the same"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>listMock<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token function">anyInt</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token class-name">String</span> element <span class="token operator">=</span> listMock<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertThat</span><span class="token punctuation">(</span>element<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token string">"Always the same"</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>