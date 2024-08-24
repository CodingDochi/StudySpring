<h2 id="1-introduction">1. Introduction</h2>
<h3 id="11-overview">1.1 Overview</h3>
<p>이 게시물에서 우리는 모의(mocking)가 무엇인지, 왜 사용하는지, 그리고 가장 많이 사용되는 Java용 모의 라이브러리를 사용하여 동일한 테스트 사례를 모의하는 방법에 대한 몇 가지 예에 대해 이야기하겠습니다.</p>
<p>모의 개념에 대한 몇 가지 형식적/준형식적 정의부터 시작하겠습니다. 그런 다음 테스트 중인 사례를 제시하고 각 라이브러리에 대한 예제를 추적한 후 몇 가지 결론을 내릴 것입니다. 선택한 라이브러리는 Mockito, EasyMock 및 JMockit입니다.</p>
<p>조롱의 기본 사항을 이미 알고 있다고 생각되면 다음 세 개의 하위 섹션을 읽지 않고 섹션 2로 건너뛸 수도 있습니다.</p>
<h3 id="12-reasons-to-use-mocks">1.2 Reasons to Use Mocks</h3>
<p>테스트(TDD, ATDD 또는 BDD)를 중심으로 한 일부 주도적 개발 방법론에 따라 이미 코드를 작성했다고 가정하겠습니다. 또는 단순히 해당 기능을 달성하기 위해 종속성에 의존하는 기존 클래스에 대한 테스트를 만들고 싶을 수도 있습니다.</p>
<p>어떤 경우든, 클래스를 단위 테스트할 때 우리는 해당 클래스의 기능만 테스트하고 종속성은 테스트하지 않습니다(그 구현을 신뢰하거나 직접 테스트할 것이기 때문입니다).</p>
<p>이를 달성하려면 해당 종속성을 제어할 수 있는 대체 개체를 테스트 대상 개체에 제공해야 합니다. 이런 식으로 우리는 극단적인 반환 값, 예외 발생을 강제하거나 단순히 시간이 많이 걸리는 메서드를 고정된 반환 값으로 줄일 수 있습니다.</p>
<p>이 제어된 교체는 모의이며 테스트 코딩을 단순화하고 테스트 실행 시간을 줄이는 데 도움이 됩니다.</p>
<h3 id="13-mock-concepts-and-definition">1.3 Mock Concepts and Definition</h3>
<p>모든 사람이 모의 객체에 대해 알아야 할 기본 사항을 요약한 Martin Fowler가 작성한 기사에서 네 가지 정의를 살펴보겠습니다.</p>
<ul>
<li>
<p>Dummy 객체는 전달되지만 실제로는 사용되지 않습니다. 일반적으로 매개변수 목록을 채우는 데 사용됩니다.</p>
</li>
<li>
<p>Fake 객체에는 작동하는 구현이 있지만 일반적으로 프로덕션에 적합하지 않게 만드는 몇 가지 지름길을 사용합니다(인메모리 데이터베이스가 좋은 예입니다).</p>
</li>
<li>
<p>Stub은 테스트 중에 이루어진 호출에 대해 미리 준비된 답변을 제공하며 일반적으로 테스트를 위해 프로그래밍된 것 이외의 어떤 것에도 전혀 응답하지 않습니다. 스텁은 '보낸' 메시지를 기억하는 이메일 게이트웨이 스텁과 같이 통화에 대한 정보를 기록하거나 '보낸' 메시지 수만 기록할 수도 있습니다.</p>
</li>
<li>
<p>Mocks는 우리가 여기서 말하는 것입니다. 수신할 것으로 예상되는 호출의 사양을 형성하는 기대로 미리 프로그래밍된 객체입니다.</p>
</li>
</ul>
<h3 id="14-to-mock-or-not-to-mock-that-is-the-question">1.4 To Mock or Not to Mock: That Is the Question</h3>
<p>모든 것이 mocking되어서는 안 됩니다. 때로는 실제 이점이 거의 없는 메서드/기능을 조롱하는 통합 테스트를 수행하는 것이 더 나을 때도 있습니다. LoginDao를 테스트하는 테스트 케이스(다음 섹션에 표시됨)입니다.</p>
<p>LoginDao는 DB 액세스를 위해 일부 타사 라이브러리를 사용하고 이를 mocking하는 것은 매개변수가 호출을 위해 준비되었는지 확인하는 것으로만 구성되지만 호출이 원하는 데이터를 반환하는지 테스트해야 합니다.</p>
<p>이러한 이유로 이 예제에는 포함되지 않습니다. (타사 라이브러리 호출에 대한 모의 호출을 사용하는 단위 테스트와 타사 라이브러리의 실제 성능을 테스트하기 위해 DBUnit을 사용한 통합 테스트를 모두 작성할 수 있지만)</p>
<h2 id="2-test-case">2. Test Case</h2>
<p>이전 섹션의 모든 내용을 염두에 두고 매우 일반적인 테스트 사례를 제안하고 이를 모의 개체를 사용하여 테스트하는 방법(모의 개체를 사용하는 것이 적합한 경우)을 제안해 보겠습니다. 이는 나중에 다양한 모의 라이브러리를 비교할 수 있는 공통 시나리오를 갖는 데 도움이 될 것입니다.</p>
<h3 id="21-proposed-case">2.1 Proposed Case</h3>
<p>제안된 테스트 케이스는 계층화된 아키텍처를 갖춘 애플리케이션의 로그인 프로세스입니다.</p>
<p>로그인 요청은 DAO(DB에서 사용자 자격 증명을 찾는)를 사용하는 서비스를 사용하는 컨트롤러에 의해 처리됩니다. 우리는 각 레이어의 구현을 너무 깊게 다루지 않고 각 레이어 구성 요소 간의 상호 작용에 더 중점을 둘 것입니다.</p>
<p>이런 식으로 <code>LoginController</code>, <code>LoginService</code> 및 <code>LoginDAO</code>를 갖게 됩니다. 설명을 위해 다이어그램을 살펴보겠습니다.</p>
<p><img src="https://www.baeldung.com/wp-content/uploads/2016/07/Test-case-1.png"></p>
<h3 id="22-implementation">2.2 Implementation</h3>
<p>이제 테스트 사례에 사용된 구현을 따라가므로 테스트에서 무슨 일이 일어나고 있는지(또는 무슨 일이 일어나야 하는지) 이해할 수 있습니다.</p>
<p>모든 작업에 사용되는 모델인 <code>UserForm</code>부터 시작하겠습니다. 이 모델은 사용자의 이름과 비밀번호만 보유하며(간단화하기 위해 공개 액세스 한정자를 사용하고 있습니다) 해당 속성에 대한 모의를 허용하는 <code>username</code> 필드에 대한 getter 메서드를 사용합니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">UserForm</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> password<span class="token punctuation">;</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> username<span class="token punctuation">;</span>
    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> username<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>필요한 경우 모의할 수 있도록 메서드만 있기를 원하기 때문에 기능이 없는 <code>LoginDAO</code>를 살펴보겠습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginDao</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">login</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span> userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token number">0</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p><code>LoginDao</code>는 <code>LoginService</code>의 <code>login</code> 메소드에서 사용됩니다. <code>LoginService</code>에는 해당 모형을 테스트하기 위해 <code>void</code>를 반환하는 <code>setCurrentUser</code> 메서드도 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginService</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginDao</span> loginDao<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">String</span> currentUser<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">login</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span> userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">assert</span> <span class="token keyword">null</span> <span class="token operator">!=</span> userForm<span class="token punctuation">;</span>
        <span class="token keyword">int</span> loginResults <span class="token operator">=</span> loginDao<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">switch</span> <span class="token punctuation">(</span>loginResults<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">case</span> <span class="token number">1</span><span class="token operator">:</span>
                <span class="token keyword">return</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
            <span class="token keyword">default</span><span class="token operator">:</span>
                <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token class-name">String</span> username<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token keyword">null</span> <span class="token operator">!=</span> username<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span>currentUser <span class="token operator">=</span> username<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>마지막으로 <code>LoginController</code>는 <code>login</code> 메서드로 <code>LoginService</code>를 사용합니다. 여기에는 다음이 포함됩니다.</p>
<ul>
<li>mocked 서비스에 대한 호출이 수행되지 않는 경우입니다.</li>
<li>하나의 메소드만 호출되는 경우입니다.</li>
<li>모든 메소드가 호출되는 경우입니다.</li>
<li>예외 발생이 테스트되는 경우입니다.</li>
</ul>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginController</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token class-name">LoginService</span> loginService<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">String</span> <span class="token function">login</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span> userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token keyword">null</span> <span class="token operator">==</span> userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token string">"ERROR"</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
            <span class="token keyword">boolean</span> logged<span class="token punctuation">;</span>

            <span class="token keyword">try</span> <span class="token punctuation">{</span>
                logged <span class="token operator">=</span> loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token string">"ERROR"</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">if</span> <span class="token punctuation">(</span>logged<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span>userForm<span class="token punctuation">.</span><span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token keyword">return</span> <span class="token string">"OK"</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> <span class="token string">"KO"</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 우리가 테스트하려는 것이 무엇인지 알았으니 각 라이브러리에서 이를 어떻게 모의할 것인지 살펴보겠습니다.</p>
<h2 id="3-test-setup">3. Test Setup</h2>
<h3 id="31-mockito">3.1 Mockito</h3>
<p>Mockito의 경우 버전 2.8.9를 사용합니다.</p>
<p>모의 객체를 생성하고 사용하는 가장 쉬운 방법은 <code>@Mock</code> 및 <code>@InjectMocks</code> 어노테이션을 사용하는 것입니다. 첫 번째는 필드를 정의하는 데 사용되는 클래스에 대한 모의를 생성하고 두 번째는 생성된 모의를 어노테이션이 달린 모의에 주입하려고 시도합니다.</p>
<p>부분 모의(모의되지 않은 메서드에서 일반 구현을 사용하는 모의)를 생성할 수 있는 <code>@Spy</code>와 같은 더 많은 어노테이션이 있습니다.</p>
<p>즉, 이 모든 "마법"이 작동하려면 해당 모의 객체를 사용하는 테스트를 실행하기 전에 <code>MockitoAnnotations.initMocks(this)</code>를 호출해야 합니다. 이는 일반적으로 <code>@Before</code> 어노테이션이 달린 메서드에서 수행됩니다. <code>MockitoJUnitRunner</code>를 사용할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginControllerTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginDao</span> loginDao<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Spy</span>
    <span class="token annotation punctuation">@InjectMocks</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginService</span> spiedLoginService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginService</span> loginService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@InjectMocks</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginController</span> loginController<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Before</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setUp</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        loginController <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">LoginController</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">MockitoAnnotations</span><span class="token punctuation">.</span><span class="token function">initMocks</span><span class="token punctuation">(</span><span class="token keyword">this</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="32-easymock">3.2 EasyMock</h3>
<p><code>EasyMock</code>의 경우 버전 3.4(Javadoc)를 사용합니다. EasyMock을 사용하면 모의 객체가 "작동"을 시작하려면 모든 테스트 메서드에서 <code>EasyMock.replay(mock)</code>를 호출해야 합니다. 그렇지 않으면 예외가 발생합니다.</p>
<p>모의 클래스와 테스트된 클래스는 어노테이션을 통해 정의할 수도 있지만 이 경우 작동하도록 정적 메서드를 호출하는 대신 테스트 클래스에 <code>EasyMockRunner</code>를 사용합니다.</p>
<p>모의는 <code>@Mock</code> 어노테이션으로 생성되고 테스트된 객체는 <code>@TestSubject</code>로 생성됩니다(생성된 모의에서 종속성이 주입됩니다). 테스트된 개체는 인라인으로 생성되어야 합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@RunWith</span><span class="token punctuation">(</span><span class="token class-name">EasyMockRunner</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginControllerTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginDao</span> loginDao<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginService</span> loginService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@TestSubject</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginController</span> loginController <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">LoginController</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="33-jmockit">3.3 JMockit</h3>
<p>JMockit의 경우 버전 1.49(Javadoc)를 사용합니다.</p>
<p>JMockit 설정은 부분 모의에 대한 특정 어노테이션이 없다는 점을 제외하면 Mockito만큼 쉽습니다(그리고 실제로도 필요하지 않습니다). 또한 더 이상 JUnit 4 테스트를 위해 <code>@RunWith(JMockit.class)</code>로 테스트 클래스에 어노테이션을 달 필요가 없습니다.</p>
<p>모의는 <code>@Injectable</code> 어노테이션(모의 인스턴스 하나만 생성) 또는 <code>@Mocked</code> 어노테이션(어노테이션이 달린 필드 클래스의 모든 인스턴스에 대해 모의를 생성)을 사용하여 정의됩니다.</p>
<p><code>@Tested</code> 어노테이션을 사용하여 테스트된 인스턴스가 생성되고 해당 종속성이 주입됩니다.</p>
<pre><code class="language-java"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">LoginControllerIntegrationTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Injectable</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginDao</span> loginDao<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Injectable</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginService</span> loginService<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Tested</span>
    <span class="token keyword">private</span> <span class="token class-name">LoginController</span> loginController<span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="4-verifying-no-calls-to-mock">4. Verifying No Calls to Mock</h2>
<h3 id="41-mockito">4.1 Mockito</h3>
<p>Mockito에서 모의 개체가 호출을 받지 않았는지 확인하기 위해 모의 개체를 허용하는 <code>verifyNoInteractions()</code> 메서드가 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertThatNoMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verifyNoInteractions</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="42easymock">4.2EasyMock</h3>
<p>모의 개체가 호출을 받지 않았는지 확인하기 위해 동작을 지정하지 않고 모의 개체를 재생한 후 마지막으로 확인합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertThatNoMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="43-jmockit">4.3 JMockit</h3>
<p>모의 개체가 호출을 받지 않았는지 확인하기 위해 해당 모의 개체에 대한 기대치를 지정하지 않고 해당 모의 개체에 대해 <code>FullVerifications(mock)</code>을 수행합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertThatNoMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="5-defining-mocked-method-calls-and-verifying-calls-to-mocks">5. Defining Mocked Method Calls and Verifying Calls to Mocks</h2>
<h3 id="51-mockito">5.1 Mockito</h3>
<p>모의 메서드 호출의 경우 <code>Mockito.when(mock.method(args)).thenReturn(value)</code>을 사용할 수 있습니다. 여기서는 더 많은 매개변수로 추가하기만 하면 둘 이상의 호출에 대해 서로 다른 값을 반환할 수 있습니다: <code>thenReturn(value1, value2, value-n, …)</code>.</p>
<p>이 구문으로는 void 반환 메서드를 모의할 수 없다는 점에 유의하세요. 해당 경우 해당 방법의 확인을 사용합니다.</p>
<p>모의에 대한 호출을 확인하기 위해 <code>Mockito.verify(mock).method(args)</code>를 사용할 수 있으며 <code>verifyNoMoreInteractions(mock)</code>를 사용하여 모의에 더 이상 호출이 수행되지 않았는지 확인할 수도 있습니다.</p>
<p>인수를 확인하기 위해 특정 값을 전달하거나 <code>any()</code>, <code>anyString()</code> 및 <code>anyInt()</code>와 같은 사전 정의된 매처를 사용할 수 있습니다. 이러한 종류의 일치자가 훨씬 더 많으며 다음 예에서 볼 수 있는 일치자를 정의할 수도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertTwoMethodsHaveBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertOnlyOneMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"KO"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verifyNoMoreInteractions</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="52-easymock">5.2 EasyMock</h3>
<p>모의 메서드 호출에 <code>EasyMock.expect(mock.method(args)).andReturn(value)</code> 을 사용할 수 있습니다. 또한 모의 호출을 확인하기 위해 <code>EasyMock.verify(mock)</code>를 사용할 수 있습니다. 이를 위해서는 <code>EasyMock.replay(mock)</code>를 호출한 후 항상 호출해야 합니다. 또한 인수를 확인하기 위해 특정 값을 전달할 수 있습니다. 또는 <code>isA(Class.class)</code>, <code>anyString()</code>, <code>anyInt()</code>와 같은 사전 정의된 매처와 훨씬 더 많은 종류의 매처가 있고 다시 우리를 정의할 수 있는 가능성도 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertTwoMethodsHaveBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertOnlyOneMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token boolean">false</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"KO"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="53-jmockit">5.3 JMockit</h3>
<p>JMockit을 사용하여 기록, 재생 및 확인이라는 테스트 단계를 정의했습니다.</p>
<p>기록(record)은 새로운 <code>Expectations(){{}}</code> 블록(여러 모의 개체에 대한 작업을 정의할 수 있음)에서 수행되고, 재생(reply)은 테스트된 클래스의 메서드(모의 개체를 호출해야 함)를 호출하여 간단히 수행되며, 확인(verification)은 다음과 같습니다. 새로운 <code>Verifications(){{}}</code> 블록 내에서 수행됩니다(여러 모의에 대한 검증을 정의할 수 있음).</p>
<p>모의 메서드 호출의 경우 <code>Expectations</code> 블록 내부에서 <code>mock.method(args); result = value;</code>를 사용할 수 있습니다. 여기에서는 <code>result = value;</code>대신 <code>return(value1, value2, …, valuen);</code>을 사용하여 둘 이상의 호출에 대해 서로 다른 값을 반환할 수 있습니다.</p>
<p>모의 호출을 확인(verify)하기 위해 새로운  <code>Verifications(){{mock.call(value)}}</code> 또는 <code>new Verifications(mock){{}}</code>을 사용하여 이전에 정의된 모든 예상 호출을 확인할 수 있습니다.</p>
<p>args를 확인(verify)하기 위해 특정 값을 전달할 수 있습니다. 또는 <code>any</code>, <code>anyString</code>, <code>anyLong</code>과 같은 사전 정의된 값과 훨씬 더 많은 종류의 특수 값이 있고 다시 매처(Hamcrest 매처여야 함)를 정의할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertTwoMethodsHaveBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
        loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">assertOnlyOneMethodHasBeenCalled</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
        <span class="token comment">// no expectation for setCurrentUser</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"KO"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="6-mocking-exception-throwing">6. Mocking Exception Throwing</h2>
<h3 id="61-mockito">6.1 Mockito</h3>
<p>Mockito는 <code>Mockito.when(mock.method(args))</code> 다음에 <code>.thenThrow(ExceptionClass.class)</code>를 사용하여 예외를 발생시키는 모의 기능을 제공합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockExceptionThrowing</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenThrow</span><span class="token punctuation">(</span><span class="token class-name">IllegalArgumentException</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"ERROR"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verifyNoInteractions</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="62-easymock">6.2 EasyMock</h3>
<p><code>EasyMock.expect(…)</code> 호출 후에 <code>.andThrow(new ExceptionClass())</code>를 사용하여 예외 발생을 모의할 수 있습니다:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockExceptionThrowing</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andThrow</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"ERROR"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="63-jmockit">6.3 JMockit</h3>
<p>JMockito로 예외를 던지는 것은 특히 쉽습니다. "정상적인" 반환 대신에 모의 메서드 호출의 결과로 예외를 반환하면 됩니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockExceptionThrowing</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">IllegalArgumentException</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">// no expectation for setCurrentUser</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"ERROR"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="7--mocking-an-object-to-pass-around">7.  Mocking an Object to Pass Around</h2>
<h3 id="71-mockito">7.1 Mockito</h3>
<p>메서드 호출에 대한 인수로 전달하기 위해 모의 개체를 만들 수도 있습니다. Mockito를 사용하면 한 줄로 이를 수행할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockAnObjectToPassAround</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span><span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">getMock</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="72-easymock">7.2 EasyMock</h3>
<p><code>EasyMock.mock(Class.class)</code>를 사용하여 인라인 모의 객체를 만들 수 있습니다. 그 후, <code>EasyMock.expect(mock.method())</code>를 사용하여 실행을 준비할 수 있으며, 사용하기 전에 항상 <code>EasyMock.replay(mock)</code>를 호출하는 것을 기억하세요.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockAnObjectToPassAround</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">mock</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>userForm<span class="token punctuation">.</span><span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="73-jmockit">7.3 JMockit</h3>
<p>하나의 메소드에 대해서만 객체를 모의하려면 모의된 객체를 테스트 메소드에 매개변수로 전달하기만 하면 됩니다. 그런 다음 다른 모형과 마찬가지로 기대치를 생성할 수 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">mockAnObjectToPassAround</span><span class="token punctuation">(</span><span class="token annotation punctuation">@Mocked</span> <span class="token class-name">UserForm</span> userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        userForm<span class="token punctuation">.</span><span class="token function">getUsername</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
        loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
        loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
    
    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="8-custom-argument-matching">8. Custom Argument Matching</h2>
<h3 id="81-mockito">8.1 Mockito</h3>
<p>때때로 모의 호출에 대한 인수 일치는 고정 값이나 <code>anyString()</code>보다 조금 더 복잡해야 합니다. 이 경우 Mockito에는 <code>argThat(ArgumentMatcher&lt;&gt;)</code>와 함께 사용되는 matcher 클래스가 있습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">argumentMatching</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token comment">// default matcher</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">any</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// complex matcher</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token class-name">ArgumentMatchers</span><span class="token punctuation">.</span><span class="token function">argThat</span><span class="token punctuation">(</span>
        <span class="token keyword">new</span> <span class="token class-name">ArgumentMatcher</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">matches</span><span class="token punctuation">(</span><span class="token class-name">String</span> argument<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> argument<span class="token punctuation">.</span><span class="token function">startsWith</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="82-easymock">8.2 EasyMock</h3>
<p>사용자 정의 인수 일치는 실제 일치자를 생성한 다음 이를 <code>EasyMock.reportMatcher(IArgumentMatcher)</code>로 보고하는 정적 메서드를 생성해야 하기 때문에 EasyMock을 사용하면 조금 더 복잡합니다.</p>
<p>이 메서드가 생성되면 메서드 호출과 함께 모의 기대에 대해 이를 사용합니다(인라인 예제에서 볼 수 있음).</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">argumentMatching</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token comment">// default matcher</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">isA</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// complex matcher</span>
    loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token function">specificArgumentMatching</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>

<span class="token keyword">private</span> <span class="token keyword">static</span> <span class="token class-name">String</span> <span class="token function">specificArgumentMatching</span><span class="token punctuation">(</span><span class="token class-name">String</span> expected<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">reportMatcher</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">IArgumentMatcher</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">matches</span><span class="token punctuation">(</span><span class="token class-name">Object</span> argument<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> argument <span class="token keyword">instanceof</span> <span class="token class-name">String</span> 
              <span class="token operator">&amp;&amp;</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">)</span> argument<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">startsWith</span><span class="token punctuation">(</span>expected<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">appendTo</span><span class="token punctuation">(</span><span class="token class-name">StringBuffer</span> buffer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token comment">//NOOP</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">return</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="83-jmockit">8.3 JMockit</h3>
<p>특별한 <code>with(Delegate)</code> 메소드를 사용하여 JMockit과 일치하는 사용자 정의 인수를 생성할 수 있습니다:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">argumentMatching</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token comment">// default matcher</span>
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        loginService<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">UserForm</span><span class="token punctuation">)</span> any<span class="token punctuation">)</span><span class="token punctuation">;</span>
        result <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span>
        <span class="token comment">// complex matcher</span>
        loginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token keyword">with</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Delegate</span><span class="token generics"><span class="token punctuation">&lt;</span><span class="token class-name">String</span><span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token annotation punctuation">@Override</span>
            <span class="token keyword">public</span> <span class="token keyword">boolean</span> <span class="token function">matches</span><span class="token punctuation">(</span><span class="token class-name">Object</span> item<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> item <span class="token keyword">instanceof</span> <span class="token class-name">String</span> <span class="token operator">&amp;&amp;</span> <span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token class-name">String</span><span class="token punctuation">)</span> item<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">startsWith</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">new</span> <span class="token class-name">FullVerifications</span><span class="token punctuation">(</span>loginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h2 id="9-partial-mocking">9. Partial Mocking</h2>
<h3 id="91-mockito">9.1 Mockito</h3>
<p>Mockito는 두 가지 방법으로 부분 모의(일부 메서드에서 모의 메서드 호출 대신 실제 구현을 사용하는 모의)를 허용합니다.</p>
<p>일반적인 모의 메서드 호출 정의에서 <code>.thenCallRealMethod()</code>를 사용할 수도 있고, 모의 대신 스파이를 생성할 수도 있습니다. 이 경우 기본 동작은 모의되지 않은 모든 메서드에서 실제 구현을 호출하는 것입니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">partialMocking</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// use partial mock</span>
    loginController<span class="token punctuation">.</span>loginService <span class="token operator">=</span> spiedLoginService<span class="token punctuation">;</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token comment">// let service's login use implementation so let's mock DAO call</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// verify mocked call</span>
    <span class="token class-name">Mockito</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>spiedLoginService<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="92-easymock">9.2 EasyMock</h3>
<p>모의를 생성할 때 어떤 메서드를 조롱할지 정의해야 하기 때문에 부분적 조롱도 EasyMock을 사용하면 좀 더 복잡해집니다.</p>
<p><code>EasyMock.partialMockBuilder(Class.class).addMockedMethod(“methodName”).createMock()</code>을 사용하여 이 작업을 수행할 수 있습니다. 그 후, 우리는 모의를 다른 비부분 모의로 사용할 수 있습니다:</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">partialMocking</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
    <span class="token comment">// use partial mock</span>
    <span class="token class-name">LoginService</span> loginServicePartial <span class="token operator">=</span> <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">partialMockBuilder</span><span class="token punctuation">(</span><span class="token class-name">LoginService</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
      <span class="token punctuation">.</span><span class="token function">addMockedMethod</span><span class="token punctuation">(</span><span class="token string">"setCurrentUser"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">createMock</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginServicePartial<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// let service's login use implementation so let's mock DAO call</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">andReturn</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    loginServicePartial<span class="token punctuation">.</span><span class="token function">setLoginDao</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginController<span class="token punctuation">.</span>loginService <span class="token operator">=</span> loginServicePartial<span class="token punctuation">;</span>
    
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">replay</span><span class="token punctuation">(</span>loginServicePartial<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// verify mocked call</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginServicePartial<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token class-name">EasyMock</span><span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<h3 id="93-jmockit">9.3 JMockit</h3>
<p>JMockit을 사용한 부분 mocking은 특히 쉽습니다. <code>Expectations(){{}}</code>에 정의된 모의 동작이 없는 모든 메서드 호출은 "실제" 구현을 사용합니다.</p>
<p>이제 <code>login()</code> 메서드의 실제 구현을 사용하는 동안 <code>setCurrentUser()</code> 메서드를 모의하기 위해 <code>LoginService</code> 클래스를 부분적으로 모의하고 싶다고 가정해 보겠습니다.</p>
<p>이를 위해 먼저 <code>LoginService</code> 인스턴스를 생성하여 기대 블록에 전달합니다. 그런 다음 <code>setCurrentUser()</code> 메서드에 대한 기대값만 기록합니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">partialMocking</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token class-name">LoginService</span> partialLoginService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">LoginService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    partialLoginService<span class="token punctuation">.</span><span class="token function">setLoginDao</span><span class="token punctuation">(</span>loginDao<span class="token punctuation">)</span><span class="token punctuation">;</span>
    loginController<span class="token punctuation">.</span>loginService <span class="token operator">=</span> partialLoginService<span class="token punctuation">;</span>

    <span class="token class-name">UserForm</span> userForm <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserForm</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    userForm<span class="token punctuation">.</span>username <span class="token operator">=</span> <span class="token string">"foo"</span><span class="token punctuation">;</span>
        
    <span class="token keyword">new</span> <span class="token class-name">Expectations</span><span class="token punctuation">(</span>partialLoginService<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        <span class="token comment">// let's mock DAO call</span>
        loginDao<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span> result <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span>
            
        <span class="token comment">// no expectation for login method so that real implementation is used</span>
            
        <span class="token comment">// mock setCurrentUser call</span>
        partialLoginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>

    <span class="token class-name">String</span> login <span class="token operator">=</span> loginController<span class="token punctuation">.</span><span class="token function">login</span><span class="token punctuation">(</span>userForm<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token class-name">Assert</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span><span class="token string">"OK"</span><span class="token punctuation">,</span> login<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// verify mocked call</span>
    <span class="token keyword">new</span> <span class="token class-name">Verifications</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">{</span>
        partialLoginService<span class="token punctuation">.</span><span class="token function">setCurrentUser</span><span class="token punctuation">(</span><span class="token string">"foo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">}</span><span class="token punctuation">;</span>     
<span class="token punctuation">}</span></code></pre>