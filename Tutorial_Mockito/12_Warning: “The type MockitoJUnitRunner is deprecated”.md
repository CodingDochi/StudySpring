<h2 id="1-introduction">1. Introduction</h2>
<p>이 빠른 튜토리얼에서는 인기 있는 테스트 프레임워크인 Mockito로 작업할 때 볼 수 있는 경고 중 하나를 살펴보겠습니다.</p>
<p>즉, 더 이상 사용되지 않는 <code>MockitoJUnitRunner</code> 클래스를 참조하는 것입니다. 이 경고가 발생하는 이유와 이를 처리하는 방법을 살펴보겠습니다.</p>
<p>마지막으로 <code>MockitoJUnitRunner</code>를 사용하여 Mockito에게 다른 Mockito 어노테이션과 함께 <code>@Mock</code> 또는 <code>@Spy</code> 어노테이션이 달린 테스트 더블을 초기화하도록 지시할 수 있다는 점을 상기시켜 보겠습니다.</p>
<p>Mockito 테스트에 대해 자세히 알아보려면 여기에서 Mockito 시리즈를 확인하세요.</p>
<h2 id="2-why-is-this-warning-shown">2. Why Is This Warning Shown</h2>
<p>이 지원 중단 경고는 2.2.20(2016년 11월) 이전의 Mockito 버전을 사용하는 경우 표시됩니다.</p>
<p>그 뒷이야기를 간략하게 살펴보겠습니다. 이전 버전의 Mockito에서는 Mockito JUnit Runner를 사용하려는 경우 가져와야 하는 패키지는 다음과 같습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>runners</span><span class="token punctuation">.</span><span class="token class-name">MockitoJUnitRunner</span><span class="token punctuation">;</span></code></pre>
<p>버전 2.2.20부터 JUnit 관련 클래스가 특정 JUnit 패키지로 재그룹화되었습니다. 여기서 패키지를 찾을 수 있습니다:</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">MockitoJUnitRunner</span><span class="token punctuation">;</span></code></pre>
<p>결과적으로 원래 <code>org.mockito.runners.MockitoJUnitRunner</code>는 이제 더 이상 사용되지 않습니다. 클래스의 로직은 이제 <code>org.mockito.junit.runners.MockitoJUnitRunner</code>에 속합니다.</p>
<p>경고를 제거하는 것은 필수는 아니지만 제거하는 것이 좋습니다. Mockito 버전 3에서는 이 클래스를 제거합니다.</p>
<h2 id="3-solutions">3. Solutions</h2>
<p>이 섹션에서는 이러한 지원 중단 경고를 해결하기 위한 세 가지 솔루션을 설명합니다.</p>
<ul>
<li>올바른 import를 사용하도록 업데이트 중</li>
<li><code>MockitoAnnotations</code>를 사용하여 필드 초기화 </li>
<li><code>MockitoRule</code> 사용</li>
</ul>
<h3 id="31-updating-imports">3.1 Updating Imports</h3>
<p>간단히 패키지 import 문을 변경하는 가장 간단한 솔루션부터 시작해 보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">MockitoJUnitRunner</span><span class="token punctuation">;</span>

<span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">MockitoJUnitRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleTest</span> <span class="token punctuation">{</span>
    <span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>그리고 그게 다야! 변경은 상당히 쉬워야 합니다.</p>
<h3 id="32-initializing-fields-using-mockitoannotations">3.2 Initializing Fields Using <code>MockitoAnnotations</code></h3>
<p>다음 예제에서는 <code>MockitoAnnotations</code> 클래스를 사용하여 모의 객체를 다른 방식으로 초기화하겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">Before</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito</span><span class="token punctuation">.</span><span class="token class-name">MockitoAnnotations</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleTest</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Before</span> 
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">initMocks</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">MockitoAnnotations</span><span class="token punctuation">.</span><span class="token function">initMocks</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    <span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>우선, <code>MockitoJUnitRunner</code>에 대한 참조를 제거합니다. 대신 <code>MockitoAnnotations</code> 클래스의 정적 <code>initMocks()</code> 메서드를 호출합니다.</p>
<p>테스트 클래스의 JUnit <code>@Before</code> 메소드에서 이를 수행합니다. 이는 각 테스트가 실행되기 전에 Mockito 어노테이션이 있는 모든 필드를 초기화합니다.</p>
<h3 id="33-using-mockitorule">3.3 Using <code>MockitoRule</code></h3>
<p>그러나 이미 언급했듯이 <code>MockitoJUnitRunner</code>는 결코 필수가 아닙니다. 이 마지막 예에서는 <code>MockitoRule</code>을 사용하여 <code>@Mock</code>을 작동시킬 수 있는 또 다른 방법을 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">Rule</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">MockitoJUnit</span><span class="token punctuation">;</span>
<span class="token keyword">import</span> <span class="token namespace">org<span class="token punctuation">.</span>mockito<span class="token punctuation">.</span>junit</span><span class="token punctuation">.</span><span class="token class-name">MockitoRule</span><span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ExampleTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Rule</span>
    <span class="token keyword">public</span> <span class="token class-name">MockitoRule</span> rule <span class="token operator">=</span> <span class="token class-name">MockitoJUnit</span><span class="token punctuation">.</span><span class="token function">rule</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">//...</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 이 예에서 JUnit 규칙은 <code>@Mock</code> 문이 열리면서 모든 모의 항목을 환영합니다. 따라서 <code>MockitoAnnotations#initMocks(Object)</code> 또는 <code>@RunWith(MockitoJUnitRunner.class)</code>를 수학적으로 사용할 필요가 없음을 의미합니다.</p>