<h2 id="1-introduction">1. Introduction</h2>
<p>이 짧은 튜토리얼에서는 Mockito의 lazy 검증을 살펴보겠습니다. failing-fast 대신 Mockito를 사용하면 테스트가 끝날 때 수집되고 보고된 모든 결과를 볼 수 있습니다.</p>
<h2 id="2-maven-dependencies">2. Maven Dependencies</h2>
<p>Mockito 종속성을 추가하는 것부터 시작해 보겠습니다.</p>
<pre><code class="language-java"><span class="token generics"><span class="token punctuation">&lt;</span>dependency<span class="token punctuation">&gt;</span></span>
    <span class="token generics"><span class="token punctuation">&lt;</span>groupId<span class="token punctuation">&gt;</span></span>org<span class="token punctuation">.</span>mockito<span class="token operator">&lt;</span><span class="token operator">/</span>groupId<span class="token operator">&gt;</span>
    <span class="token generics"><span class="token punctuation">&lt;</span>artifactId<span class="token punctuation">&gt;</span></span>mockito<span class="token operator">-</span>core<span class="token operator">&lt;</span><span class="token operator">/</span>artifactId<span class="token operator">&gt;</span>
    <span class="token generics"><span class="token punctuation">&lt;</span>version<span class="token punctuation">&gt;</span></span><span class="token number">2.21</span><span class="token number">.0</span><span class="token operator">&lt;</span><span class="token operator">/</span>version<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>dependency<span class="token operator">&gt;</span></code></pre>
<h2 id="3-lazy-verification">3. Lazy Verification</h2>
<p>Mockito의 기본 동작은 첫 번째 실패 시 즉각적으로 중지하는 것입니다. 이 접근 방식은 빠른 실패(fail-fast)라고도 합니다. 때로는 이전 실패에 관계없이 모든 검증을 실행하고 보고해야 할 수도 있습니다.</p>
<p><code>VerificationCollector</code>는 테스트 메소드의 모든 검증을 수집하는 JUnit 규칙입니다. 실패가 있는 경우 테스트가 끝나면 실행되고 보고됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LazyVerificationTest</span> <span class="token punctuation">{</span>
 
    <span class="token annotation punctuation">@Rule</span>
    <span class="token keyword">public</span> <span class="token class-name">VerificationCollector</span> verificationCollector <span class="token operator">=</span> <span class="token class-name">MockitoJUnit</span><span class="token punctuation">.</span><span class="token function">collector</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// ...</span>
<span class="token punctuation">}</span></code></pre>
<p>간단한 테스트를 추가해 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">testLazyVerification</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> <span class="token class-name">Exception</span> <span class="token punctuation">{</span>
    <span class="token class-name">List</span> mockList <span class="token operator">=</span> <span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">ArrayList</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    
    <span class="token function">verify</span><span class="token punctuation">(</span>mockList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token function">verify</span><span class="token punctuation">(</span>mockList<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">clear</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p>이 테스트가 실행되면 두 확인 모두 실패했다고 보고됩니다.</p>
<pre><code class="language-shell">org.mockito.exceptions.base.MockitoAssertionError: There were multiple verification failures:
<span class="token number">1</span>. Wanted but not invoked:
arrayList.add<span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
-<span class="token operator">&gt;</span> at com.baeldung.mockito.java8.LazyVerificationTest.testLazyVerification<span class="token punctuation">(</span>LazyVerificationTest.java:21<span class="token punctuation">)</span>
Actually, there were zero interactions with this mock.

<span class="token number">2</span>. Wanted but not invoked:
arrayList.clear<span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
-<span class="token operator">&gt;</span> at com.baeldung.mockito.java8.LazyVerificationTest.testLazyVerification<span class="token punctuation">(</span>LazyVerificationTest.java:22<span class="token punctuation">)</span>
Actually, there were zero interactions with this mock.</code></pre>
<p><code>VerificationCollector</code> 규칙이 없으면 첫 번째 확인만 보고됩니다.</p>
<pre><code class="language-java"><span class="token class-name">Wanted</span> but not invoked<span class="token operator">:</span>
arrayList<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span><span class="token string">"one"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token operator">-&gt;</span> at com<span class="token punctuation">.</span>baeldung<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>java8<span class="token punctuation">.</span><span class="token class-name">LazyVerificationTest</span><span class="token punctuation">.</span><span class="token function">testLazyVerification</span><span class="token punctuation">(</span><span class="token class-name">LazyVerificationTest</span><span class="token punctuation">.</span>java<span class="token operator">:</span><span class="token number">19</span><span class="token punctuation">)</span>
<span class="token class-name">Actually</span><span class="token punctuation">,</span> there were zero interactions <span class="token keyword">with</span> <span class="token keyword">this</span> mock<span class="token punctuation">.</span></code></pre>