<h2 id="1-overview">1. Overview</h2>
<p>이 튜토리얼에서는 단위 테스트에서 Mockito ArgumentCaptor를 사용하는 일반적인 사용 사례를 다룹니다. 또는 다른 Mockito.verify 사용 사례에 대해서는 Mockito 확인 설명서를 참조하세요.</p>
<h2 id="2-using-argumentcaptor">2. Using <code>ArgumentCaptor</code></h2>
<p><code>ArgumentCaptor</code>를 사용하면 메서드에 전달된 인수를 캡처하여 검사할 수 있습니다. 이는 테스트하려는 메서드 외부의 인수에 액세스할 수 없을 때 특히 유용합니다.</p>
<p>예를 들어 테스트하려는 send 메서드가 있는 <code>EmailService</code> 클래스를 생각해 보세요.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EmailService</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> <span class="token class-name">DeliveryPlatform</span> platform<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">EmailService</span><span class="token punctuation">(</span><span class="token class-name">DeliveryPlatform</span> platform<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>platform <span class="token operator">=</span> platform<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">send</span><span class="token punctuation">(</span><span class="token class-name">String</span> <span class="token keyword">to</span><span class="token punctuation">,</span> <span class="token class-name">String</span> subject<span class="token punctuation">,</span> <span class="token class-name">String</span> body<span class="token punctuation">,</span> <span class="token keyword">boolean</span> html<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Format</span> format <span class="token operator">=</span> <span class="token class-name">Format</span><span class="token punctuation">.</span>TEXT_ONLY<span class="token punctuation">;</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>html<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            format <span class="token operator">=</span> <span class="token class-name">Format</span><span class="token punctuation">.</span>HTML<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
        <span class="token class-name">Email</span> email <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Email</span><span class="token punctuation">(</span><span class="token keyword">to</span><span class="token punctuation">,</span> subject<span class="token punctuation">,</span> body<span class="token punctuation">)</span><span class="token punctuation">;</span>
        email<span class="token punctuation">.</span><span class="token function">setFormat</span><span class="token punctuation">(</span>format<span class="token punctuation">)</span><span class="token punctuation">;</span>
        platform<span class="token punctuation">.</span><span class="token function">deliver</span><span class="token punctuation">(</span>email<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p><code>EmailService.send</code>에서 <code>platform.deliver</code>가 새 <code>Email</code>을 인수로 사용하는 방법을 확인하세요. 테스트의 일환으로 새 <code>Email</code>의 형식 필드가 <code>Format.HTML</code>로 설정되어 있는지 확인하고 싶습니다. 이를 위해서는 <code>platform.deliver</code>에 전달된 인수를 캡처하고 검사해야 합니다.</p>
<p><code>ArgumentCaptor</code>를 사용하여 어떻게 도움을 줄 수 있는지 살펴보겠습니다.</p>
<h3 id="21-set-up-the-unit-test">2.1 Set Up the Unit Test</h3>
<p>먼저 단위 테스트 클래스를 만들어 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">MockitoExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">class</span> <span class="token class-name">EmailServiceUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token class-name">DeliveryPlatform</span> platform<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@InjectMocks</span>
    <span class="token class-name">EmailService</span> emailService<span class="token punctuation">;</span>
  
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token punctuation">}</span></code></pre>
<p>우리는 <code>@InjectMocks</code> 어노테이션과 함께 <code>EmailService</code>에 자동으로 주입되는 <code>DeliveryPlatform</code>을 모의하기 위해 <code>@Mock</code> 어노테이션을 사용하고 있습니다. 자세한 내용은 Mockito 어노테이션 기사를 참조하세요.</p>
<h3 id="22-add-an-argumentcaptor-field">2.2 Add an <code>ArgumentCaptor</code> Field</h3>
<p>둘째, 캡처된 인수를 저장하기 위해 <code>Email</code> 유형의 새 <code>ArgumentCaptor</code> 필드를 추가하겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Captor</span>
<span class="token class-name">ArgumentCaptor</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">Email</span><span class="token punctuation">&gt;</span></span> emailCaptor<span class="token punctuation">;</span></code></pre>
<h3 id="23-capture-the-argument">2.3 Capture the Argument</h3>
<p>셋째, <code>ArgumentCaptor</code>와 함께 <code>verify()</code>를 사용하여 <code>Email</code>을 캡처해 보겠습니다.</p>
<pre><code class="language-java"><span class="token function">verify</span><span class="token punctuation">(</span>platform<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">deliver</span><span class="token punctuation">(</span>emailCaptor<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>그런 다음 캡처된 값을 가져와 새 <code>Email</code> 개체로 저장할 수 있습니다.</p>
<pre><code class="language-java"><span class="token class-name">Email</span> emailCaptorValue <span class="token operator">=</span> emailCaptor<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<h3 id="24--inspect-the-captured-value">2.4  Inspect the Captured Value</h3>
<p>마지막으로 캡처된 <code>Email</code> 개체를 assert하기 위한 어설션이 포함된 전체 테스트를 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">void</span> <span class="token function">whenDoesSupportHtml_expectHTMLEmailFormat</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">String</span> <span class="token keyword">to</span> <span class="token operator">=</span> <span class="token string">"info@baeldung.com"</span><span class="token punctuation">;</span>
    <span class="token class-name">String</span> subject <span class="token operator">=</span> <span class="token string">"Using ArgumentCaptor"</span><span class="token punctuation">;</span>
    <span class="token class-name">String</span> body <span class="token operator">=</span> <span class="token string">"Hey, let'use ArgumentCaptor"</span><span class="token punctuation">;</span>

    emailService<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token keyword">to</span><span class="token punctuation">,</span> subject<span class="token punctuation">,</span> body<span class="token punctuation">,</span> <span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token function">verify</span><span class="token punctuation">(</span>platform<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">deliver</span><span class="token punctuation">(</span>emailCaptor<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Email</span> value <span class="token operator">=</span> emailCaptor<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">assertThat</span><span class="token punctuation">(</span>value<span class="token punctuation">.</span><span class="token function">getFormat</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">isEqualTo</span><span class="token punctuation">(</span><span class="token class-name">Format</span><span class="token punctuation">.</span>HTML<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="3-avoiding-stubbing">3. Avoiding Stubbing</h2>
<p>스텁과 함께 <code>ArgumentCaptor</code>를 사용할 수 있지만 일반적으로 그렇게 하는 것은 피해야 합니다. 명확히 하자면, Mockito에서 이는 일반적으로 <code>Mockito.when</code>과 함께 <code>ArgumentCaptor</code>를 사용하지 않는 것을 의미합니다. 스텁을 사용하면 <code>ArgumentMatcher</code>를 대신 사용해야 합니다.</p>
<p>스터빙을 피해야 하는 몇 가지 이유를 살펴보겠습니다.</p>
<h3 id="31-decreased-test-readability">3.1 Decreased Test Readability</h3>
<p>먼저 간단한 테스트를 고려해보세요</p>
<pre><code class="language-java"><span class="token class-name">Credentials</span> credentials <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Credentials</span><span class="token punctuation">(</span><span class="token string">"baeldung"</span><span class="token punctuation">,</span> <span class="token string">"correct_password"</span><span class="token punctuation">,</span> <span class="token string">"correct_key"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">when</span><span class="token punctuation">(</span>platform<span class="token punctuation">.</span><span class="token function">authenticate</span><span class="token punctuation">(</span><span class="token function">eq</span><span class="token punctuation">(</span>credentials<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">AuthenticationStatus</span><span class="token punctuation">.</span>AUTHENTICATED<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertTrue</span><span class="token punctuation">(</span>emailService<span class="token punctuation">.</span><span class="token function">authenticatedSuccessfully</span><span class="token punctuation">(</span>credentials<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>여기서는 모의 객체가 객체를 반환해야 하는 시기를 지정하기 위해 <code>eq(credentials)</code>를 사용합니다. 다음으로, 대신 <code>ArgumentCaptor</code>를 사용하여 동일한 테스트를 고려해 보세요.</p>
<pre><code class="language-java"><span class="token class-name">Credentials</span> credentials <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Credentials</span><span class="token punctuation">(</span><span class="token string">"baeldung"</span><span class="token punctuation">,</span> <span class="token string">"correct_password"</span><span class="token punctuation">,</span> <span class="token string">"correct_key"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">when</span><span class="token punctuation">(</span>platform<span class="token punctuation">.</span><span class="token function">authenticate</span><span class="token punctuation">(</span>credentialsCaptor<span class="token punctuation">.</span><span class="token function">capture</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token class-name">AuthenticationStatus</span><span class="token punctuation">.</span>AUTHENTICATED<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token function">assertTrue</span><span class="token punctuation">(</span>emailService<span class="token punctuation">.</span><span class="token function">authenticatedSuccessfully</span><span class="token punctuation">(</span>credentials<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token function">assertEquals</span><span class="token punctuation">(</span>credentials<span class="token punctuation">,</span> credentialsCaptor<span class="token punctuation">.</span><span class="token function">getValue</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span></code></pre>
<p>첫 번째 테스트와 달리 <code>eq(credentials)</code>와 동일한 작업을 수행하기 위해 마지막 줄에서 추가 어설션을 수행해야 하는 방법에 유의하세요.</p>
<p>마지막으로, <code>credentialCaptor.capture()</code>가 무엇을 참조하는지 즉시 명확하지 않다는 점에 유의하세요. 이는 우리가 사용하는 라인 외부에 captor를 생성해야 하므로 가독성이 떨어지기 때문입니다.</p>
<h3 id="32-reduced-defect-localization">3.2 Reduced Defect Localization</h3>
<p><code>emailService.authenticatedSuccessfully</code>이 <code>platform.authenticate</code>를 호출하지 않으면 예외가 발생하기 때문입니다.</p>
<pre><code class="language-java">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>exceptions<span class="token punctuation">.</span>base<span class="token punctuation">.</span><span class="token class-name">MockitoException</span><span class="token operator">:</span> 
<span class="token class-name">No</span> argument value was captured<span class="token operator">!</span></code></pre>
<p>이는 우리의 스텁 메서드가 인수를 캡처하지 않았기 때문입니다. 그러나 실제 문제는 테스트 자체가 아니라 테스트하고 있는 실제 방법에 있습니다.</p>
<p>즉, 실제 결함은 우리가 테스트하는 방법에 있는 반면 테스트에서는 예외가 발생하도록 잘못 안내합니다.</p>