<h2 id="1-overview">1. Overview</h2>
<p>이 짧은 튜토리얼에서는 인기 있는 테스트 프레임워크인 Mockito를 사용하여 콜백을 테스트하는 방법에 중점을 둘 것입니다.</p>
<p>먼저 <code>ArgumentCaptor</code>를 사용한 다음 직관적인 <code>doAnswer()</code> 메서드를 사용하는 두 가지 솔루션을 살펴보겠습니다.</p>
<p>Mockito로 테스트하는 방법에 대해 자세히 알아보려면 여기에서 Mockito 시리즈를 확인하세요.</p>
<h2 id="2-introduction-to-callbacks">2. Introduction to Callbacks</h2>
<p>콜백은 주어진 시간에 인수를 콜백(실행)할 것으로 예상되는 메서드에 인수로 전달되는 코드 조각입니다.</p>
<p>이 실행은 동기 콜백처럼 즉시 실행될 수도 있지만 일반적으로 비동기 콜백처럼 나중에 발생할 수도 있습니다.</p>
<p>콜백 사용에 대한 일반적인 시나리오는 서비스 호출의 응답을 처리해야 하는 서비스 상호 작용 중에입니다. 이 튜토리얼에서는 아래에 표시된 <code>Service</code> 인터페이스를 테스트 사례의 협력자로 사용합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Service</span> <span class="token punctuation">{</span>
    <span class="token keyword">void</span> <span class="token function">doAction</span><span class="token punctuation">(</span><span class="token class-name">String</span> request<span class="token punctuation">,</span> <span class="token class-name">Callback</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Response</span><span class="token punctuation">&gt;</span></span> callback<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>Calllback</code> 인수에서는 <code>reply(T response)</code> 메소드를 사용하여 응답을 처리할 클래스를 전달합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">interface</span> <span class="token class-name">Callback</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">T</span><span class="token punctuation">&gt;</span></span> <span class="token punctuation">{</span>
    <span class="token keyword">void</span> <span class="token function">reply</span><span class="token punctuation">(</span><span class="token class-name">T</span> response<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="21-a-simple-service">2.1 A Simple Service</h3>
<p>또한 간단한 서비스 예제를 사용하여 콜백을 전달하고 호출하는 방법을 보여드리겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">doAction</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    service<span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token string">"our-request"</span><span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">Callback</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Response</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">reply</span><span class="token punctuation">(</span><span class="token class-name">Response</span> response<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token function">handleResponse</span><span class="token punctuation">(</span>response<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>handlerResponse</code> 메소드는 <code>Response</code> 객체에 일부 데이터를 추가하기 전에 응답이 유효한지 확인합니다.</p>
<pre><code class="language-java"><span class="token keyword">private</span> <span class="token keyword">void</span> <span class="token function">handleResponse</span><span class="token punctuation">(</span><span class="token class-name">Response</span> response<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>response<span class="token punctuation">.</span><span class="token function">isValid</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        response<span class="token punctuation">.</span><span class="token function">setData</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Data</span><span class="token punctuation">(</span><span class="token string">"Successful data response"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>명확성을 위해 Java Lamda 표현식을 사용하지 않기로 선택했지만 <code>service.doAction</code> 호출을 더 간결하게 작성할 수도 있습니다.</p>
<pre><code class="language-java">service<span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token string">"our-request"</span><span class="token punctuation">,</span> response <span class="token operator">-&gt;</span> <span class="token function">handleResponse</span><span class="token punctuation">(</span>response<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h2 id="3-using-an-argumentcaptor">3. Using an <code>ArgumentCaptor</code></h2>
<p>이제 Mockito를 사용하여 <code>ArgumentCaptor</code>를 사용하여 <code>Callback</code> 개체를 가져오는 방법을 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenServiceWithValidResponse_whenCallbackReceived_thenProcessed</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">ActionHandler</span> handler <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ActionHandler</span><span class="token punctuation">(</span>service<span class="token punctuation">)</span><span class="token punctuation">;</span>
    handler<span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">verify</span><span class="token punctuation">(</span>service<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token function">anyString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> callbackCaptor<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Callback</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Response</span><span class="token punctuation">&gt;</span></span> callback <span class="token operator">=</span> callbackCaptor<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Response</span> response <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Response</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    callback<span class="token punctuation">.</span><span class="token function">reply</span><span class="token punctuation">(</span>response<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> expectedMessage <span class="token operator">=</span> <span class="token string">"Successful data response"</span><span class="token punctuation">;</span>
    <span class="token class-name">Data</span> data <span class="token operator">=</span> response<span class="token punctuation">.</span><span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertEquals</span><span class="token punctuation">(</span>
      <span class="token string">"Should receive a successful message: "</span><span class="token punctuation">,</span> 
      expectedMessage<span class="token punctuation">,</span> data<span class="token punctuation">.</span><span class="token function">getMessage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 예에서는 이 핸들러의 <code>doAction</code> 메소드를 호출하기 전에 먼저 <code>ActionHandler</code>를 생성합니다. 이는 콜백을 호출하는 Simple Service <code>doAction</code> 메소드 호출에 대한 단순한 래퍼입니다.</p>
<p>다음으로 첫 번째 인수로 <code>anyString()</code>을 전달하고 <code>Callback</code> 객체를 캡처하는 두 번째 인수로 <code>callbackCaptor.capture()</code>를 전달하는 모의 서비스 인스턴스에서 doAction이 호출되었는지 확인합니다. 그런 다음 <code>getValue()</code> 메서드를 사용하여 인수의 캡처된 값을 반환할 수 있습니다.</p>
<p>이제 <code>Callback</code> 개체가 있으므로 <code>reply</code> 메서드를 직접 호출하고 <code>Response</code> 데이터에 올바른 값이 있는지 assert하기 전에 기본적으로 유효한 응답 개체를 만듭니다.</p>
<h2 id="4-using-doanswer-method">4. Using <code>doAnswer()</code> Method</h2>
<p>이제 Mockito의 <code>Answer</code> 개체와 <code>doAnswer</code> 메서드를 사용하여 void 메서드 <code>doAction</code>을 스텁하는 콜백이 있는 스텁 메서드에 대한 일반적인 솔루션을 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenServiceWithInvalidResponse_whenCallbackReceived_thenNotProcessed</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">Response</span> response <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Response</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    response<span class="token punctuation">.</span><span class="token function">setIsValid</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">doAnswer</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">Answer</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Void</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">)</span> invocation <span class="token operator">-&gt;</span> <span class="token punctuation">{</span>
        <span class="token class-name">Callback</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Response</span><span class="token punctuation">&gt;</span></span> callback <span class="token operator">=</span> invocation<span class="token punctuation">.</span><span class="token function">getArgument</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        callback<span class="token punctuation">.</span><span class="token function">reply</span><span class="token punctuation">(</span>response<span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">Data</span> data <span class="token operator">=</span> response<span class="token punctuation">.</span><span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token function">assertNull</span><span class="token punctuation">(</span><span class="token string">"No data in invalid response: "</span><span class="token punctuation">,</span> data<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>service<span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token function">anyString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">Callback</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">ActionHandler</span> handler <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ActionHandler</span><span class="token punctuation">(</span>service<span class="token punctuation">)</span><span class="token punctuation">;</span>
    handler<span class="token punctuation">.</span><span class="token function">doAction</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>그리고 두 번째 예에서는 먼저 나중에 테스트에서 사용할 잘못된 <code>Response</code> 개체를 만듭니다.</p>
<p>다음으로 <code>doAction</code>이 호출될 때 호출을 가로채고 <code>invocation.getArgument(1)</code>을 사용하여 메서드 인수를 가져오도록 모의 서비스에 <code>Answer</code>를 설정하여 <code>Callback</code> 인수를 가져옵니다.</p>
<p>마지막 단계는 <code>ActionHandler</code>를 생성하고 <code>Answer</code>가 호출되도록 하는 <code>doAction</code>을 호출하는 것입니다.</p>
<p>stubbing void 메서드에 대해 자세히 알아보려면 여기를 참조하세요.</p>