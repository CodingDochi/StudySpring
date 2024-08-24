<h2 id="1-overview">1. Overview</h2>
<p>이 짧은 튜토리얼에서는 Mockito를 사용하여 final 클래스와 메소드를 모의하는 방법에 중점을 둘 것입니다.</p>
<p>Mockito 프레임워크(예: Mockito Verify, Mockito When/Then 및 Mockito의 Mock 메서드)에 초점을 맞춘 다른 기사와 마찬가지로 아래 표시된 <code>MyList</code> 클래스를 테스트 사례의 공동 작업자로 사용합니다.</p>
<p>이 튜토리얼에 새로운 메소드를 추가하겠습니다:</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">MyList</span> <span class="token keyword">extends</span> <span class="token class-name">AbstractList</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">final</span> <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">finalMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>그리고 우리는 이를 <code>fianl</code> 하위 클래스로 확장할 것입니다:</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">final</span> <span class="token keyword">class</span> <span class="token class-name">FinalList</span> <span class="token keyword">extends</span> <span class="token class-name">MyList</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token number">1</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="2-mock-a-final-method">2. Mock a Final Method</h2>
<p>Mockito를 적절하게 구성한 후에는 다른 메소드와 마찬가지로 최종 메소드를 모의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenMockFinalMethod_thenMockWorks</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">MyList</span> myList <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">MyList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">MyList</span> mock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">MyList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">when</span><span class="token punctuation">(</span>mock<span class="token punctuation">.</span><span class="token function">finalMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertThat</span><span class="token punctuation">(</span>mock<span class="token punctuation">.</span><span class="token function">finalMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotZero</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>MyList</code>의 구체적인 인스턴스와 모의 인스턴스를 생성함으로써 두 버전의 <code>finalMethod()</code>에서 반환된 값을 비교하고 모의 인스턴스가 호출되었는지 확인할 수 있습니다.</p>
<h2 id="3-mock-a-final-class">3. Mock a Final Class</h2>
<p>final 클래스를 mocking하는 것은 다른 클래스를 mocking하는 것만큼 쉽습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">whenMockFinalClass_thenMockWorks</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token class-name">FinalList</span> mock <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">FinalList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">when</span><span class="token punctuation">(</span>mock<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">2</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">assertThat</span><span class="token punctuation">(</span>mock<span class="token punctuation">.</span><span class="token function">size</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isNotEqualTo</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>위의 테스트와 유사하게 최종 클래스의 구체적인 인스턴스와 모의 인스턴스를 생성하고 메서드를 모의하고 모의 인스턴스가 다르게 동작하는지 확인합니다.</p>