<h2 id="1-introduction">1. Introduction</h2>
<p>우리는 일종의 웹 요청을 수행하는 애플리케이션을 자주 사용합니다. 이 동작을 테스트할 때 Spring 앱에는 몇 가지 옵션이 있습니다.</p>
<p>이 빠른 튜토리얼에서는 <code>RestTemplate</code>을 통해서만 수행되는 호출을 mocking하는 몇 가지 방법을 살펴보겠습니다.</p>
<p>인기 있는 모의 라이브러리인 Mockito로 테스트부터 시작하겠습니다. 그런 다음 서버 상호 작용을 정의하기 위해 모의 서버를 생성하는 메커니즘을 제공하는 Spring Test를 사용합니다.</p>
<h2 id="using-mockito">Using <code>Mockito</code></h2>
<p>Mockito를 사용하여 <code>RestTemplate</code>을 완전히 모의할 수 있습니다. 이 접근 방식을 사용하면 서비스 테스트가 모의 작업과 관련된 다른 테스트만큼 간단해집니다.</p>
<p>HTTP를 통해 직원 세부 정보를 가져오는 간단한 <code>EmployeeService</code> 클래스가 있다고 가정해 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Service</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EmployeeService</span> <span class="token punctuation">{</span>
    
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">;</span>

    <span class="token keyword">public</span> <span class="token class-name">Employee</span> <span class="token function">getEmployee</span><span class="token punctuation">(</span><span class="token class-name">String</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token class-name">ResponseEntity</span> resp <span class="token operator">=</span> 
          restTemplate<span class="token punctuation">.</span><span class="token function">getForEntity</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080/employee/"</span> <span class="token operator">+</span> id<span class="token punctuation">,</span> <span class="token class-name">Employee</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        
	<span class="token keyword">return</span> resp<span class="token punctuation">.</span><span class="token function">getStatusCode</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">==</span> <span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>OK <span class="token operator">?</span> resp<span class="token punctuation">.</span><span class="token function">getBody</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">:</span> <span class="token keyword">null</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이제 이전 코드에 대한 테스트를 구현해 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">MockitoExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EmployeeServiceTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Mock</span>
    <span class="token keyword">private</span> <span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@InjectMocks</span>
    <span class="token keyword">private</span> <span class="token class-name">EmployeeService</span> empService <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">EmployeeService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@Test</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenMockingIsDoneByMockito_whenGetIsCalled_shouldReturnMockedObject</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token class-name">Employee</span> emp <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Employee</span><span class="token punctuation">(</span>“E001”<span class="token punctuation">,</span> <span class="token string">"Eric Simmons"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Mockito</span>
          <span class="token punctuation">.</span><span class="token function">when</span><span class="token punctuation">(</span>restTemplate<span class="token punctuation">.</span><span class="token function">getForEntity</span><span class="token punctuation">(</span>
            <span class="token string">"http://localhost:8080/employee/E001"</span><span class="token punctuation">,</span> <span class="token class-name">Employee</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">thenReturn</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">ResponseEntity</span><span class="token punctuation">(</span>emp<span class="token punctuation">,</span> <span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>OK<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token class-name">Employee</span> employee <span class="token operator">=</span> empService<span class="token punctuation">.</span><span class="token function">getEmployee</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Assertions</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span>emp<span class="token punctuation">,</span> employee<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>위의 JUnit 테스트 클래스에서는 먼저 Mockito에게 <code>@Mock</code> 어노테이션을 사용하여 더미 <code>RestTemplate</code> 인스턴스를 생성하도록 요청했습니다.</p>
<p>그런 다음 <code>EmployeeService</code> 인스턴스에 <code>@InjectMocks</code> 어노테이션을 달아 더미 인스턴스를 주입했습니다.</p>
<p>마지막으로 테스트 메서드에서 Mockito의 when/then 지원을 사용하여 모의 동작을 정의했습니다.</p>
<h2 id="3-using-spring-test">3. Using Spring Test</h2>
<p>Spring 테스트 모듈에는 <code>MockRestServiceServer</code>라는 모의 서버가 포함되어 있습니다. 이 접근 방식을 사용하면 RestTemplate 인스턴스를 통해 특정 요청이 전달될 때 특정 객체를 반환하도록 서버를 구성합니다. 또한 모든 기대 사항이 충족되었는지 여부를 해당 서버 인스턴스에서 <code>verify()</code>할 수 있습니다.</p>
<p><code>MockRestServiceServer</code>는 실제로 <code>MockClientHttpRequestFactory</code>를 사용하여 HTTP API 호출을 가로채는 방식으로 작동합니다. 구성에 따라 예상 요청 및 해당 응답 목록을 생성합니다. <code>RestTemplate</code> 인스턴스가 API를 호출하면 예상 목록에서 요청을 조회하고 해당 응답을 반환합니다.</p>
<p>따라서 모의 응답을 보내기 위해 다른 포트에서 HTTP 서버를 실행할 필요가 없습니다.</p>
<p><code>MockRestServiceServer</code>를 사용하여 동일한 <code>getEmployee()</code> 예제에 대한 간단한 테스트를 만들어 보겠습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@ExtendWith</span><span class="token punctuation">(</span><span class="token class-name">SpringExtension</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token annotation punctuation">@SpringBootTest</span><span class="token punctuation">(</span>classes <span class="token operator">=</span> <span class="token class-name">SpringTestConfig</span><span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">EmployeeServiceMockRestServiceServerUnitTest</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">EmployeeService</span> empService<span class="token punctuation">;</span>
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> <span class="token class-name">RestTemplate</span> restTemplate<span class="token punctuation">;</span>

    <span class="token keyword">private</span> <span class="token class-name">MockRestServiceServer</span> mockServer<span class="token punctuation">;</span>
    <span class="token keyword">private</span> <span class="token class-name">ObjectMapper</span> mapper <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">ObjectMapper</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token annotation punctuation">@BeforeEach</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">init</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        mockServer <span class="token operator">=</span> <span class="token class-name">MockRestServiceServer</span><span class="token punctuation">.</span><span class="token function">createServer</span><span class="token punctuation">(</span>restTemplate<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    
    <span class="token annotation punctuation">@Test</span>                                                                                          
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">givenMockingIsDoneByMockRestServiceServer_whenGetIsCalled_thenReturnsMockedObject</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>   
        <span class="token class-name">Employee</span> emp <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Employee</span><span class="token punctuation">(</span><span class="token string">"E001"</span><span class="token punctuation">,</span> <span class="token string">"Eric Simmons"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        mockServer<span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span><span class="token class-name">ExpectedCount</span><span class="token punctuation">.</span><span class="token function">once</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> 
          <span class="token function">requestTo</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token function">URI</span><span class="token punctuation">(</span><span class="token string">"http://localhost:8080/employee/E001"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">andExpect</span><span class="token punctuation">(</span><span class="token function">method</span><span class="token punctuation">(</span><span class="token class-name">HttpMethod</span><span class="token punctuation">.</span>GET<span class="token punctuation">)</span><span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">andRespond</span><span class="token punctuation">(</span><span class="token function">withStatus</span><span class="token punctuation">(</span><span class="token class-name">HttpStatus</span><span class="token punctuation">.</span>OK<span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">contentType</span><span class="token punctuation">(</span><span class="token class-name">MediaType</span><span class="token punctuation">.</span>APPLICATION_JSON<span class="token punctuation">)</span>
          <span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span>mapper<span class="token punctuation">.</span><span class="token function">writeValueAsString</span><span class="token punctuation">(</span>emp<span class="token punctuation">)</span><span class="token punctuation">)</span>
        <span class="token punctuation">)</span><span class="token punctuation">;</span>                                   
                       
        <span class="token class-name">Employee</span> employee <span class="token operator">=</span> empService<span class="token punctuation">.</span><span class="token function">getEmployee</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
        mockServer<span class="token punctuation">.</span><span class="token function">verify</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token class-name">Assertions</span><span class="token punctuation">.</span><span class="token function">assertEquals</span><span class="token punctuation">(</span>emp<span class="token punctuation">,</span> employee<span class="token punctuation">)</span><span class="token punctuation">;</span>                                                        
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span></code></pre>
<p>이전 코드 조각에서는 <code>MockRestRequestMatchers</code> 및 <code>MockRestResponseCreators</code>의 정적 메서드를 사용하여 REST 호출에 대한 기대와 응답을 명확하고 읽기 쉬운 방식으로 정의했습니다.</p>
<pre><code class="language-java"><span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client<span class="token punctuation">.</span>match</span><span class="token punctuation">.</span><span class="token class-name">MockRestRequestMatchers</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span>      
<span class="token keyword">import</span> <span class="token keyword">static</span> <span class="token namespace">org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>test<span class="token punctuation">.</span>web<span class="token punctuation">.</span>client<span class="token punctuation">.</span>response</span><span class="token punctuation">.</span><span class="token class-name">MockRestResponseCreators</span><span class="token punctuation">.</span>*<span class="token punctuation">;</span></code></pre>
<p>테스트 클래스의 <code>RestTemplate</code>은 <code>EmployeeService</code> 클래스에서 사용된 인스턴스와 동일해야 한다는 점을 명심해야 합니다. 이를 보장하기 위해 Spring 구성에서 RestTemplate 빈을 정의하고 테스트와 구현 모두에서 인스턴스를 자동 연결했습니다.</p>
<pre><code class="language-java"><span class="token annotation punctuation">@Bean</span>
<span class="token keyword">public</span> <span class="token class-name">RestTemplate</span> <span class="token function">restTemplate</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token keyword">new</span> <span class="token class-name">RestTemplate</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span></code></pre>
<p><code>MockRestServiceServer</code>를 사용하는 것은 통합 테스트를 작성할 때 매우 유용하며 외부 HTTP 호출만 모의해야 합니다.</p>